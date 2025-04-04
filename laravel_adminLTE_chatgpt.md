# Projeto Laravel 12 com AdminLTE 4 e Bootstrap 5

Este tutorial apresenta um passo a passo para iniciar um projeto Laravel 12 utilizando o AdminLTE 4 como interface e o Bootstrap 5. Inclui a utilização do npm para instalação das dependências, compilação dos assets via Vite e configuração para o deploy da aplicação.


## Criação do Projeto Laravel

1.  Abra o terminal e execute o comando para criar um novo projeto Laravel:
    
    ```bash
    composer create-project laravel/laravel nome-do-projeto
    
    ```
    
2.  Entre no diretório do projeto:
    
    ```bash
    cd nome-do-projeto
    
    ```
    

----------

## Instalação das Dependências via NPM

1.  Instale as dependências padrão do Laravel:
    
    ```bash
    npm install
    
    ```
    
2.  Instale o Bootstrap 5 e o AdminLTE 4:
    
    ```bash
    npm install bootstrap@5 admin-lte
    
    ```
    
3.  **Observação:** Caso o AdminLTE 4 necessite do jQuery, instale-o também:
    
    ```bash
    npm install jquery
    
    ```
    

----------

## Configuração dos Arquivos de Assets

### Arquivo SCSS

Crie o arquivo `resources/scss/app.scss` (caso não exista) e adicione os seguintes imports:

```scss
// Importa o Bootstrap 5 (SCSS)
@import "~bootstrap/scss/bootstrap";

// Importa o AdminLTE 4 (SCSS)
// Verifique o caminho correto conforme a estrutura do pacote. Geralmente é:
@import "~admin-lte/build/scss/adminlte";

// Inclua estilos customizados abaixo, se necessário.

```

### Arquivo JavaScript

No arquivo `resources/js/app.js`, importe os scripts necessários:

```javascript
// Importa o Bootstrap 5 (JS)
import 'bootstrap';

// Importa o AdminLTE 4 (JS)
import 'admin-lte';

// Se estiver utilizando jQuery, garanta que ele seja global
import $ from 'jquery';
window.$ = $;

```

----------

## Configuração do Vite

O Laravel 12 utiliza o Vite para compilar os assets. Para configurá-lo, edite o arquivo `vite.config.js` na raiz do projeto:

```javascript
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel({
            input: [
                'resources/js/app.js',
                'resources/scss/app.scss',
            ],
            refresh: true,
        }),
    ],
});

```

Esta configuração instrui o Vite a processar tanto o JavaScript quanto o SCSS e a gerar os arquivos finais na pasta de build (geralmente `public/build`).

----------

## Inclusão dos Assets nos Templates Blade

No template Blade principal (por exemplo, `resources/views/welcome.blade.php`), adicione a diretiva `@vite` para carregar os arquivos compilados:

```html
<!DOCTYPE html>
<html lang="pt">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Projeto Laravel com AdminLTE</title>
    @vite(['resources/js/app.js', 'resources/scss/app.scss'])
</head>
<body>
    <div class="wrapper">
        <!-- Estrutura e conteúdo do AdminLTE -->
    </div>
</body>
</html>

```

A diretiva `@vite` garante que, durante o desenvolvimento, os arquivos sejam carregados com Hot Module Replacement (HMR) e, em produção, os assets compilados sejam referenciados corretamente.

----------

## Compilando os Assets com NPM

### Ambiente de Desenvolvimento

Para iniciar o servidor de desenvolvimento com Vite e acompanhar alterações em tempo real, execute:

```bash
npm run dev

```

### Ambiente de Produção

Quando estiver pronto para gerar os assets para o deploy, execute:

```bash
npm run build

```

Após a execução, os arquivos compilados serão gerados (normalmente na pasta `public/build`) e serão servidos pelo Laravel como assets estáticos.

----------

## Deploy da Aplicação

-   Certifique-se de incluir a pasta `public/build` no deploy, pois ela contém os arquivos finais gerados pelo Vite.
    
-   Em produção, a diretiva `@vite` em seus templates Blade fará a referência correta aos assets compilados.
    

----------

## Considerações Finais

-   **Personalizações:** Para customizar os estilos do Bootstrap ou do AdminLTE, edite o arquivo `resources/scss/app.scss` e execute novamente o build.
    
-   **Verificação de Dependências:** Consulte a documentação do AdminLTE 4 para confirmar se há dependências adicionais ou configurações específicas.
    
-   **Utilização do jQuery:** Se componentes do AdminLTE exigirem jQuery, a importação no arquivo JavaScript garantirá que ele esteja disponível globalmente.
    

----------

Este tutorial oferece um fluxo modular e organizado para integrar o Bootstrap 5 e o AdminLTE 4 em seu projeto Laravel 12 utilizando npm e Vite, facilitando o desenvolvimento e o deploy da aplicação com as melhores práticas atuais.
