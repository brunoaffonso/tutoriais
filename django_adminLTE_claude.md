# Tutorial: Integrando AdminLTE 4 com Bootstrap 5 em um Projeto Django

## 1. Configuração Inicial do Projeto Django

Primeiro, vamos criar um ambiente virtual e instalar o Django:

```bash
# Criar e ativar ambiente virtual
python -m venv venv
source venv/bin/activate  # No Windows: venv\Scripts\activate

# Instalar Django
pip install django

# Criar um novo projeto Django
django-admin startproject meu_projeto
cd meu_projeto

# Criar um app (opcional, mas recomendado)
python manage.py startapp core

```

Agora, configure seu app no arquivo `settings.py`:

```python
# meu_projeto/settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'core',  # seu app
]

# Configurações de arquivos estáticos
STATIC_URL = '/static/'
STATIC_ROOT = BASE_DIR / 'staticfiles'
STATICFILES_DIRS = [
    BASE_DIR / 'static',
]

```

Crie as pastas necessárias para os arquivos estáticos:

```bash
mkdir -p static/css static/js static/scss

```

## 2. Configuração do npm e package.json

Inicialize o npm no seu projeto e crie o arquivo package.json:

```bash
# Inicializar npm (responda às perguntas conforme necessário)
npm init

```

## 3. Instalando Bootstrap 5 e AdminLTE 4

Adicione o Bootstrap 5 e o AdminLTE 4 como dependências:

```bash
# Instalar Bootstrap 5
npm install bootstrap@5

# Instalar AdminLTE 4
npm install admin-lte@4

# Instalar dependências para compilação de SCSS
npm install --save-dev sass
npm install --save-dev postcss autoprefixer
npm install --save-dev postcss-cli

```

## 4. Configuração dos Scripts de Build

Atualize o arquivo `package.json` para incluir scripts de build:

```json
{
  "name": "meu-projeto-django",
  "version": "1.0.0",
  "scripts": {
    "css:scss": "sass src/scss:static/css",
    "css:autoprefixer": "postcss static/css/*.css --use autoprefixer -d static/css",
    "css:build": "npm run css:scss && npm run css:autoprefixer",
    "js:build": "mkdir -p static/js && cp node_modules/bootstrap/dist/js/bootstrap.bundle.min.js static/js/ && cp node_modules/admin-lte/dist/js/adminlte.min.js static/js/",
    "build": "npm run css:build && npm run js:build",
    "watch": "sass --watch src/scss:static/css"
  },
  "dependencies": {
    "admin-lte": "^4.0.0",
    "bootstrap": "^5.3.0"
  },
  "devDependencies": {
    "autoprefixer": "^10.4.0",
    "postcss": "^8.4.0",
    "postcss-cli": "^9.1.0",
    "sass": "^1.56.0"
  }
}

```

## 5. Estrutura de arquivos SCSS personalizados

Crie uma pasta para seus arquivos SCSS fonte:

```bash
mkdir -p src/scss

```

Crie um arquivo principal SCSS que importará Bootstrap e AdminLTE:

```bash
# Criar o arquivo principal SCSS
touch src/scss/main.scss

```

Edite `src/scss/main.scss`:

```scss
// Variáveis personalizadas do Bootstrap (opcional)
$primary: #007bff;
$secondary: #6c757d;
// Adicione outras variáveis personalizadas conforme necessário

// Importar Bootstrap
@import "../../node_modules/bootstrap/scss/bootstrap";

// Importar AdminLTE 4
@import "../../node_modules/admin-lte/build/scss/adminlte";

// Seus estilos personalizados abaixo

```

## 6. Executando o Build

Execute o script de build para compilar os arquivos SCSS e copiar os arquivos JS:

```bash
npm run build

```

Isso irá:

1.  Compilar os arquivos SCSS para CSS na pasta `static/css/`
2.  Aplicar autoprefixer aos arquivos CSS para compatibilidade entre navegadores
3.  Copiar os arquivos JS necessários para a pasta `static/js/`

Para desenvolvimento, você pode executar o modo "watch" para recompilar automaticamente os arquivos SCSS quando eles forem alterados:

```bash
npm run watch

```

## 7. Integrando com Templates Django

Crie um template base que inclua o Bootstrap e o AdminLTE:

```bash
mkdir -p core/templates/core
touch core/templates/core/base.html

```

Edite `core/templates/core/base.html`:

```html
{% load static %}
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Meu Projeto Django{% endblock %}</title>
    
    <!-- CSS -->
    <link rel="stylesheet" href="{% static 'css/main.css' %}">
    
    <!-- Favicons, meta tags adicionais, etc. -->
    {% block extra_head %}{% endblock %}
</head>
<body class="layout-fixed">
    <div class="wrapper">
        <!-- Navbar -->
        <nav class="main-header navbar navbar-expand navbar-white navbar-light">
            <!-- Left navbar links -->
            <ul class="navbar-nav">
                <li class="nav-item">
                    <a class="nav-link" data-widget="pushmenu" href="#" role="button"><i class="fas fa-bars"></i></a>
                </li>
                <li class="nav-item d-none d-sm-inline-block">
                    <a href="/" class="nav-link">Home</a>
                </li>
            </ul>
        </nav>
        
        <!-- Main Sidebar Container -->
        <aside class="main-sidebar sidebar-dark-primary elevation-4">
            <!-- Brand Logo -->
            <a href="/" class="brand-link">
                <span class="brand-text font-weight-light">Meu Projeto</span>
            </a>
            
            <!-- Sidebar -->
            <div class="sidebar">
                <!-- Sidebar Menu -->
                <nav class="mt-2">
                    <ul class="nav nav-pills nav-sidebar flex-column" data-widget="treeview" role="menu">
                        <li class="nav-item">
                            <a href="/" class="nav-link">
                                <i class="nav-icon fas fa-home"></i>
                                <p>Dashboard</p>
                            </a>
                        </li>
                        <!-- Adicione mais itens de menu conforme necessário -->
                    </ul>
                </nav>
            </div>
        </aside>
        
        <!-- Content Wrapper -->
        <div class="content-wrapper">
            <!-- Content Header (Page header) -->
            <section class="content-header">
                <div class="container-fluid">
                    <div class="row mb-2">
                        <div class="col-sm-6">
                            <h1>{% block page_title %}{% endblock %}</h1>
                        </div>
                    </div>
                </div>
            </section>
            
            <!-- Main content -->
            <section class="content">
                <div class="container-fluid">
                    {% block content %}{% endblock %}
                </div>
            </section>
        </div>
        
        <!-- Footer -->
        <footer class="main-footer">
            <div class="float-right d-none d-sm-block">
                <b>Version</b> 1.0.0
            </div>
            <strong>Copyright © {% now "Y" %} <a href="#">Meu Projeto</a>.</strong> Todos os direitos reservados.
        </footer>
    </div>
    
    <!-- JavaScript -->
    <script src="{% static 'js/bootstrap.bundle.min.js' %}"></script>
    <script src="{% static 'js/adminlte.min.js' %}"></script>
    
    {% block extra_js %}{% endblock %}
</body>
</html>

```

Crie uma view de exemplo:

```python
# core/views.py
from django.shortcuts import render

def index(request):
    return render(request, 'core/index.html')

```

Crie um template que estenda o template base:

```bash
touch core/templates/core/index.html

```

Edite `core/templates/core/index.html`:

```html
{% extends 'core/base.html' %}

{% block title %}Dashboard{% endblock %}

{% block page_title %}Dashboard{% endblock %}

{% block content %}
<div class="row">
    <div class="col-lg-3 col-6">
        <div class="small-box bg-info">
            <div class="inner">
                <h3>150</h3>
                <p>Novos Pedidos</p>
            </div>
            <div class="icon">
                <i class="fas fa-shopping-cart"></i>
            </div>
            <a href="#" class="small-box-footer">Mais informações <i class="fas fa-arrow-circle-right"></i></a>
        </div>
    </div>
    <!-- Adicione mais blocos conforme necessário -->
</div>
{% endblock %}

```

Configure a URL:

```python
# meu_projeto/urls.py
from django.contrib import admin
from django.urls import path
from core.views import index

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', index, name='index'),
]

```

## 8. Font Awesome (Opcional mas recomendado para o AdminLTE)

O AdminLTE usa Font Awesome para ícones. Você pode adicioná-lo ao seu projeto:

```bash
# Instalar Font Awesome
npm install @fortawesome/fontawesome-free

```

Atualize seu arquivo `src/scss/main.scss` para incluir Font Awesome:

```scss
// Importações existentes...

// Importar Font Awesome
$fa-font-path: "../../node_modules/@fortawesome/fontawesome-free/webfonts";
@import "../../node_modules/@fortawesome/fontawesome-free/scss/fontawesome";
@import "../../node_modules/@fortawesome/fontawesome-free/scss/solid";
@import "../../node_modules/@fortawesome/fontawesome-free/scss/regular";
@import "../../node_modules/@fortawesome/fontawesome-free/scss/brands";

// Seus estilos personalizados...

```

Atualize o script `js:build` no `package.json` para copiar os arquivos de fontes:

```json
"js:build": "mkdir -p static/js && cp node_modules/bootstrap/dist/js/bootstrap.bundle.min.js static/js/ && cp node_modules/admin-lte/dist/js/adminlte.min.js static/js/ && mkdir -p static/webfonts && cp -r node_modules/@fortawesome/fontawesome-free/webfonts/* static/webfonts/",

```

Execute o build novamente:

```bash
npm run build

```

## 9. Coletando arquivos estáticos para produção

Quando estiver pronto para implantação, colete todos os arquivos estáticos:

```bash
python manage.py collectstatic

```

## 10. Configuração para produção

Para produção, certifique-se de configurar corretamente o `settings.py`:

```python
# meu_projeto/settings.py (para produção)
DEBUG = False
ALLOWED_HOSTS = ['seu-dominio.com', 'www.seu-dominio.com']

# Configuração de arquivos estáticos para produção
STATIC_ROOT = BASE_DIR / 'staticfiles'

```

## Resumo

Seu projeto agora está configurado com:

1.  Django como backend
2.  Bootstrap 5 para estilos base
3.  AdminLTE 4 para interface de administração
4.  SCSS para customização de estilos
5.  npm para gerenciar dependências e builds

Para desenvolvimento, use:

-   `npm run build` para compilar seus assets
-   `npm run watch` para recompilar automaticamente durante o desenvolvimento
-   `python manage.py runserver` para executar o servidor de desenvolvimento

Para produção, use:

-   `npm run build` para compilar os assets finais
-   `python manage.py collectstatic` antes do deploy
