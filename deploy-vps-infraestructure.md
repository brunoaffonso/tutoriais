# Pipeline DevOps em uma VPS (Ubuntu 24.04) — k3s + GitLab Runner + Build local + Registry + Helm Deploy

Este tutorial monta uma esteira **CI/CD dentro da própria VPS**, semelhante ao cenário corporativo (sem Jenkins), usando:

- **GitLab.com**: repositório + pipelines
- **GitLab Runner na VPS**: executa o pipeline
- **Docker na VPS**: faz *build* da imagem **na VPS**
- **GitLab Container Registry**: armazena imagens (mais simples do que Harbor para começar)
- **k3s**: Kubernetes leve (single-node)
- **Helm**: deploy/upgrade/rollback no Kubernetes

> **Aviso de recursos (1 vCPU / 1 GB RAM):** funciona como laboratório, mas builds podem ser lentos e eventualmente falhar por memória. Este guia inclui **swap** para reduzir travamentos. Se ficar apertado, aumente RAM para 2–4 GB.

---

## 0) Pré-requisitos

### 0.1 Dados que você precisa ter em mãos
- IP público da VPS
- Acesso SSH com um usuário com `sudo`
- Conta no GitLab.com e um projeto com **Container Registry habilitado** (Settings → General → Visibility, project features… ou via UI do projeto)

### 0.2 (Opcional, mas recomendado) Domínio apontando para a VPS
Crie um DNS tipo:
- `app.seudominio.com -> IP_DA_VPS`

Você consegue seguir sem domínio (acessando por IP), mas HTTPS com Let’s Encrypt fica mais simples com domínio.

---

## 1) Preparar a VPS (swap, updates, firewall)

### 1.1 Atualizar o sistema e instalar utilitários
```bash
sudo apt update && sudo apt -y upgrade
sudo apt -y install curl git ufw ca-certificates apt-transport-https gnupg lsb-release
```

### 1.2 Criar swap (fortemente recomendado com 1 GB RAM)
Swap reduz risco de OOM (processo morto por falta de memória).
```bash
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
free -h
```

### 1.3 Configurar firewall (mínimo)
- SSH: 22
- HTTP: 80
- HTTPS: 443
```bash
sudo ufw allow OpenSSH
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw --force enable
sudo ufw status
```

---

## 2) Instalar Docker Engine (para build na VPS)

### 2.1 Adicionar repositório oficial do Docker
```bash
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo $VERSION_CODENAME) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
```

### 2.2 Instalar Docker
```bash
sudo apt -y install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo systemctl enable --now docker
docker --version
```

### 2.3 (Opcional) Permitir uso do docker sem sudo
> Para comandos manuais. No GitLab Runner, vamos tratar separadamente.
```bash
sudo usermod -aG docker $USER
# Refaça login SSH depois disso para aplicar o grupo
```

---

## 3) Instalar k3s (Kubernetes leve)

### 3.1 Instalar k3s
```bash
curl -sfL https://get.k3s.io | sudo sh -
```

### 3.2 Validar cluster
```bash
sudo kubectl get nodes
sudo kubectl get pods -A
```

> O k3s geralmente instala o **Traefik** como Ingress padrão.

### 3.3 (Recomendado) Copiar kubeconfig para seu usuário
Assim você usa `kubectl` sem `sudo`.
```bash
mkdir -p ~/.kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $(id -u):$(id -g) ~/.kube/config
chmod 600 ~/.kube/config

kubectl get nodes
```

---

## 4) Instalar Helm

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
helm version
```

---

## 5) Criar namespace e RBAC para deploy (boa prática)

Vamos criar um namespace `apps` e uma conta `deployer` com permissões apenas nesse namespace.

### 5.1 Namespace
```bash
kubectl create namespace apps
```

### 5.2 ServiceAccount + permissões (Role/RoleBinding)
Crie o arquivo `rbac-deployer.yaml`:
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: deployer
  namespace: apps
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: deployer-role
  namespace: apps
rules:
  - apiGroups: ["", "apps", "networking.k8s.io"]
    resources:
      ["deployments","replicasets","pods","services","configmaps","secrets","ingresses"]
    verbs: ["get","list","watch","create","update","patch","delete"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: deployer-rb
  namespace: apps
subjects:
  - kind: ServiceAccount
    name: deployer
    namespace: apps
roleRef:
  kind: Role
  name: deployer-role
  apiGroup: rbac.authorization.k8s.io
```

Aplicar:
```bash
kubectl apply -f rbac-deployer.yaml
```

---

## 6) Instalar GitLab Runner na VPS (para build + deploy)

### 6.1 Instalar runner
```bash
curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh | sudo bash
sudo apt -y install gitlab-runner
sudo gitlab-runner --version
sudo systemctl enable --now gitlab-runner
```

### 6.2 Registrar o runner no seu projeto GitLab
No GitLab:
- Projeto → **Settings → CI/CD → Runners**
- Pegue o **registration token** (ou “new runner token”, dependendo do modo)

No servidor:
```bash
sudo gitlab-runner register
```

Sugestões ao responder o assistente do registro:
- **GitLab instance URL:** `https://gitlab.com/`
- **Token:** (cole o token)
- **Description:** `vps-runner`
- **Tags:** `vps`
- **Executor:** `docker`
- **Default Docker image:** `docker:27` (ou `docker:stable`)

### 6.3 Permitir o runner usar Docker do host (recomendado para VPS pequena)
Esse modo evita Docker-in-Docker e economiza recursos.

Edite o arquivo:
```bash
sudo nano /etc/gitlab-runner/config.toml
```

No runner registrado, ajuste o bloco do Docker para ter algo assim (exemplo):
```toml
[[runners]]
  name = "vps-runner"
  url = "https://gitlab.com/"
  token = "SEU_TOKEN_AQUI"
  executor = "docker"

  [runners.docker]
    image = "docker:27"
    privileged = false
    volumes = ["/var/run/docker.sock:/var/run/docker.sock", "/cache"]
```

Reinicie:
```bash
sudo systemctl restart gitlab-runner
```

> **Nota:** Com o socket montado, os comandos `docker build` dentro do job usam o Docker do host.

---

## 7) Preparar o projeto: Dockerfile + Helm Chart

### 7.1 Estrutura sugerida
No seu repositório:
```
.
├─ Dockerfile
├─ src/...
└─ deploy/
   └─ chart/   (Helm chart)
```

### 7.2 Criar um Helm chart base
No root do repo:
```bash
mkdir -p deploy
cd deploy
helm create chart
```

Isso cria templates padrão. Você vai customizar principalmente:
- `deploy/chart/values.yaml`
- `deploy/chart/templates/deployment.yaml`
- `deploy/chart/templates/ingress.yaml`

### 7.3 Ajustar valores de imagem (para tag do CI)
Edite `deploy/chart/values.yaml` e deixe algo assim (trecho):
```yaml
image:
  repository: ""
  tag: ""
  pullPolicy: IfNotPresent
```

No `deploy/chart/templates/deployment.yaml`, garanta que a imagem use esses valores:
```yaml
image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
imagePullPolicy: {{ .Values.image.pullPolicy }}
```

> O `helm create` já vem quase assim; só confirme.

### 7.4 Ingress (opcional)
No `values.yaml`:
```yaml
ingress:
  enabled: true
  className: ""
  annotations: {}
  hosts:
    - host: app.seudominio.com
      paths:
        - path: /
          pathType: Prefix
  tls: []
```

E habilite no template `templates/ingress.yaml` conforme o chart padrão.

---

## 8) CI/CD no GitLab: build na VPS + push no GitLab Registry + deploy com Helm

### 8.1 Variáveis necessárias no GitLab
No GitLab: Projeto → Settings → CI/CD → Variables

Crie uma variável **KUBECONFIG_B64** com o kubeconfig do deployer.

#### Gerar kubeconfig limitado (deployer)
No servidor, gere token e kubeconfig:

1) Gere um token:
```bash
DEPLOY_TOKEN=$(kubectl -n apps create token deployer)
echo "$DEPLOY_TOKEN"
```

2) Pegue o CA do cluster:
```bash
CA_DATA=$(kubectl config view --raw -o jsonpath='{.clusters[0].cluster.certificate-authority-data}')
echo "$CA_DATA"
```

3) Endpoint do API server do k3s (local na VPS):
```bash
APISERVER="https://127.0.0.1:6443"
echo "$APISERVER"
```

4) Crie um arquivo `kubeconfig-deployer.yaml`:
```bash
cat > kubeconfig-deployer.yaml <<EOF
apiVersion: v1
kind: Config
clusters:
- name: k3s
  cluster:
    server: ${APISERVER}
    certificate-authority-data: ${CA_DATA}
contexts:
- name: deployer@k3s
  context:
    cluster: k3s
    namespace: apps
    user: deployer
current-context: deployer@k3s
users:
- name: deployer
  user:
    token: ${DEPLOY_TOKEN}
EOF
```

5) Converter para base64 (uma linha) para colocar no GitLab:
```bash
base64 -w 0 kubeconfig-deployer.yaml
echo
```

Copie o resultado e cole na variável **KUBECONFIG_B64**.
- Marque como **Protected** se você usar branches protegidas
- Marque como **Masked** (se possível)

### 8.2 Criar `.gitlab-ci.yml` no repositório
Exemplo mínimo (adaptar nomes do chart e domínio):

```yaml
stages: [test, build, deploy]

variables:
  IMAGE_TAG: "$CI_COMMIT_SHORT_SHA"
  IMAGE: "$CI_REGISTRY_IMAGE:$IMAGE_TAG"

test:
  stage: test
  image: alpine:3.20
  script:
    - echo "Coloque seus testes aqui (pytest, npm test, etc.)"
    - echo "OK"

build_image:
  stage: build
  image: docker:27
  tags: [vps]
  script:
    - echo "$CI_REGISTRY_PASSWORD" | docker login -u "$CI_REGISTRY_USER" --password-stdin "$CI_REGISTRY"
    - docker build -t "$IMAGE" .
    - docker push "$IMAGE"
  only:
    - branches

deploy_k3s:
  stage: deploy
  image: alpine:3.20
  tags: [vps]
  before_script:
    - apk add --no-cache curl bash ca-certificates
    # instala helm
    - curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
    # instala kubectl
    - curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    - install -m 0755 kubectl /usr/local/bin/kubectl
    # kubeconfig
    - mkdir -p ~/.kube
    - echo "$KUBECONFIG_B64" | base64 -d > ~/.kube/config
    - chmod 600 ~/.kube/config
  script:
    - kubectl get ns
    - helm upgrade --install minha-app ./deploy/chart         -n apps --create-namespace         --set image.repository="$CI_REGISTRY_IMAGE"         --set image.tag="$IMAGE_TAG"
    - kubectl -n apps rollout status deploy/minha-app
  when: manual
  only:
    - branches
```

**O que esse pipeline faz:**
- `build_image`: builda a imagem **na VPS** e faz push para o **GitLab Registry**
- `deploy_k3s`: decodifica kubeconfig, roda Helm e faz rollout no k3s
- Deploy está como **manual**, para imitar o “clique no Jenkins”

---

## 9) Teste de ponta a ponta

### 9.1 Rodar pipeline
1) Faça commit do `.gitlab-ci.yml`, Dockerfile e chart.
2) Faça push.
3) No GitLab: CI/CD → Pipelines
4) Verifique build e push da imagem.
5) Clique no job **deploy_k3s** (manual) e acompanhe.

### 9.2 Verificar no cluster
Na VPS:
```bash
kubectl -n apps get all
kubectl -n apps get ingress
kubectl -n apps logs deploy/minha-app --tail=100
```

---

## 10) Próximos upgrades (quando você aumentar recursos)
Quando você tiver 2–4 GB RAM, a evolução natural é:
- **cert-manager + Let's Encrypt** para HTTPS automático
- **Rancher** (painel de gerenciamento) — tende a precisar mais RAM
- **Harbor** (registry enterprise) — também pesado
- **Vault/External Secrets** (segredos corporativos)

---

## Checklist final
- [ ] Swap configurado
- [ ] Docker instalado e rodando
- [ ] k3s instalado e acessível via kubectl
- [ ] Helm instalado
- [ ] Namespace `apps` criado
- [ ] ServiceAccount `deployer` e RBAC aplicados
- [ ] GitLab Runner registrado com executor Docker
- [ ] Runner configurado com `/var/run/docker.sock`
- [ ] Container Registry habilitado no GitLab
- [ ] `KUBECONFIG_B64` criado nas variables do projeto
- [ ] `.gitlab-ci.yml` com build+push+deploy via Helm
