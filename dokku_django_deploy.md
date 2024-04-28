# Deploy de aplicações Django utilizando o Dokku

Este é um passo a passo de como fazeo deploy de uma aplicação Django no Dokku.

## Configurações iniciais no Servidor

1.Atualização de pacotes

```
sudo apt update && sudo apt upgrade -y
```

2.Gerar chave SSH

```
ssh-keygen -t rsa
```

3.Copiar todo o conteúdo da chave pública para o arquivo `authorized_key`

```
cat ~/.ssh/id_rsa.pub
vi ~/.ssh/authorized_keys
```

4.Fazer o mesmo no usuário `root` (diretório: `/root.ssh/`)

5.Ajustar todas as permissões das chaves para 600:

```
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/id_rsa
```

6.Fazer download do Dokku (conferir versão mais nova) :

```
wget -NP . https://dokku.com/install/v0.34.4/bootstrap.sh
```

7.Instalar o Dokku:

```
sudo DOKKU_TAG=v0.34.4 bash bootstrap.sh
```

8.Conferir a versão instalada

```
dokku version
```

9.Configurar uma chave SSH:

```
cat ~/.ssh/authorized_keys | sudo dokku ssh-keys:add admin
```

10.Conferir as chaves configuradas no servidor:

```
sudo dokku ssh-keys:list
```

11.Instalar plugin do Postgres:

```
sudo dokku plugin:install https://github.com/dokku/dokku-postgres.git
```

12.Instalar plugin do Lets encrypt:

```
sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git
```

## Configurações no Computador Local

1.Copiar conteúdo da chave privada do servidor:

No servidor:

```
cat ~/.ssh/id_rsa
```

No computador local:

```
vim ~/.ssh/authorized_keys
```

2.Mudar as permissões:

```
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/id_rsa
```

3.Configuração das chaves:

```
ssh-add -l
```

Se o agente não estiver iniciado, inicie com:

```
eval "$(ssh-agent -s)"
```

Se nenhuma chave estivar carregada, adicione-a com:

```
ssh-add ~/.ssh/your_private_key
```

4.Teste o acesso SSH:

```
ssh -o StrictHostKeyChecking=no <nome_do_usuario_na_chave>@<endereco_ip_servidor> whoami
```

Output: `<nome_do_usuario_na_chave>`

**Em resumo, a chave pública do computador local deve estar em authorized_keys do servidor. No fim da chave há o nome do usuário. Na máquina local, deve ser configurado o ssh-agent com o comando `ssh-add ~/.ssh/chave_privada` do passo anterior. Esta chave privada é a mesma que foi gerada com a chave pública.**

## Criar aplicação no Dokku

1.Criar aplicação:

```
dokku apps:create <nome_app>
```

Conferir se a criação foi bem sucedida:

```
dokku apps:list
```

2.Configuração de domínio:

Para configurar um domínio local:

```
dokku domains:set-global <dominio.com>
```

Para configurar um dominio para a aplicação:

```
dokku domains:add <nome_app> <ip_servidor_ou_nome_completo_dominio>
```

Conferir a configuração no NGINX:

```
dokku nginx:show-config <my_app>
```

Conferir a configuração de domínio da aplicação:

```
dokku domains:report <my_app>
```

3.Criar chave para SSL do domínio da aplicação:

```
dokku letsencrypt:enable <my_app>
```

4.Criar um banco de dados Postgres para a aplicação:

```
dokku postgres:create <mydb>
```

Conferir se o container do banco de dados foi iniciado:

```
sudo docker ps
```

5.Fazer link do banco de dados com a aplicação:

```
dokku postgres:link <mydb> <nome_app>
```

## Configuração em uma aplicação Django

1.Adicionar ao requirements.txt:

- python-decouple
- dj-database-url
- gunicorn
- psycopg2-binary
- django-storages\*

**\*Adicionado para aplicação funcionar com arquivos estáticos na aws ou outro serviço. Ajustar conforme necessidade da aplicação**

2.Configurar variáveis de ambiente (`.env`):

```
ALLOWED_HOSTS=*
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_STORAGE_BUCKET_NAME=
DATABASE_URL=
DEBUG=False
DJANGO_SETTINGS_MODULE=<app_name>.settings
DJANGO_SUPERUSER_EMAIL=
DJANGO_SUPERUSER_PASSWORD=
DJANGO_SUPERUSER_USERNAME=
SECRET_KEY=
USE_AWS=False
```

---

**Configurar variável de ambiente da aplicação no SERVIDOR**:

```
dokku config:set --no-restart <my_app> <VARIAVEL_AMBIENTE>=<VALOR_VARIAVEL>
```

---

3.Criar arquivo `Procfile`:

```
web: gunicorn <app_name>.wsgi --log-file -
```

4.Criar arquivo `runtime.txt`:

```
python-3.12.3
```

5.Configurações no arquivo `settings.py`:

```
from decouple import config, Csv
from dj_database_url import parse as dburl

SECRET_KEY = config('SECRET_KEY')

DEBUG = config('DEBUG', default=False, cast=bool)

ALLOWED_HOSTS = config('ALLOWED_HOSTS', default='*', cast=Csv())

default_dburl = 'sqlite:///' + str(BASE_DIR / 'db.sqlite3')
DATABASES = {
    'default': config('DATABASE_URL', default=default_dburl, cast=dburl),
}

if config('USE_AWS', default=False, cast=bool):
    AWS_ACCESS_KEY_ID = config('AWS_ACCESS_KEY_ID')
    AWS_SECRET_ACCESS_KEY = config('AWS_SECRET_ACCESS_KEY')
    AWS_STORAGE_BUCKET_NAME = config('AWS_STORAGE_BUCKET_NAME')
    AWS_S3_REGION_NAME = 'us-east-2'
    AWS_S3_CUSTOM_DOMAIN = f'{AWS_STORAGE_BUCKET_NAME}.s3.amazonaws.com'
    AWS_DEFAULT_ACL = 'public-read'
    AWS_QUERYSTRING_AUTH = False
    AWS_S3_OBJECT_PARAMETERS = {'CacheControl': 'max-age=86400'}
    MEDIA_URL = f'https://{AWS_S3_CUSTOM_DOMAIN}/'
    DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
    STATIC_URL = f'https://{AWS_S3_CUSTOM_DOMAIN}/static/'
    STATICFILES_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
else:
    STATIC_URL = 'static/'
    STATIC_ROOT = str(BASE_DIR / 'staticfiles')

    MEDIA_URL = '/media/'
    MEDIA_ROOT = str(BASE_DIR / 'media')
```

## Preparar projeto para enviar para o Dokku:

1.Git:

```
git add .
git commit -m "app prepared for deployment"
```

2.Configurar variável de ambiente da aplicação no SERVIDOR:

```
dokku config:set --no-restart <my_app> <VARIAVEL_AMBIENTE>=<VALOR_VARIAVEL>
```

Verificar variáveis de ambiente configuradas na aplicação:

```
dokku config:show <my_app>
```

3.Adicionar o repositório remoto do Dokku no git:

```
git remote add dokku dokku@<your_domain>:<my_app>
git checkuot -b main
git push dokku main
```

**Obs.: Ao configurar o git remote, após o @ deve conter seu domínio válido (aquele que bate no servidor) ou o endereço IP do servidor, pois o acesso será via SSH. Após o ":", utilizar o nome da aplicação como foi configurada no Dokku.**

4.Após o deploy, necessário dar os comandos para migração do banco de dados:

```
dokku run <my_app> python manage.py migrate
```

## Configurar storage da aplicação

1.Para persistir dados de um container da aplicação, primeiro precisamos criar um diretório de armazenamento. A documentação do Dokku recomenda utilizar `/var/lib/dokku/data/storage/<app_name>`:

```
mkdir /var/lib/dokku/data/storage/<my_app>/
chown -R dokku:dokku /var/lib/dokku/data/storage/<my_app>/
sudo chmod -R 777 /var/lib/dokku/data/storage/<my_app>/
```

2.Em seguida precisamos mapear/montar os diretórios do container para o diretório criado. O comando necessita de 2 argumentos:

- Nome da aplicação
- caminho-diretorio-servidor:caminho-diretorio-container

```
dokku storage:mount <my_app> /var/lib/dokku/data/storage/<my_app>/staticfiles:/app/staticfiles
dokku storage:mount <my_app> /var/lib/dokku/data/storage/<my_app>/media:/app/media
sudo chmod -R 777 /var/lib/dokku/data/storage/<my_app>/staticfiles/
sudo chmod -R 777 /var/lib/dokku/data/storage/<my_app>/media/
```

**Ajustar nomes conforme necessidade.**

3.Faça o redeploy para ter certeza que os arquivos serão coletados:

```
dokku ps:restart <my_app>
```

Liste os diretórios:

```
ls /var/lib/dokku/data/storage/<my_app>
```

Output: `media  staticfiles`

4.Configurar o django para servir os arquivos de media e staticfiles:

No fim do arquivo `urls.py`:

```
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
    urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

5.Para configurar o NGING para servir os arquivos nas pastas mapeadas, será necessário criar um diretório chamado `nginx.conf.d` dentro do diretório `/home/dokku/<my_app>/`:

```
mkdir -p /home/dokku/<my_app>/nginx.conf.d
chown dokku:dokku /home/dokku/<my_app>/nginx.conf.d/
```

6.Após, criar o arquivo `static.conf` dentro do diretório criado anteriormente:

```
vim /home/dokku/<my_app>/nginx.conf.d/static.conf
```

Colar o conteúdo dentro do arquivo `static.conf`:

```
location /static/ {
    alias /var/lib/dokku/data/storage/<my_app>/staticfiles/;
}

location /media/ {
    alias /var/lib/dokku/data/storage/<my_app>/media/;
}
```

Confirme as permissões do arquivo `static.conf`:

```
chown -R dokku:dokku /home/dokku/<my_app>/nginx.conf.d/static.conf
```

7.Reinicie a aplicação para que as configurações sejam aplicadas:

```
dokku ps:restart <my_app>
```

## Fazer backup do banco de dados na AWS

1.Instalar awscli:

```
sudo apt update
sudo apt install awscli
```

2.Configurar as credenciais da aws. Será solicitado:

- AWS Access Key ID
- AWS Secret Access Key
- Default region name
- Default output format (pode deixar em branco)

```
aws configure
```

3.Criar um script de backup. Criar o arquivo `backup_db.sh` e colocar o conteúdo abaixo:

```
#!/bin/bash

# Data para nomear o arquivo de backup
DATE=$(date +"%Y-%m-%d_%H-%M-%S")

# Caminho para salvar os backups
BACKUP_DIR="/var/backups/postgres"

# Nome do serviço do banco de dados
DB_SERVICE="manutsisdb"

# Caminho completo do arquivo de backup
BACKUP_FILE="${BACKUP_DIR}/backup_${DB_SERVICE}_${DATE}.dump"

# Criar diretório de backup se não existir
mkdir -p ${BACKUP_DIR}

# Executar o backup
dokku postgres:export ${DB_SERVICE} > ${BACKUP_FILE}

# Transferir para o S3
aws s3 cp ${BACKUP_FILE} s3://seu-bucket-s3/caminho/para/backup/

# Opcional: Limpar backups antigos localmente
find ${BACKUP_DIR} -type f -mtime +30 -name '*.dump' -delete
```

**Ajuste os nomes dos diretórios.**

4.Dê a permissão de execução do script:

```
chmod +x /home/dokku/scripts/backup_db.sh
```

5.Teste o script:

```
/home/dokku/scripts/backup_db.sh
```

6.Automatizar a execução do script com o `Cron`:

```
crontab -e
```

Adicionar a linha abaixo para fazer o backup todos os dias às 00:00. Ajuste conforme a necessidade:

```
0 0 * * * /home/dokku/scripts/backup_db.sh >> /var/log/backup_db.log 2>&1
```

7.ALtere as permissões de arquivos e diretórios:

```
sudo chmod -R 777 /var/backups/postgres
```

**Para evitar problemas de permissão, pode-se utilizar o `sudo` para executar o `crontab -e` para que seja executado pelo root.**

## Restaurar banco de dados

1.Importar arquivo dump para serviço de Banco de dados:

```
dokku postgres:import <servico_banco_dados> < <nome_do_arquivo>.dump
```

**Caso não seja possível a importação, pode ser necessário destruir o banco de dados da aplicação, criar um novo, e então, com o novo banco de dados vazio, fazer a importação.**

## Cheat sheet

- Adicionar variável de ambiente:

```
dokku config:set --no-restart <my_app> <VARIAVEL>=<Valor>
dokku config:set <my_app> USE_AWS=True
```

- Executar comando:

```
dokku run <my_app> python manage.py collectstatic --noinput
```

- Ver configurações do NGINX de uma aplicação:

```
dokku nginx:show-config <my_app>
```

- Ver configurações de domínio de uma aplicação:

```
dokku domains:report <my_app>
```

- Remover domínio de uma aplicação:

```
dokku domains:remove <my_app> <domain.com>
```

- Reiniciar uma aplicação:

```
dokku ps:restart <my_app>
```

- Rebuild de uma App:

```
dokku ps:rebuild <my_app>
```

- Acessar container

```
dokku enter <my_app> <tipo_aplicacao>
```

- Listar pontos de montagens de container

```
dokku storage:list <my_app>
```

- Montar um diretorio no host ao container

```
dokku storage:mount <my_app> /var/lib/dokku/data/storage/<my_app>/<nome_da_pasta>:/app/<nome_da_pasta>/
```

- Remover ponto de montagem

```
dokku storage:unmount <my_app> /var/lib/dokku/data/storage/<my_app>/<nome_da_pasta>:/app/<nome_da_pasta>
```

- Criar um novo serviço de banco de dados:

```
dokku postgres:create <nome_do_servico>
```

- Exportar banco de dados para um arquivo:

```
dokku postgres:export <nome_do_servico> > backup.dump
```

- Importar dados de um arquivo dump:

```
dokku postgres:import <nome_do_servico> < backup.dump
```

- Destruir um banco de dados (ATENÇÃO):

```
dokku postgres:destroy <nome_do_servico>
```

- Linkar banco de dados a uma aplicação Dokku:

```
dokku postgres:link <nome_do_servico> <nome_da_aplicacao>
```

- Desvincular banco de dados da aplicação

```
dokku postgres:unlink <nome_do_servico> <nome_da_aplicacao>
```

- Copiar arquivo para um bucket S3:

```
aws s3 cp <nome_do_arquivo> s3://seu-bucket-s3/caminho/
```

- Copiar um arquivo do bucket S3 para o servidor:

```
aws s3 cp s3://seu-bucket-s3/caminho/<nome_do_arquivo> <caminho_destino_do_arquivo>
```
