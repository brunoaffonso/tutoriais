# Tutorial: Integrando Django com AdminLTE 4 e Bootstrap 5 usando npm

Este tutorial detalha como configurar um projeto Django para utilizar o template AdminLTE 4 (que por sua vez utiliza Bootstrap 5), gerenciando as dependências front-end (CSS/JS) com `npm` e criando um processo de build para compilar SCSS e preparar os arquivos estáticos para o Django.

**Tecnologias:**

* Django
* AdminLTE 4
* Bootstrap 5
* Node.js / npm
* Sass (Compilador SCSS)

---

## Pré-requisitos

Antes de começar, certifique-se de ter o seguinte instalado e configurado:

1.  **Python e Django:**
    * Python instalado ([python.org](https://www.python.org/))
    * Um ambiente virtual criado e ativado.
    * Django instalado (`pip install django`).
    * Um projeto Django e um app criados.

    ```bash
    # Criar ambiente virtual (exemplo)
    python -m venv venv

    # Ativar ambiente virtual
    # Linux/macOS:
    source venv/bin/activate
    # Windows:
    .\venv\Scripts\activate

    # Instalar Django
    pip install django

    # Criar projeto e app (exemplo)
    django-admin startproject meuprojeto
    cd meuprojeto
    python manage.py startapp meuapp

    # Não se esqueça de adicionar 'meuapp' a INSTALLED_APPS em meuprojeto/settings.py
    ```

2.  **Node.js e npm:**
    * Node.js e npm instalados ([nodejs.org](https://nodejs.org/)).
    * Verifique a instalação:
        ```bash
        node -v
        npm -v
        ```

---

## Passo a Passo

Siga estes passos na raiz do seu projeto Django (a pasta que contém `manage.py`).

### Passo 1: Inicializar o npm no Projeto Django

Crie um arquivo `package.json` para gerenciar as dependências front-end.

```bash
npm init -y

```

Isso cria um `package.json` básico na raiz do seu projeto.

### Passo 2: Instalar Bootstrap 5, AdminLTE 4 e Dependências

Instale as bibliotecas front-end como dependências de desenvolvimento (`--save-dev`), pois são necessárias principalmente durante o build.

``` Bash
npm install bootstrap@^5 --save-dev
npm install admin-lte@^4 --save-dev
# Nota: Bootstrap 5 bundle (bootstrap.bundle.min.js) já inclui Popper.js.
# Se precisar do Popper separadamente por algum motivo:
# npm install @popperjs/core --save-dev
```

### Passo 3: Instalar o Compilador SASS

Bootstrap 5 (e AdminLTE 4) utilizam SCSS. Precisamos de um compilador. Usaremos `sass` (Dart Sass).

``` Bash
npm install sass --save-dev

```

### Passo 4: Configurar os Arquivos Estáticos do Django

Edite o arquivo `meuprojeto/settings.py` para que o Django encontre e sirva os arquivos estáticos que vamos gerar.

1.  **`STATIC_URL`**: Geralmente já definido. Mantenha-o.
     
    ``` Python
    # meuprojeto/settings.py
    STATIC_URL = '/static/'
    
    ```
    
2.  **`STATICFILES_DIRS`**: Especifique diretórios onde o Django deve procurar por arquivos estáticos _além_ dos diretórios `static/` de cada app. Criaremos uma pasta `static/` na raiz para nossos assets compilados.
        
    ``` Python
    # meuprojeto/settings.py
    import os
    
    STATICFILES_DIRS = [
        os.path.join(BASE_DIR, 'static'),
    ]
    
    ```
    
3.  **`STATIC_ROOT`**: Defina o diretório para onde `collectstatic` copiará _todos_ os arquivos estáticos para produção. **Não deve ser o mesmo que qualquer diretório em `STATICFILES_DIRS`**.
        
    ``` Python
    # meuprojeto/settings.py
    STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles_prod') # Ou outro nome
    
    ```
    

### Passo 5: Criar Estrutura de Arquivos e Arquivo SCSS Principal

Organize seus arquivos fonte e de destino.

1.  **Crie as seguintes pastas e arquivos** na raiz do projeto:
    
    ```
    meuprojeto/
    ├── manage.py
    ├── meuprojeto/
    ├── meuapp/
    ├── node_modules/      (criado pelo npm)
    ├── package.json
    ├── package-lock.json  (criado pelo npm)
    ├── static/            <-- CRIE ESTA PASTA (assets compilados)
    │   ├── css/
    │   └── js/
    └── src/               <-- CRIE ESTA PASTA (código fonte front-end)
        └── scss/
            └── main.scss  <-- CRIE ESTE ARQUIVO
    
    ```
    
2.  **Edite `src/scss/main.scss`**: Este arquivo importará o Bootstrap, AdminLTE e seus estilos personalizados.
    
    ``` SCSS
    // src/scss/main.scss
    
    // Opcional: Importe partes do Bootstrap para customização avançada
    // @import "../../node_modules/bootstrap/scss/functions";
    // @import "../../node_modules/bootstrap/scss/variables"; // Importe ANTES de definir suas variáveis customizadas
    // @import "../../node_modules/bootstrap/scss/mixins";
    
    // Exemplo de customização de variáveis Bootstrap (ANTES do @import principal)
    // $primary: #007bff; // Cor primária padrão
    // $secondary: #6c757d; // Cor secundária padrão
    // $body-bg: #f8f9fa;   // Cor de fundo padrão
    
    // 1. Importe o SCSS principal do Bootstrap
    @import "../../node_modules/bootstrap/scss/bootstrap";
    
    // 2. Importe o SCSS principal do AdminLTE 4
    //    Verifique o caminho correto dentro de node_modules/admin-lte/dist ou node_modules/admin-lte/build
    //    Pode ser algo como:
    @import "../../node_modules/admin-lte/build/scss/adminlte";
    //    Ou verifique a documentação/estrutura da versão que você instalou.
    
    // 3. Adicione seus estilos personalizados aqui
    body {
      // Estilos globais, se necessário
    }
    
    .meu-componente-customizado {
      border: 1px solid $gray-300; // Usando variável do Bootstrap
      padding: 1rem;
      margin-top: 1rem;
    }
    
    // Mais estilos...
    
    ```
    
    _Importante:_ O caminho exato para importar `adminlte.scss` pode variar ligeiramente dependendo da versão exata do `admin-lte` instalada. Verifique a pasta `node_modules/admin-lte/`.
    

### Passo 6: Configurar Scripts de Build no `package.json`

Automatize o processo de compilação e cópia de arquivos usando scripts `npm`. Usaremos `copyfiles` para compatibilidade multi-plataforma.

1.  **Instale `copyfiles`:**
    
    Bash
    
    ```
    npm install copyfiles --save-dev
    
    ```
    
2.  **Adicione/Modifique a seção `"scripts"` no seu `package.json`:**
    
    ``` JSON
    {
      "name": "meuprojeto",
      "version": "1.0.0",
      "description": "",
      "main": "index.js",
      "scripts": {
        "compile:sass": "sass src/scss/main.scss static/css/main.css --style compressed --no-source-map",
        "copy:js": "copyfiles -u 1 node_modules/bootstrap/dist/js/bootstrap.bundle.min.js static/js && copyfiles -u 1 node_modules/admin-lte/dist/js/adminlte.min.js static/js",
        "build": "npm run compile:sass && npm run copy:js",
        "watch:sass": "sass --watch src/scss:static/css --style expanded --source-map"
      },
      "keywords": [],
      "author": "",
      "license": "ISC",
      "devDependencies": {
        // Suas dependências como admin-lte, bootstrap, sass, copyfiles estarão listadas aqui
        // Exemplo:
        // "admin-lte": "^4.0.0",
        // "bootstrap": "^5.3.3",
        // "copyfiles": "^2.4.1",
        // "sass": "^1.77.6"
      }
    }
    
    ```
    
    -   **`compile:sass`**: Compila `src/scss/main.scss` para `static/css/main.css`, minificando (`--style compressed`) e sem source map (`--no-source-map`). Para desenvolvimento, você pode remover `--no-source-map` ou usar `--source-map`.
    -   **`copy:js`**: Copia os arquivos JS necessários do Bootstrap (`bootstrap.bundle.min.js`) e AdminLTE (`adminlte.min.js`) de `node_modules` para `static/js/`. O `-u 1` remove o diretório `node_modules/` do caminho de origem.
    -   **`build`**: Executa a compilação e a cópia em sequência. Use este comando para gerar os assets finais.
    -   **`watch:sass`**: (Opcional, para desenvolvimento) Monitora a pasta `src/scss` e recompila automaticamente para `static/css` quando houver alterações. Gera CSS expandido e com source map para facilitar a depuração.

### Passo 7: Criar um Template Base no Django (`base.html`)

Crie um template base que inclua a estrutura HTML do AdminLTE e carregue os arquivos CSS/JS gerados.

1.  **Crie a pasta `templates`** na raiz do projeto (mesmo nível de `manage.py`), se ainda não existir.
    
2.  **Crie o arquivo `templates/base.html`:**
    
    ``` HTML
    {% load static %}
    <!DOCTYPE html>
    <html lang="pt-br">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>{% block title %}AdminLTE 4 | Meu Projeto{% endblock %}</title>
    
        <link rel="stylesheet" href="[https://fonts.googleapis.com/css?family=Source+Sans+Pro:300,400,400i,700&display=fallback](https://fonts.googleapis.com/css?family=Source+Sans+Pro:300,400,400i,700&display=fallback)">
        <link rel="stylesheet" href="[https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css](https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css)" />
    
        <link rel="stylesheet" href="{% static 'css/main.css' %}">
    
        {% block extra_head %}{% endblock %}
    </head>
    <body class="hold-transition sidebar-mini layout-fixed">
    <div class="wrapper">
    
        <nav class="main-header navbar navbar-expand navbar-white navbar-light">
            <ul class="navbar-nav">
                <li class="nav-item">
                    <a class="nav-link" data-widget="pushmenu" href="#" role="button"><i class="fas fa-bars"></i></a>
                </li>
                <li class="nav-item d-none d-sm-inline-block">
                    <a href="/" class="nav-link">Home</a>
                </li>
            </ul>
            <ul class="navbar-nav ml-auto">
                </ul>
        </nav>
        <aside class="main-sidebar sidebar-dark-primary elevation-4">
            <a href="/" class="brand-link">
                <span class="brand-text font-weight-light">Meu Projeto</span>
            </a>
    
            <div class="sidebar">
                <div class="user-panel mt-3 pb-3 mb-3 d-flex">
                    <div class="info">
                        <a href="#" class="d-block">Usuário</a> </div>
                </div>
    
                <nav class="mt-2">
                    <ul class="nav nav-pills nav-sidebar flex-column" data-widget="treeview" role="menu" data-accordion="false">
                        <li class="nav-item">
                            <a href="/" class="nav-link {% if request.path == '/' %}active{% endif %}">
                                <i class="nav-icon fas fa-tachometer-alt"></i>
                                <p>Dashboard</p>
                            </a>
                        </li>
                        </ul>
                </nav>
                </div>
            </aside>
    
        <div class="content-wrapper">
            <div class="content-header">
                <div class="container-fluid">
                    <div class="row mb-2">
                        <div class="col-sm-6">
                            <h1 class="m-0">{% block page_title %}Título Padrão{% endblock %}</h1>
                        </div>
                        <div class="col-sm-6">
                            <ol class="breadcrumb float-sm-right">
                                {% block breadcrumb %}
                                <li class="breadcrumb-item"><a href="/">Home</a></li>
                                <li class="breadcrumb-item active">Página Atual</li>
                                {% endblock %}
                            </ol>
                        </div>
                    </div>
                </div>
            </div>
            <section class="content">
                <div class="container-fluid">
                    {% block content %}
                    {% endblock %}
                </div>
            </section>
            </div>
        <footer class="main-footer">
            <strong>Copyright &copy; {% now "Y" %} <a href="#">Meu Projeto</a>.</strong>
            All rights reserved.
            <div class="float-right d-none d-sm-inline-block">
                <b>Version</b> 1.0
            </div>
        </footer>
    
    </div>
    <script src="[https://code.jquery.com/jquery-3.7.1.min.js](https://code.jquery.com/jquery-3.7.1.min.js)"></script>
    <script src="{% static 'js/bootstrap.bundle.min.js' %}"></script>
    <script src="{% static 'js/adminlte.min.js' %}"></script>
    
    {% block extra_scripts %}{% endblock %}
    </body>
    </html>
    
    ```
    
    _Nota:_ AdminLTE 4 provavelmente requer jQuery. O exemplo usa CDN, mas você pode instalar via `npm install jquery --save-dev` e adicionar a cópia ao script `copy:js` no `package.json`. Verifique a documentação do AdminLTE 4 para requisitos específicos. O mesmo vale para Font Awesome.
    
3.  **Configure o Django para encontrar a pasta `templates`**: No arquivo `meuprojeto/settings.py`, atualize a configuração `TEMPLATES`:
    
    ``` Python
    # meuprojeto/settings.py
    TEMPLATES = [
        {
            'BACKEND': 'django.template.backends.django.DjangoTemplates',
            # Adicione o caminho para a pasta 'templates' global
            'DIRS': [os.path.join(BASE_DIR, 'templates')],
            'APP_DIRS': True,
            'OPTIONS': {
                'context_processors': [
                    'django.template.context_processors.debug',
                    'django.template.context_processors.request',
                    'django.contrib.auth.context_processors.auth',
                    'django.contrib.messages.context_processors.messages',
                ],
            },
        },
    ]
    
    ```
    

### Passo 8: Criar uma View e URL de Exemplo

Crie uma página simples para testar o template base.

1.  **View (`meuapp/views.py`):**
    
    ``` Python
    # meuapp/views.py
    from django.shortcuts import render
    
    def home(request):
        context = {} # Adicione dados ao contexto se necessário
        return render(request, 'meuapp/home.html', context)
    
    ```
    
2.  **Template da View (`meuapp/templates/meuapp/home.html`):**
    
    -   Crie a estrutura de pastas: `meuapp/templates/meuapp/`.
    -   Crie o arquivo `home.html` dentro dela:
    
    ``` HTML
    {% extends "base.html" %}
    {% load static %}
    
    {% block title %}Página Inicial | Meu Projeto{% endblock %}
    
    {% block page_title %}Página Inicial{% endblock %}
    
    {% block breadcrumb %}
        <li class="breadcrumb-item"><a href="/">Home</a></li>
        <li class="breadcrumb-item active">Início</li>
    {% endblock %}
    
    {% block content %}
    <div class="row">
        <div class="col-12">
            <div class="card card-primary card-outline">
                <div class="card-header">
                    <h3 class="card-title">Bem-vindo!</h3>
                </div>
                <div class="card-body">
                    Este é o conteúdo da sua página inicial, usando o template base com AdminLTE 4 e Bootstrap 5.
                    <br><br>
                    <button class="btn btn-success">Botão de Sucesso</button>
                </div>
            </div>
        </div>
    </div>
    {% endblock %}
    
    {% block extra_scripts %}
        {% endblock %}
    
    ```
    
3.  **URLs:**
    
    -   Crie `meuapp/urls.py`:
        
        ``` Python
        # meuapp/urls.py
        from django.urls import path
        from . import views
        
        app_name = 'meuapp'
        
        urlpatterns = [
            path('', views.home, name='home'),
        ]
        
        ```
        
    -   Inclua as URLs do app em `meuprojeto/urls.py`:
        
        ``` Python
        # meuprojeto/urls.py
        from django.contrib import admin
        from django.urls import path, include # Adicione include
        
        urlpatterns = [
            path('admin/', admin.site.urls),
            path('', include('meuapp.urls')), # Rota raiz aponta para as URLs de meuapp
        ]
        
        ```
        

### Passo 9: Construir os Assets e Rodar o Servidor

Agora, vamos gerar os arquivos estáticos e iniciar o servidor Django.

1.  **Execute o build do npm:**
    
    ``` Bash
    npm run build
    
    ```
    
    Isso deve criar/atualizar `static/css/main.css` e copiar os arquivos JS para `static/js/`.
    
2.  **Execute as migrações do Django (se for a primeira vez):**
    
    ``` Bash
    python manage.py migrate
    ```
    
3.  **Inicie o servidor de desenvolvimento:**
    
    ``` Bash
    python manage.py runserver
    
    ```
    
5.  **Acesse no navegador:** Abra `http://127.0.0.1:8000/`. Você deverá ver sua página inicial estilizada com AdminLTE 4.
    

### Passo 10: Desenvolvimento e Deploy

-   **Desenvolvimento:**
    -   Para compilar SCSS automaticamente ao salvar, use: `npm run watch:sass` (em um terminal separado).
    -   Lembre-se de rodar `npm run build` antes de fazer commits ou sempre que atualizar dependências JS que precisam ser copiadas.
-   **Deploy (Produção):**
    1.  No servidor de produção, instale as dependências: `npm install`.
    2.  Execute o build dos assets: `npm run build`.
    3.  Configure seu servidor web (Nginx, Apache, etc.) para servir arquivos estáticos a partir do diretório definido em `STATIC_ROOT`.
    4.  Execute `collectstatic` no servidor:
        
        ``` Bash
        # Certifique-se que DEBUG=False em settings.py para produção
        python manage.py collectstatic --noinput
        
        ```
        
        Isso copiará todos os assets (do Django admin, de apps e os que você construiu com npm) para a pasta `STATIC_ROOT`.

----------

Pronto! Você configurou um fluxo de trabalho moderno para integrar AdminLTE 4 e Bootstrap 5 ao seu projeto Django, utilizando `npm` e `SCSS`.
