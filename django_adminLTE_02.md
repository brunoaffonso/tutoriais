# Tutorial: Integração do Django com AdminLTE 4 e Bootstrap 5 via npm

Este tutorial detalha o processo de criação de um projeto Django que utiliza o AdminLTE (versão 4) e o Bootstrap 5, utilizando o npm para gerenciar as dependências de frontend e compilar os arquivos SCSS. O fluxo descrito permite que os arquivos gerados sejam incorporados na pasta de arquivos estáticos do Django, prontos para serem servidos em produção.

---

## 1. Pré-requisitos

- **Python e Django:** Certifique-se de ter o Python e o Django instalados.
- **Node.js e npm:** Necessários para gerenciar as dependências de frontend.
- **Conhecimento básico de linha de comando:** Para executar os comandos e configurar os scripts.

---

## 2. Criação do Projeto Django

Inicie criando o seu projeto Django e um aplicativo (caso necessário):

```bash
django-admin startproject meu_projeto
cd meu_projeto
python manage.py startapp core

```

### Configuração dos Arquivos Estáticos

No arquivo `settings.py`, ajuste as variáveis para os arquivos estáticos:

```python
# settings.py

import os
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))

# Pasta onde os arquivos estáticos serão coletados no deploy
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

# Pastas adicionais de arquivos estáticos (útil em desenvolvimento)
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static'),
]

```

----------

## 3. Configuração do Ambiente npm no Projeto Django

Na raiz do projeto (mesmo nível do `manage.py`), inicialize um novo projeto npm:

```bash
npm init -y

```

Isso criará o arquivo `package.json` onde serão registradas as dependências e scripts.

----------

## 4. Instalação das Dependências via npm

Instale o Bootstrap 5, o AdminLTE 4 e as ferramentas para compilar SCSS (utilizando o Dart Sass e o pacote `copyfiles` para cópias de arquivos):

```bash
npm install bootstrap@5 admin-lte@4 sass copyfiles --save-dev

```

> **Observação:** Verifique a versão exata do AdminLTE desejado. Caso o pacote npm não esteja disponível ou a versão precise ser ajustada, consulte a documentação oficial do AdminLTE.

----------

## 5. Organização da Estrutura de Pastas

Sugere-se a seguinte estrutura de diretórios para organizar os arquivos de frontend:

```
meu_projeto/
├── assets/
│   └── scss/
│       └── main.scss      # Arquivo principal de SCSS
├── static/                # Arquivos gerados serão armazenados aqui
├── node_modules/
├── package.json
├── manage.py
└── ...

```

Os arquivos-fonte (como os SCSS e JS customizados) ficam na pasta `assets`, enquanto os arquivos compilados são enviados para a pasta `static`.

----------

## 6. Criação do Arquivo SCSS Principal

Crie o arquivo `assets/scss/main.scss` para importar o Bootstrap e o AdminLTE. Se desejar customizar variáveis, faça isso antes das importações:

```scss
// assets/scss/main.scss

// (Opcional) Sobrescreva variáveis do Bootstrap ou AdminLTE
// $primary: #ff6600;

// Importa o SCSS do Bootstrap
@import "bootstrap/scss/bootstrap";

// Importa o SCSS do AdminLTE
// Verifique o caminho correto dentro do node_modules; exemplo:
@import "../node_modules/admin-lte/build/scss/AdminLTE";

// Adicione outras importações conforme necessário

```

> **Dica:** Os caminhos de importação podem variar conforme a estrutura dos pacotes. Consulte a documentação do AdminLTE para confirmar os caminhos corretos.

----------

## 7. Configuração dos Scripts npm para Build

Edite o arquivo `package.json` para incluir os scripts que compilarão o SCSS e copiarão os arquivos estáticos do AdminLTE para a pasta `static`.

### Exemplo de Scripts

```json
{
  "name": "meu_projeto",
  "version": "1.0.0",
  "description": "Projeto Django com AdminLTE e Bootstrap 5",
  "scripts": {
    "build:css": "npx sass assets/scss/main.scss static/css/main.css --no-source-map --style=compressed",
    "copy:adminlte": "copyfiles -u 3 node_modules/admin-lte/{dist,plugins} static/adminlte",
    "build": "npm run build:css && npm run copy:adminlte",
    "watch:css": "npx sass assets/scss/main.scss static/css/main.css --watch"
  },
  "devDependencies": {
    "bootstrap": "^5.x.x",
    "admin-lte": "^4.x.x",
    "sass": "^1.x.x",
    "copyfiles": "^2.x.x"
  }
}

```

#### Explicação dos Scripts:

-   **build:css:** Compila o arquivo SCSS para CSS, minificando-o e sem gerar source maps (ideal para produção).
    
-   **copy:adminlte:** Copia os arquivos estáticos do AdminLTE (CSS, JS, plugins, etc.) da pasta `node_modules` para `static/adminlte`.  
    O parâmetro `-u 3` remove os três primeiros diretórios do caminho de origem; ajuste conforme a estrutura desejada.
    
-   **build:** Executa os scripts `build:css` e `copy:adminlte` em sequência.
    
-   **watch:css:** Ativa o modo "watch" do Sass para recompilar automaticamente o SCSS quando houver alterações.
    

----------

## 8. Executando o Build e Integrando no Django

### 8.1 Gerar os Arquivos Estáticos

Na raiz do projeto, execute:

```bash
npm run build

```

Este comando irá:

-   Compilar o SCSS de `assets/scss/main.scss` para `static/css/main.css`.
    
-   Copiar os arquivos estáticos do AdminLTE para `static/adminlte`.
    

### 8.2 Integração dos Arquivos no Template Django

No template base do Django (por exemplo, `templates/base.html`), inclua os links para os arquivos CSS e JS compilados:

```html
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title>Meu Projeto Django</title>
    {% load static %}
    <!-- CSS customizado compilado -->
    <link rel="stylesheet" href="{% static 'css/main.css' %}">
    <!-- CSS do AdminLTE (se não estiver incluso no main.css) -->
    <link rel="stylesheet" href="{% static 'adminlte/css/adminlte.min.css' %}">
</head>
<body>
    <!-- Conteúdo do site -->
    
    <!-- Scripts do AdminLTE e dependências -->
    <script src="{% static 'adminlte/js/adminlte.min.js' %}"></script>
</body>
</html>

```

Ajuste os caminhos conforme a estrutura gerada pelo comando de build.

### 8.3 Utilizando o Watch Durante o Desenvolvimento

Para recompilar automaticamente o SCSS durante o desenvolvimento, execute:

```bash
npm run watch:css

```

----------

## 9. Considerações Finais e Deploy

-   **Customização:** Personalize os arquivos SCSS sobrescrevendo variáveis do Bootstrap ou AdminLTE conforme necessário.
    
-   **Gerenciamento de Assets:** Em projetos maiores, considere o uso de um bundler (Webpack, Rollup, Parcel) para otimizar e organizar melhor os assets, inclusive JavaScript.
    
-   **Deploy:** No deploy, execute o comando de build e garanta que a pasta `static` esteja atualizada. Utilize também o comando `python manage.py collectstatic` para consolidar os arquivos estáticos para o servidor.
    
-   **Versionamento:** Mantenha o versionamento das dependências no `package.json` para evitar atualizações inesperadas que possam alterar o comportamento dos assets.
    

----------

## Conclusão

Este tutorial apresenta uma abordagem prática para integrar o Bootstrap 5 e o AdminLTE 4 em um projeto Django utilizando npm para a instalação, compilação (incluindo SCSS) e organização dos arquivos estáticos. Essa metodologia permite uma customização flexível e um fluxo de trabalho moderno, integrando tecnologias de frontend com o backend Django de forma eficiente e escalável.
