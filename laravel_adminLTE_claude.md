# Tutorial: Instalando e Configurando AdminLTE 4 com Laravel 12

Este tutorial guiará você passo a passo para integrar o AdminLTE 4 (que utiliza Bootstrap 5) em um projeto Laravel 12, incluindo a configuração de SCSS e o processo de build dos assets.

## Pré-requisitos

- PHP 8.2 ou superior
- Composer
- Node.js e NPM
- Laravel 12 instalado

## 1. Criando um novo projeto Laravel 12

Caso ainda não tenha um projeto Laravel 12, crie-o com o seguinte comando:

```bash
composer create-project laravel/laravel nome-do-projeto
cd nome-do-projeto
```

## 2. Configurando o Vite para o projeto

O Laravel 12 usa o Vite como bundler padrão. Vamos configurá-lo para trabalhar com SCSS e Bootstrap:

1. Instale as dependências necessárias:

```bash
npm install
npm install bootstrap @popperjs/core admin-lte@^4.0.0 sass
```

2. Configure o arquivo `vite.config.js` na raiz do projeto:

```javascript
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel({
            input: [
                'resources/scss/app.scss',
                'resources/js/app.js',
            ],
            refresh: true,
        }),
    ],
    resolve: {
        alias: {
            '~bootstrap': 'node_modules/bootstrap',
            '~admin-lte': 'node_modules/admin-lte',
        }
    },
});
```

## 3. Estruturando os arquivos SCSS e JS

Crie a estrutura de diretórios necessária:

```bash
mkdir -p resources/scss
touch resources/scss/app.scss
```

## 4. Configurando o SCSS

Edite o arquivo `resources/scss/app.scss`:

```scss
// Bootstrap
@import "~bootstrap/scss/bootstrap";

// AdminLTE
@import "~admin-lte/dist/css/adminlte.css";

// Seus estilos personalizados
.custom-style {
    // seus estilos
}
```

## 5. Configurando o JavaScript

Edite o arquivo `resources/js/app.js`:

```javascript
import './bootstrap';
import * as bootstrap from 'bootstrap';
window.bootstrap = bootstrap;

// Importa o AdminLTE
import 'admin-lte/dist/js/adminlte';
```

Crie ou atualize o arquivo `resources/js/bootstrap.js`:

```javascript
// Import lodash
import _ from 'lodash';
window._ = _;

// Import Bootstrap
import * as bootstrap from 'bootstrap';
window.bootstrap = bootstrap;

// Import Axios
import axios from 'axios';
window.axios = axios;
window.axios.defaults.headers.common['X-Requested-With'] = 'XMLHttpRequest';
```

## 6. Configuração do Layout Blade

Crie uma estrutura básica para usar o AdminLTE:

1. Crie o arquivo `resources/views/layouts/admin.blade.php`:

```html
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="csrf-token" content="{{ csrf_token() }}">
    <title>{{ config('app.name', 'Laravel') }}</title>
    
    <!-- Fonts -->
    <link rel="preconnect" href="https://fonts.bunny.net">
    <link href="https://fonts.bunny.net/css?family=figtree:400,500,600&display=swap" rel="stylesheet" />
    
    <!-- Font Awesome (necessário para o AdminLTE) -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    
    <!-- Scripts e Estilos -->
    @vite(['resources/scss/app.scss', 'resources/js/app.js'])
</head>
<body class="hold-transition sidebar-mini">
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
                <span class="brand-text font-weight-light">{{ config('app.name', 'Laravel') }}</span>
            </a>
            
            <!-- Sidebar -->
            <div class="sidebar">
                <!-- Sidebar Menu -->
                <nav class="mt-2">
                    <ul class="nav nav-pills nav-sidebar flex-column" data-widget="treeview" role="menu" data-accordion="false">
                        <li class="nav-item">
                            <a href="/" class="nav-link">
                                <i class="nav-icon fas fa-tachometer-alt"></i>
                                <p>Dashboard</p>
                            </a>
                        </li>
                        <!-- Adicione mais itens de menu aqui -->
                    </ul>
                </nav>
            </div>
        </aside>
        
        <!-- Content Wrapper -->
        <div class="content-wrapper">
            <!-- Content Header -->
            <div class="content-header">
                <div class="container-fluid">
                    <div class="row mb-2">
                        <div class="col-sm-6">
                            <h1 class="m-0">@yield('title', 'Dashboard')</h1>
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- Main content -->
            <div class="content">
                <div class="container-fluid">
                    @yield('content')
                </div>
            </div>
        </div>
        
        <!-- Footer -->
        <footer class="main-footer">
            <div class="float-right d-none d-sm-inline">
                Laravel 12 com AdminLTE 4
            </div>
            <strong>Copyright &copy; {{ date('Y') }} <a href="/">{{ config('app.name', 'Laravel') }}</a>.</strong> Todos os direitos reservados.
        </footer>
    </div>
</body>
</html>
```

## 7. Crie uma página de exemplo

Crie um arquivo `resources/views/dashboard.blade.php`:

```html
@extends('layouts.admin')

@section('title', 'Dashboard')

@section('content')
<div class="row">
    <div class="col-lg-6">
        <div class="card">
            <div class="card-header">
                <h5 class="card-title">Card Exemplo</h5>
            </div>
            <div class="card-body">
                <p class="card-text">Este é um exemplo de card usando AdminLTE 4 e Bootstrap 5 no Laravel 12.</p>
                <a href="#" class="btn btn-primary">Botão de Exemplo</a>
            </div>
        </div>
    </div>
</div>
@endsection
```

## 8. Configure uma rota para a página

Edite o arquivo `routes/web.php`:

```php
<?php

use Illuminate\Support\Facades\Route;

Route::get('/', function () {
    return view('dashboard');
});
```

## 9. Compilando os assets

Para desenvolvimento:

```bash
npm run dev
```

Para produção:

```bash
npm run build
```

O comando `npm run build` irá compilar todos os seus assets e colocá-los no diretório `public/build`, que é onde o Laravel irá servir os arquivos estáticos.

## 10. Configuração adicional do SCSS (opcional)

Se você quiser personalizar as variáveis do Bootstrap, você pode criar um arquivo `resources/scss/_variables.scss`:

```scss
// Override Bootstrap default variables
$primary: #3490dc;
$secondary: #6c757d;
// ... mais variáveis

// Importe este arquivo no topo do seu app.scss
```

E então atualize seu `app.scss`:

```scss
// Variáveis personalizadas
@import "variables";

// Bootstrap
@import "~bootstrap/scss/bootstrap";

// AdminLTE
@import "~admin-lte/dist/css/adminlte.css";

// Seus estilos personalizados
```

## 11. Trabalhando com componentes AdminLTE

O AdminLTE 4 inclui vários componentes Bootstrap personalizados. Para usá-los, consulte a documentação oficial do AdminLTE 4.

## 12. Deploy para produção

Quando estiver pronto para implantar seu aplicativo:

1. Execute o build dos assets para produção:

```bash
npm run build
```

2. Certifique-se de que seu arquivo `.env` está configurado para produção:

```
APP_ENV=production
APP_DEBUG=false
```

3. Otimize o Laravel:

```bash
php artisan optimize
```

## Solução de problemas comuns

### Problema: Os assets não carregam corretamente
Solução: Verifique se os caminhos no Vite estão corretos e se o comando `npm run build` foi executado sem erros.

### Problema: Conflitos de CSS/JS
Solução: Verifique a ordem de importação nos arquivos SCSS e JS para garantir que não haja sobreposições indesejadas.

### Problema: AdminLTE não funciona como esperado
Solução: Certifique-se de que todos os dependências do AdminLTE, como Font Awesome, estão corretamente instaladas e importadas.
