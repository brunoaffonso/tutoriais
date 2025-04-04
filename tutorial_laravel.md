# Roadmap Full Stack Laravel (Blade & Livewire) para Desenvolvedor Django

## 1. Configuração inicial do ambiente e instalação do Laravel

-   **Instalação do PHP e Composer:** Certifique-se de ter o PHP (de preferência PHP 8.x) instalado, junto com o Composer (gerenciador de dependências do PHP, equivalente ao `pip` no Python). O Composer permite instalar pacotes PHP como o Laravel ([Laravel Series Part 1 — Environment Setup & Laravel Installation | by Tharjalan Jeyaradnam | Medium](https://medium.com/@tharju004/laravel-series-part-1-environment-setup-laravel-installation-d67257751d6c#:~:text=https%3A%2F%2Fwww,Which%20is%20really%20nice%20Text)).
    
-   **Criando um novo projeto Laravel:** Assim como no Django usamos `django-admin startproject`, no Laravel utilizamos o Composer ou o instalador do Laravel. Por exemplo, para criar um projeto Laravel chamado _example-app_:
    
    ```bash
    composer create-project "laravel/laravel:^10.0" example-app
    
    ```
    
    Esse comando baixa o framework e cria a estrutura básica do projeto ([Installation - Laravel 10.x - The PHP Framework For Web Artisans](https://laravel.com/docs/10.x#:~:text=via%20Composer%27s%20%60create)). Alternativamente, podemos instalar o instalador global do Laravel (`composer global require laravel/installer`) e rodar `laravel new nome-do-projeto` ([Installation - Laravel 10.x - The PHP Framework For Web Artisans](https://laravel.com/docs/10.x#:~:text=composer%20create)).
    
-   **Configuração de ambiente de desenvolvimento:** Após criar o projeto, navegue até a pasta (`cd example-app`) e inicialize o servidor de desenvolvimento embutido do Laravel: `php artisan serve`. Isso inicia a aplicação em **[http://localhost:8000](http://localhost:8000/)** (similar ao `python manage.py runserver` do Django) ([Installation - Laravel 10.x - The PHP Framework For Web Artisans](https://laravel.com/docs/10.x#:~:text=3%20php%20artisan%20serve)). Você deverá ver a página inicial padrão do Laravel ao acessar esse URL.
    
-   **Variáveis de ambiente (.env):** O Laravel utiliza um arquivo _.env_ na raiz do projeto para configurações sensíveis (chave secreta, credenciais de banco de dados, etc.), similar ao uso de variáveis de ambiente no Django (por exemplo, via `django-environ` ou configurações separadas). As configurações default do Laravel estão em `config/*.php` e muitas lêem os valores do _.env_. **Não** commitamos esse arquivo no controle de versão, assim como não se deve versionar credenciais no Django.
    
-   **Banco de dados local:** Por padrão, o Laravel vem configurado para MySQL (ver `DB_*` no _.env_). Você pode ajustar para o SGBD de sua preferência editando o _.env_. Por exemplo, para usar SQLite em desenvolvimento, basta criar um arquivo `database.sqlite` e definir no _.env_: `DB_CONNECTION=sqlite` (os outros parâmetros de host/porta não serão necessários) ([Installation - Laravel 10.x - The PHP Framework For Web Artisans](https://laravel.com/docs/10.x#:~:text=If%20you%20are%20developing%20on,Redis%20locally%2C%20consider%20using%20DBngin)). Isso é útil para testes rápidos, assim como o Django usa SQLite por padrão em projetos novos. Se quiser usar PostgreSQL ou MySQL, atualize `DB_CONNECTION` para `pgsql` ou `mysql` e ajuste host, porta, usuário e senha conforme seu ambiente. O Laravel suporta esses bancos nativamente, de forma análoga ao Django que suporta PostgreSQL, MySQL, SQLite etc.
    
-   **Uso do Oracle com Docker:** O Oracle não é suportado nativamente pelo Laravel ([A Quick Guide to Using Laravel With Oracle Database](https://adevait.com/laravel/how-to-use-laravel-with-oracle-database#:~:text=not%20supported%20out%20of%20the,box%20by%20Laravel)), mas é possível integrá-lo. Para evitar instalar o Oracle localmente (que é complexo), recomenda-se usar um container Docker do Oracle XE (Express Edition). Existem imagens Docker comunitárias e oficiais – por exemplo, a imagem **gvenzl/oracle-xe** disponibiliza o Oracle XE de forma simplificada ([Introducing gvenzl/oracle-xe: Oracle Database XE Docker images](https://geraldonit.com/2021/08/15/oracle-xe-docker-images/#:~:text=Introducing%20gvenzl%2Foracle,for%20things%20like%20CI%2FCD%20consumption)). Suba o container Oracle e então instale o pacote **yajra/laravel-oci8** no Laravel (`composer require yajra/laravel-oci8`), que adiciona o driver OCI8 ao framework ([A Quick Guide to Using Laravel With Oracle Database](https://adevait.com/laravel/how-to-use-laravel-with-oracle-database#:~:text=with%20the%20yajra%2Flaravel,Laravel%20working%20with%20Oracle%20Database)). Registre o service provider do Oracle (conforme a documentação do pacote) e configure uma conexão `oracle` em `config/database.php` com host, porta (1521), SID/serviço, usuário e senha ([A Quick Guide to Using Laravel With Oracle Database](https://adevait.com/laravel/how-to-use-laravel-with-oracle-database#:~:text=%27oracle%27%20%3D,env%28%27DB_CHARSET%27%2C%20%27AL32UTF8)). No arquivo _.env_, ajuste `DB_CONNECTION=oracle` e as credenciais correspondentes. Com OCI8 e o pacote do Yajra configurados, o Eloquent funcionará com Oracle normalmente ([A Quick Guide to Using Laravel With Oracle Database](https://adevait.com/laravel/how-to-use-laravel-with-oracle-database#:~:text=with%20the%20yajra%2Flaravel,Laravel%20working%20with%20Oracle%20Database)). _Dica:_ ao usar Oracle, lembre-se de incluir o client/driver Oracle (por exemplo, instantclient) no ambiente PHP, especialmente se estiver usando containers.
    

## 2. Estrutura do projeto Laravel comparada ao Django

Ao abrirmos a pasta do projeto Laravel, vemos uma estrutura de diretórios já organizada. Isto contrasta com o Django, onde após o `startproject` e `startapp` temos pastas de apps contendo arquivos como `models.py`, `views.py`, etc. No Laravel, a separação é por convenção de tipos de arquivo (MVC), não por apps. Principais pastas e arquivos do Laravel e suas analogias:

-   **`app/`**: Contém o código da aplicação em si (lado servidor). No Laravel 10+, geralmente há subpastas como `Http/Controllers` (controladores) e `Models` (modelos) ([Laravel — P3: Directory Structure Basics | by Dino Cajic | Geek Culture | Medium](https://medium.com/geekculture/laravel-p3-directory-structure-basics-d922acc135c#:~:text=,%60resources%2Fviews)). Por exemplo, `app/Http/Controllers` é onde ficam os controllers (responsável pela lógica das rotas, similar às _views_ do Django), e `app/Models` contém as classes de modelo (representando tabelas do banco, similar às classes em `models.py` do Django) ([Laravel — P3: Directory Structure Basics | by Dino Cajic | Geek Culture | Medium](https://medium.com/geekculture/laravel-p3-directory-structure-basics-d922acc135c#:~:text=,command%20for%20creating%20these%20files)).
    
-   **`routes/`**: Aqui ficam os arquivos de definição de rotas. O principal é `web.php` (rotas web, que renderizam Blade views ou redirecionam, etc.) e há também `api.php` (rotas para APIs REST, geralmente retornando JSON). Essas rotas **substituem o `urls.py` do Django** – em vez de termos múltiplos arquivos de URL por app, o Laravel centraliza as rotas nesses arquivos, ou subdivide por domínio (web, api) ([Laravel — P3: Directory Structure Basics | by Dino Cajic | Geek Culture | Medium](https://medium.com/geekculture/laravel-p3-directory-structure-basics-d922acc135c#:~:text=routes%2F%20api,logs%2F%20tests%2F%20Feature)).
    
-   **`resources/views/`**: Equivale ao diretório de templates do Django. Aqui ficam os arquivos Blade (.blade.php), que são os templates renderizados para formar as páginas HTML ([Laravel — P3: Directory Structure Basics | by Dino Cajic | Geek Culture | Medium](https://medium.com/geekculture/laravel-p3-directory-structure-basics-d922acc135c#:~:text=resources%2F%20,php%20storage)). Podemos organizar em subpastas conforme necessidade. Laravel já vem com um `resources/views/welcome.blade.php` de exemplo.
    
-   **`database/migrations/`**: Contém as migrações do banco de dados, similares às migrações do Django (arquivos gerados por `makemigrations`). No Laravel, essas migrações são arquivos PHP que definem as tabelas e colunas a criar/altear ([Laravel — P3: Directory Structure Basics | by Dino Cajic | Geek Culture | Medium](https://medium.com/geekculture/laravel-p3-directory-structure-basics-d922acc135c#:~:text=database%2F%20factories%2F%20UserFactory,php%20lang)). Por padrão, um novo projeto inclui migrações prontas para criar a tabela de usuários e de password resets (isso já prepara o esquema básico para autenticação, análogo a rodar `python manage.py migrate` inicial no Django com o app `auth` – o Laravel já traz esse esquema básico) ([Laravel — P3: Directory Structure Basics | by Dino Cajic | Geek Culture | Medium](https://medium.com/geekculture/laravel-p3-directory-structure-basics-d922acc135c#:~:text=database%2F%20factories%2F%20UserFactory,php%20lang)).
    
-   **Outros diretórios importantes:** `config/` (arquivos de configuração para vários aspectos da aplicação, similar ao `settings.py` do Django, só que divididos em múltiplos arquivos por tema), `public/` (raiz pública da aplicação – contém o `index.php` que é o front controller, além de assets públicos; similar à pasta `static/` coletada do Django, ou ao role do `manage.py runserver` que serve arquivos estáticos, mas aqui é ponto de entrada para o servidor web real), `storage/` (para logs, arquivos gerados, cache de views compiladas, etc., semelhante à ideia de MEDIA_ROOT do Django para uploads e logs, embora separados em subpastas), `bootstrap/` (carregamento inicial do framework, não se altera muito), `vendor/` (pacotes instalados via Composer, equivalente à pasta de libs instaladas no virtualenv do Python).
    
-   **MVC vs MVT:** O Laravel segue MVC (Model-View-Controller), enquanto o Django segue MVT (Model-View-Template). Na prática, “View” no Django equivale ao “Controller” no Laravel, e “Template” do Django equivale à “View” (Blade) no Laravel ([Similarities between Django and Laravel | by Goga Patarkatsishvili | Medium](https://medium.com/@ent1c3d/similarities-between-django-and-laravel-eb5e2b344ecb#:~:text=I%20think%20you%20already%20heard,like%20controller%20in%20Laravel)). Essa diferença de nomenclatura pode confundir inicialmente. Em resumo: no Django, a view (função/classe) contém a lógica e seleciona um template; no Laravel, o controller (controller é a classe) contém a lógica e retorna uma _view_ Blade. Ambos frameworks seguem a separação de responsabilidades em camadas semelhantes ([Similarities between Django and Laravel | by Goga Patarkatsishvili | Medium](https://medium.com/@ent1c3d/similarities-between-django-and-laravel-eb5e2b344ecb#:~:text=I%20think%20you%20already%20heard,like%20controller%20in%20Laravel)).
    

## 3. Criação de rotas, controladores e views (Blade)

Nesta seção, vamos ver como o Laravel lida com rotas e como isso se compara ao Django (que usa `urls.py` e funções de view). O fluxo básico no Laravel (MVC) é: Rota -> Controlador -> View.

-   **Definição de Rotas:** No Django, definimos rotas em `urls.py` usando funções `path()` ou `re_path()` para apontar para uma view. Já no Laravel, definimos rotas nos arquivos dentro de `routes/`. O mais usado é o `routes/web.php` para rotas que retornam views HTML. Por exemplo, para definir uma rota GET `/posts/` que aponta para um controlador, escrevemos em `web.php`:
    
    ```php
    use App\Http\Controllers\PostController;
    Route::get('/posts', [PostController::class, 'index'])->name('posts.index');
    
    ```
    
    Isso equivale a algo como `path('posts/', views.post_list, name='posts-list')` no Django. No Laravel, `Route::get` registra uma rota GET, indicando o controlador e método que a atende, e podemos dar um nome à rota (para geração de URLs, similar ao `name=` no Django) ([Similarities between Django and Laravel | by Goga Patarkatsishvili | Medium](https://medium.com/@ent1c3d/similarities-between-django-and-laravel-eb5e2b344ecb#:~:text=Routing%20system)) ([Similarities between Django and Laravel | by Goga Patarkatsishvili | Medium](https://medium.com/@ent1c3d/similarities-between-django-and-laravel-eb5e2b344ecb#:~:text=Structure%20of%20the%20system)). Também é possível definir rotas usando closures (funções anônimas) diretamente no arquivo de rotas para algo simples, mas para separar bem a lógica, geralmente usamos controllers.
    
-   **Controladores (Controllers):** No Django, podemos escrever views como funções (`def view(request): ...`) ou classes baseadas em `View` ou `APIView` (no DRF). No Laravel, as _views_ de lógica são **sempre classes de controlador** (subclasses de `App\Http\Controllers\Controller`). Ou seja, cada rota geralmente aponta para um método de um controller. Por exemplo, `PostController@index` é um método que lida com a listagem de posts. Para criar um controller, usamos Artisan: `php artisan make:controller PostController` (podemos adicionar flags para gerar métodos RESTful automaticamente). Dentro do controller, definimos métodos públicos que correspondem às ações. **Importante:** não há funções de view soltas como no Django; a abordagem é sempre usar classes (o que disciplina a organização do código) ([My experience of moving from Laravel to Django - Roman Sorin](https://romansorin.com/blog/my-experience-of-moving-from-laravel-to-django#:~:text=match%20at%20L144%20your%20endpoints,approach%2C%20these%20are%20always%20classes)). Um desenvolvedor Django deve ter em mente que métodos de controller no Laravel cumprem o papel das funções de view no Django.
    
    -   _Exemplo:_ No Django, uma view de listagem poderia fazer `posts = Post.objects.all(); return render(request, 'posts.html', {'posts': posts})`. No Laravel, o método `index()` do `PostController` faria algo como `$posts = Post::all(); return view('posts', ['posts' => $posts]);`. Notar que usamos a função global `view()` para renderizar a template Blade `resources/views/posts.blade.php`.
        
    -   Os controllers Laravel podem retornar várias coisas: um **view Blade** (HTML), um **JSON** (usando `return response()->json($data)` ou simplesmente retornando um array/coleção que o Laravel converte em JSON no contexto de API), um **redirect**, etc. Isso é similar a como no Django podemos retornar um HttpResponse, JsonResponse ou Redirect.
        
    -   Assim como no Django podemos organizar views em múltiplos módulos apps, no Laravel podemos organizar controllers em sub-pastas ou namespaces se o projeto crescer (por exemplo, agrupar controllers de admins, de API, etc.).
        
-   **Views (Blade Templates):** Uma vez que a rota aciona o método do controller e este obtém dados, ele normalmente retorna uma _view_ Blade. Blade é o motor de templates do Laravel (ver seção 6 abaixo). Basta chamar `return view('nome-template', $dados)` passando o nome do template (sem `.blade.php`) e os dados. Blade receberá esses dados e renderizará o HTML. No Django, usaríamos `render(request, 'template.html', context)`. A ideia de separar o HTML em templates continua a mesma em Laravel. A principal diferença é a sintaxe Blade vs Django Template Language, mas ambos suportam loops, condicionais, herança de layouts, etc.
    
-   **Analogias importantes:**
    
    -   **URL nomeada:** Tanto Django quanto Laravel permitem nomear rotas. No Django, usamos `{% url 'nome_da_url' parametro %}` no template. No Blade, usamos `{{ route('nome.da.rota', parametro) }}` para gerar URLs.
        
    -   **Parâmetros na URL:** No Django definimos algo como `path('posts/<int:id>/', view, name='post-detail')`. No Laravel, definimos `Route::get('/posts/{id}', [PostController::class, 'show'])->name('posts.show');`. Laravel espera parâmetros entre `{ }` na rota, e esses são recebidos como argumentos no método do controller (por exemplo, `public function show($id)`). Regras de regex/formato podem ser definidas via métodos auxiliares (ex: ->where('id','[0-9]+')). Também é possível tornar parâmetros opcionais usando `?` e fornecendo um default no método, similar ao `path('route/<param>?', ...)` do Django.
        
    -   **Middleware de rota:** No Django, podemos aplicar middleware global ou por view (via decorador). No Laravel, podemos agrupar rotas e aplicar _middlewares_. Um muito usado é o `auth` para proteger rotas (ver seção de autenticação). Exemplo:
        
        ```php
        Route::middleware('auth')->group(function() {
           Route::get('/dashboard', [DashboardController::class, 'index']);
        });
        
        ```
        
        Isso garante que acessar `/dashboard` execute primeiro o middleware de autenticação (redirecionando não logados). No Django faríamos `@login_required` em cada view ou usaríamos `LoginRequiredMixin` nas class-based views.
        

Em suma, **rotas no Laravel (em `web.php`) correspondem às entradas no `urls.py` do Django**, **controllers Laravel correspondem às views Django**, e **Blade templates correspondem aos templates Django** – a separação de responsabilidades é semelhante, embora com sintaxe e convenções diferentes.

## 4. Criação de modelos e migrações

Agora, focamos na camada de **Model e Migração** no Laravel e comparamos com o Django:

-   **Definição de Modelos:** No Django, modelos são classes Python em `models.py` com atributos representando campos (e o ORM cria as tabelas baseado nisso). No Laravel (Eloquent ORM), os modelos são classes PHP que estendem `Illuminate\Database\Eloquent\Model`. Curiosamente, **no Laravel não definimos os campos da tabela na classe modelo** (não há algo como `name = models.CharField(max_length=...)`). Em vez disso, a definição de colunas é feita nas migrações (ver adiante) ([My experience of moving from Laravel to Django - Roman Sorin](https://romansorin.com/blog/my-experience-of-moving-from-laravel-to-django#:~:text=Unlike%20Django%2C%20defining%20fields%20is,instance%20from%20the%20database%20representation)). A classe do modelo serve principalmente para configurar detalhes (nome da tabela se for diferente do padrão, chave primária se não for `id`, casts de tipo, atributos fillable/guarded, relacionamentos com outros modelos, etc.). Isso pode parecer estranho para quem vem do Django, mas é o padrão Active Record do Eloquent: a estrutura do banco fica nas migrações, e o modelo é uma representação orientada a objeto daquela tabela (com métodos utilitários). _Exemplo:_ para criar um modelo `Post`, rodamos `php artisan make:model Post`. Isso gera `app/Models/Post.php` com uma classe vazia `class Post extends Model`. Podemos definir lá, por exemplo, `protected $fillable = ['titulo','conteudo']` para permitir atribuição em massa, mas não definimos tipos/tamanhos ali – isso irá na migração. Em Django, esses atributos seriam definidos nos campos da classe do modelo.
    
-   **Migrations (Migrações):** No Django, usamos `python manage.py makemigrations` para gerar migrações automaticamente a partir das mudanças nos modelos, e `migrate` para aplicá-las. No Laravel, as migrações são escritas manualmente (ou semi-automaticamente com Artisan). Por exemplo, ao criar o modelo com `artisan make:model Post -m`, ele já gera uma migração correspondente (flag `-m` faz isso). Essa migração terá um arquivo em `database/migrations/` com nome timestamped, por exemplo `2023_11_01_123456_create_posts_table.php`. Dentro dele, há uma classe com método `up()` onde definimos a estrutura da tabela via o _Schema Builder_ do Laravel. Exemplo simplificado:
    
    ```php
    Schema::create('posts', function(Blueprint $table){
        $table->id();
        $table->string('titulo');
        $table->text('conteudo');
        $table->timestamps();
    });
    
    ```
    
    Isso criaria uma tabela `posts` com colunas id, titulo, conteudo, e timestamps (created_at, updated_at) por convenção. Essa abordagem explícita é diferente do Django, mas oferece controle total sobre o SQL gerado. **Analogias:** O comando `php artisan migrate` no Laravel executa as migrações pendentes (como o `manage.py migrate` do Django). Já criar nova migração sem modelo pode ser feito via `php artisan make:migration add_field_to_posts_table --table=posts` por exemplo, se quisermos alterar algo – parecido com fazer alterações de modelo e rodar makemigrations no Django, mas aqui você escreve as mudanças.
    
-   **Sincronização modelo vs BD:** Como os campos não são definidos na classe modelo, a “fonte da verdade” do esquema são as migrações. É responsabilidade do desenvolvedor manter os modelos e migrações coerentes. Normalmente, após criar a migração de tabela, você pode adicionar no modelo propriedades `$fillable` (quais campos podem ser atribuição em massa) ou `$casts` (conversões de tipo), mas o tipo/tamanho do campo está apenas na migração. Vale mencionar que **o Laravel separa a instância do modelo da representação do banco** — ou seja, a classe modelo sabe a tabela e chave primária, mas os detalhes das colunas vêm do banco ([My experience of moving from Laravel to Django - Roman Sorin](https://romansorin.com/blog/my-experience-of-moving-from-laravel-to-django#:~:text=,instance%20from%20the%20database%20representation)) ([My experience of moving from Laravel to Django - Roman Sorin](https://romansorin.com/blog/my-experience-of-moving-from-laravel-to-django#:~:text=Unlike%20Django%2C%20defining%20fields%20is,instance%20from%20the%20database%20representation)). No Django, a classe do modelo contém toda essa informação de forma declarativa.
    
-   **Aplicando migrações:** Depois de escrever/criar as migrações, rodamos `php artisan migrate` para aplicar. O Laravel criará uma tabela `migrations` (similar à `_django_migrations` do Django) para controlar quais migrações já foram executadas.
    
-   **Seeder e Factory:** Assim como no Django podemos usar fixtures ou scripts para popular dados iniciais (por exemplo, criar um superuser inicial), o Laravel tem _seeders_ e _factories_. Seeders (`database/seeders`) são classes que inserem dados padrão (rodadas via `php artisan db:seed`). Por exemplo, o `DatabaseSeeder` pode chamar outros seeders, útil para dados de teste ou setup inicial. Já _factories_ (`database/factories`) são usadas para gerar dados fake para testes, análogo ao uso de libraries como Factory Boy ou ao `django.contrib.faker`. O Laravel tem integração com Faker para gerar dados sintéticos. Esse aspecto não tem um paralelo nativo no Django (onde usaríamos pacotes ou escreveríamos utilitários).
    
-   **Conexão com Banco de Dados:** O arquivo `.env` e `config/database.php` definem a conexão padrão. Por exemplo, para usar PostgreSQL, colocar `DB_CONNECTION=pgsql` e credenciais; para MySQL, `mysql`; SQLite já vimos; e para Oracle configuramos via o driver do Yajra conforme mencionado. Após ajustar isso e criar migrações, o comando migrate vai criar as tabelas no banco escolhido. Ou seja, o Eloquent é agnóstico ao SGBD (até certo ponto), semelhante ao Django ORM que suporta multiplataformas. No caso do Oracle, após instalação do driver, podemos executar migrações normalmente que o Yajra/OCI8 traduz para Oracle. **Dica:** se estiver usando Oracle XE via Docker, lembre de esperar o container subir e colocar as credenciais corretas (.env). Uma vantagem de usar Docker aqui é isolar o Oracle e facilmente descartá-lo quando não precisar, visto que Oracle pode ocupar bastante recurso.
    

## 5. Autenticação e sistema de login

Desenvolver autenticação em Laravel tem conceitos similares ao do Django (`django.contrib.auth`), embora a implementação difira. Vamos cobrir usuários, login, logout e proteção de rotas.

-   **Modelo de Usuário:** No Django, o `User` model (via `django.contrib.auth.models.User` ou um custom) vem pronto, e a infra de autenticação se baseia nele. No Laravel, um novo projeto já traz um modelo `App/Models/User.php` básico e migrações para criar a tabela `users` e `password_resets` ([Laravel — P3: Directory Structure Basics | by Dino Cajic | Geek Culture | Medium](https://medium.com/geekculture/laravel-p3-directory-structure-basics-d922acc135c#:~:text=database%2F%20factories%2F%20UserFactory,php%20lang)). Ou seja, ao rodar `php artisan migrate` inicialmente, você já terá uma tabela de usuários com campos id, name, email, password, timestamps, e uma tabela para tokens de reset de senha – algo semelhante a aplicar as migrations iniciais do app auth no Django. O modelo User do Laravel usa bcrypt para armazenar senha (config padrão) e já traz alguns helpers (traits) para autenticação.
    
-   **Guards e Providers:** Laravel utiliza o conceito de **guards** para diferentes estratégias de autenticação (por exemplo, web via sessão, api via tokens) e **providers** para fonte dos usuários (por padrão, Eloquent com o modelo User, mas poderia ser outra fonte). Essas configurações estão em `config/auth.php`. No uso típico (guard `web`, provider `users`), isso já está pronto out-of-the-box – similar ao Django que já tem autenticação por sessão e user model prontos.
    
-   **Login e Logout via scaffold (Laravel Breeze):** A maneira mais rápida de ter telas de login, registro, etc., é usar um _starter kit_ oficial. O **Laravel Breeze** é um pacote leve que gera todo o boilerplate de autenticação (rotas, controllers, Blade views) em minutos ([Authentication in Laravel Using Breeze - Kinsta®](https://kinsta.com/blog/laravel-breeze/#:~:text=Laravel%20Breeze%20is%20an%20authentication,also%20has%20an%20API%20version)). Ele inclui: registro de usuário, login, logout, reset de senha, verificação de email, atualização de perfil – semelhante ao que o Django fornece com o `django.contrib.auth` (apesar de no Django termos que habilitar e configurar as URLs do auth e templates). Com Breeze, rodando:
    
    ```bash
    composer require laravel/breeze --dev  
    php artisan breeze:install  
    npm install && npm run dev  (para assets, se aplicável)
    php artisan migrate  
    
    ```
    
    teremos um sistema de autenticação funcional ([Authentication in Laravel Using Breeze - Kinsta®](https://kinsta.com/blog/laravel-breeze/#:~:text=,Profile%20page%2C%20with%20editing)). Breeze por padrão usa Blade + TailwindCSS, integrando-se bem com Livewire também, se escolhida essa opção. É comparável a usar o `django-allauth` ou as views genéricas do próprio Django auth, porém com a vantagem de já trazer o front-end pronto com CSS.
    
-   **Implementação manual de login/logout:** Também podemos implementar sem usar um kit, entendendo as APIs do Laravel:
    
    -   Para **login**, Laravel fornece o facade `Auth` com método `attempt()`. Num controller, faríamos algo como:
        
        ```php
        if (Auth::attempt(['email' => $email, 'password' => $senha])) {
            // Sucesso: autenticação por sessão ocorrida
            return redirect()->intended('/dashboard');
        } else {
            return back()->withErrors(['login' => 'Credenciais inválidas']);
        }
        
        ```
        
        O `Auth::attempt` verifica as credenciais contra o provider (usuários) configurado e, se ok, inicia a sessão do usuário. Isso é semelhante a usar `authenticate()` e `login()` do Django (ex: `user = authenticate(request, ...); login(request, user)`). No Laravel é um passo só. Após login, `Auth::user()` ou `auth()->user()` retorna o usuário logado (similar a `request.user` no Django).
        
    -   Para **logout**, usamos `Auth::logout()` em uma rota de POST, e invalidamos a sessão (isso é análogo a chamar `logout(request)` no Django).
        
    -   Para **registro**, podemos criar um controller que valide os dados e chame `User::create([...])` para salvar (lembrando de usar `Hash::make($senha)` para criptografar a senha) – ou usar a facilidade do _Form Request_ para validação (ver seção de formulários). O Laravel já tem o hashing configurado e disponível via facade `Hash`, similar ao `make_password` do Django ou a configuração de password hasher lá.
        
-   **Protegendo rotas (login required):** No Django usamos decorador `@login_required` ou verificamos `request.user.is_authenticated` manualmente. No Laravel, podemos usar **middleware**. O middleware `auth` (já incluso) verifica se o usuário está logado na sessão. Aplicar ele a rotas ou controllers garante proteção. Por exemplo, no controller podemos ter:
    
    ```php
    class DashboardController extends Controller {
        public function __construct() {
            $this->middleware('auth');
        }
        // ...
    }
    
    ```
    
    Isso força todas as ações desse controller a requererem login. Ou diretamente no arquivo de rotas: `Route::middleware('auth')->get('/dashboard', ...)`. Caso não autenticado, o middleware redireciona para a rota de login automaticamente. Essa mecânica é configurável em `app/Http/Middleware/Authenticate.php`. No fundo, é parecido com o Django, mas ao invés de decorador, usamos middleware global ou por grupo de rotas.
    
-   **Recuperação de senha, lembrarme, etc.:** Usando Breeze ou Jetstream, já vem pronto. Manualmente, Laravel fornece o trait `Illuminate\Auth\Passwords\CanResetPassword` no modelo User e classes helper para e-mail de reset. Geralmente compensa usar o scaffold pronto a não ser que queira aprender implementando do zero. O recurso "remember me" (lembrar sessão) também existe via cookies longos (`Auth::attempt($credenciais, $remember = true)`).
    
-   **Django vs Laravel em Auth:** Ambos frameworks fornecem um sistema completo de autenticação. No Django, muito está “pronto” mas requer configurar as URLs e templates para as views de login/logout se quiser usar as padrão. No Laravel, nada impede você de escrever do zero, mas a comunidade oferece scaffolds (Breeze, Jetstream) para acelerar. Conceitos como usuário anon vs autenticado, verificação de e-mail, trocas de senha, tudo existe em ambos. Em Laravel há também o **Fortify** (back-end de auth headless, usado por Jetstream) e o **Passport/Sanctum** para autenticação de API (ver seção 10). Para um desenvolvedor experiente em Django, utilizar o Breeze no início pode dar um sistema similar ao que está acostumado, e então pode-se inspecionar o código gerado para aprender como o Laravel implementa cada parte.
    

## 6. Blade (motor de templates do Laravel)

**Blade** é o sistema de templates do Laravel, com funcionalidade semelhante ao engine de templates do Django (Django Template Language). A ideia central é a mesma: ter arquivos HTML com marcações especiais que serão substituídas por conteúdo dinâmico e lógica de apresentação. Vamos comparar e ver boas práticas:

-   **Sintaxe básica:** Blade usa `{{ ... }}` para exibir variáveis escapando conteúdo (proteção contra XSS por padrão, como o Django faz com `{{ ... }}`). Por exemplo, `{{ $user->name }}` em Blade equivale a `{{ user.name }}` no Django template. Para exibir texto sem escapar (uso cuidadoso), Blade tem `{!! ... !!}` (similar a `safe` filter do Django).
    
-   **Estruturas de controle:** Em Blade, usamos diretivas iniciadas com `@`. Exemplos:
    
    -   Condicional: `@if (condição) ... @elseif(...) ... @else ... @endif` (Django: `{% if condition %} ... {% elif ... %} {% else %} {% endif %}`).
        
    -   Loop: `@foreach($items as $item) ... @endforeach` (Django: `{% for item in items %} ... {% endfor %}`). Também há `@for`, `@while`, etc.
        
    -   Exibir algo se variável existe: `@isset($var) ... @endisset` (Django: `{% if var is not None %}` aproximadamente).
        
    -   Comentários: `{{-- comentário --}}` em Blade, vs `{# comentário #}` no Django.  
        Em resumo, o que é possível fazer em um, é possível no outro; muda a sintaxe. Blade tende a ser mais próximo de código PHP, enquanto Django template deliberadamente limita acesso direto a lógica Python (para manter separação).
        
-   **Herança de Layout:** Ambos frameworks suportam templates base e herança. No Django:
    
    ```django
    <!-- base.html -->
    <html><body>{% block content %}{% endblock %}</body></html>
    
    ```
    
    e em uma página filha:
    
    ```django
    {% extends "base.html" %}
    {% block content %} ... {% endblock %}
    
    ```
    
    Em Blade, é bem semelhante:
    
    ```blade
    <!-- layouts/app.blade.php -->
    <html><body>@yield('content')</body></html>
    
    ```
    
    e na view filha:
    
    ```blade
    @extends('layouts.app')
    @section('content')
      ... conteúdo ...
    @endsection
    
    ```
    
    Aqui `@extends` indica o layout base, e `@section('content')` define o conteúdo do placeholder `@yield('content')` do layout ([My experience of moving from Laravel to Django - Roman Sorin](https://romansorin.com/blog/my-experience-of-moving-from-laravel-to-django#:~:text=,in%20a%20Django)). O conceito é idêntico, apenas palavras-chave diferentes. **Boas práticas:** use layouts para evitar duplicação (ex: ter um layout para áreas logadas, um para públicas, etc.), como no Django.
    
-   **Inclusão de subtemplates:** Django usa `{% include "partial.html" %}`. Blade usa `@include('partial')`. Simples assim. Também há `@includeIf` (inclui só se o arquivo existe) e outras variações. Útil para componentes reaproveitáveis (ex: navbar, rodapé).
    
-   **Componentes Blade:** Além de includes, Laravel Blade (principalmente em versões recentes) oferece **Componentes de Template**, que são parecidos com incluir mas encapsulam melhor a marcação e permitem passar dados nomeados. Por exemplo, criar um componente `Alert` blade (arquivo em `resources/views/components/alert.blade.php` e opcionalmente uma classe PHP) para uso como `<x-alert type="error" message="..."/>`. Isso lembra **template tags customizadas** do Django ou **inclusão com context**. É um recurso avançado de Blade para organizar views.
    
-   **Seção de scripts/estilos:** Blade tem uma funcionalidade para empilhar seções de conteúdo. Ex: em layout base você pode ter `@stack('scripts')` e nas views filhas fazer `@push('scripts') <script>...</script> @endpush` para adicionar JS específico daquela página. No Django faríamos talvez blocks nomeados ou usar a lógica do template para decidir incluir scripts. Blade facilita com essa pilha.
    
-   **Dados e contexto:** No Django, o context passado no `render` contém as variáveis acessíveis no template. No Laravel, ao chamar `return view('nome', $dados)`, essas variáveis do array `$dados` ficam disponíveis no Blade. Além disso, Blade tem acesso a alguns dados globais: por exemplo, o usuário logado via `Auth::user()` pode ser exposto automaticamente (ou usar `@auth ... @endauth` bloco que renderiza só se usuário logado). O equivalente no Django seria usar `{{ user.is_authenticated }}` no template, que também está disponível globalmente se `django.contrib.auth` estiver habilitado e o contexto de template processadores incluso. Ou seja, em ambos há formas de obter dados globais (Laravel via facades ou injeção, Django via context processors).
    
-   **Comentários sobre performance:** Blade compila os templates para PHP puro na primeira vez (ou via comando `view:cache` no deploy) ([Deployment - Laravel 11.x - The PHP Framework For Web Artisans](https://laravel.com/docs/11.x/deployment#:~:text=the%20,your%20deployment%20process)), então a renderização é eficiente. No Django, os templates também são “compilados” em bytecode Python internamente e podem ser pré-carregados. Em geral, ambos são rápidos o suficiente para a maioria dos casos.
    
-   **Boas práticas Blade:**
    
    -   Mantenha a lógica complexa fora dos templates (mesma recomendação do Django). Blade permite chamar métodos PHP, usar qualquer função dentro das `{{ }}`, mas não abuse disso para não poluir a camada de apresentação. Ex: se precisar formatar data, Laravel oferece _helpers_ ou filtros (e.g. `$model->created_at->format('d/m/Y')` dentro de `{{ }}`) – isso é ok, mas cálculos pesados ou acesso a serviços externos não devem estar na view.
        
    -   Use partials ou componentes para não repetir código HTML. Exemplo: um card de postagem que aparece em vários lugares – crie um componente Blade `<x-post-card :post="$post" />` ao invés de duplicar o markup.
        
    -   Aproveite os **diretivas de csrf e método**: ao criar forms em Blade, sempre inclua `@csrf` dentro do `<form>` para gerar o token de proteção CSRF (Laravel injeta isso automaticamente em views, similar ao `{{ csrf_token }}` do Django). Se precisar fazer um form _fake_ de DELETE/PUT (HTTP verb não suportado nativamente em formulários HTML), use `@method('DELETE')` por exemplo, que Blade gera um input oculto informando o método para o Laravel interpretar – isso não tem paralelo no Django pois lá usamos somente métodos que o HTML suporta e encapsulamos deleção via POST.
        
    -   **Blade vs Django Template Language:** Em termos de poder, ambos se equiparam, com Blade tendo talvez algumas conveniências extras (diretivas para fazer include de svg inline, por exemplo, ou loops aninhados com índices etc.). Mas para um desenvolvedor Django, a curva de aprendizado do Blade é suave, pois os conceitos de variáveis, controle de fluxo e herança de template são os mesmos ([My experience of moving from Laravel to Django - Roman Sorin](https://romansorin.com/blog/my-experience-of-moving-from-laravel-to-django#:~:text=,in%20a%20Django)).
        

## 7. Eloquent ORM

O **Eloquent** é o ORM (Object-Relational Mapper) do Laravel. Assim como o Django ORM, ele permite manipular dados do banco de forma orientada a objetos, porém há diferenças de filosofia e sintaxe. Vamos explorar:

-   **Active Record vs Query Builder:** O Eloquent segue o padrão Active Record: cada modelo é ligado diretamente a uma tabela e instancia de modelo representa um registro. Você faz consultas estáticas no modelo ou via relacionamentos para obter dados. O Django ORM é mais do padrão Data Mapper + QuerySet: você obtém QuerySets e então avalia. Em Eloquent, por exemplo, para buscar todos posts: `Post::all()` retorna uma coleção de objetos Post. No Django: `Post.objects.all()` retorna um QuerySet (que é lazy, mas ao iterar você tem os objetos). Conceitualmente próximo, apenas a sintaxe `Post::all()` parece mais estática (mas internamente está chamando métodos da classe Model).
    
-   **Consultas (Querying):** Eloquent oferece métodos estilo _fluent interface_:
    
    ```php
    $ativos = User::where('active', true)->orderBy('name')->get();
    
    ```
    
    Isso gera algo como `SELECT * FROM users WHERE active = 1 ORDER BY name`. No Django fariamos: `User.objects.filter(active=True).order_by('name')`. Muito similar, apenas sintaxe adaptada para cada linguagem. No Eloquent podemos encadear `->where()->where()` (equivalente a múltiplos filter no Django, ou filter(Q & Q)). Também há `first()` (pega um resultado só), `find($id)` (busca por PK, similar a Django `get(pk=id)`), etc. O Eloquent retorna coleções customizadas (que implementam `Iterator`, `Countable` etc.), enquanto Django retorna QuerySet (também iterável).
    
-   **Ciclo de vida e Persistência:** Criar e salvar registros: no Django `obj = Model(field=val); obj.save()`. No Eloquent: `$obj = new Model; $obj->field = val; $obj->save();`. Bem parecido. Eloquent também tem o método `create([...])` que faz new + save em um passo (se os campos estão no `$fillable`). Para atualizar: `$obj->save()` após modificar propriedades, ou usar consultas para update em massa (`Model::where(...)->update([...])`). Deletar: `$obj->delete()` ou `Model::destroy(id)`. Tudo isso lembra o Django (save, update via QuerySet, delete).
    
-   **Relações entre modelos:** Eloquent facilita definir relacionamentos usando métodos na classe modelo:
    
    -   **One-to-Many:** no modelo `User` podemos ter método `posts() { return $this->hasMany(Post::class); }`. Isso estabelece que a chave estrangeira está em `posts.user_id`. Para o inverso, no modelo `Post`: `user() { return $this->belongsTo(User::class); }`. Depois podemos usar `$user->posts` (coleção) e `$post->user` (objeto) assim como no Django usamos `user.post_set.all()` ou atribuimos `post.user`. A diferença é que no Django definimos o ForeignKey no modelo Post e acessamos via atributos, enquanto no Laravel definimos métodos para explicitar a relação. Esses métodos retornam objetos do tipo Relation, e ao acessar como propriedade mágica `$user->posts` ele lazy load os posts. Podemos usar `with()` nas consultas para eager load (evitar N+1). Por exemplo: `User::with('posts')->get()` carrega usuários e seus posts já. No Django, usaríamos `select_related` ou `prefetch_related` para similar efeito.
        
    -   **One-to-One:** `hasOne` e `belongsTo` (ex.: User temOne Profile, Profile belongsTo User).
        
    -   **Many-to-Many:** no Eloquent, se User e Role tem MTM, definimos `roles() { return $this->belongsToMany(Role::class); }` em User e similar em Role. Precisamos de uma tabela pivô (ex: role_user) com user_id e role_id. Laravel deduz o nome da tabela pivô (ordem alfabética dos modelos) ou você especifica. No Django, definiríamos `roles = models.ManyToManyField(Role)` no User e o ORM cria tabela intermediária automaticamente. Ambos fazem a mesma coisa, mas no Laravel às vezes criamos o modelo do pivô se precisamos estender (similar ao `through=` no Django).
        
    -   **Lazy vs Eager:** Por padrão, `$user->posts` no Laravel faz consulta no momento do acesso (lazy). No Django, `user.post_set.all()` também faz na hora se não foi prefetch. Então ambos lazy por padrão. Laravel tem `load()` para carregar depois ou `with()` para antes, Django tem prefetch/ select_related.
        
-   **Consulta Raw e Query Builder:** O Eloquent permite misturar chamadas do _Query Builder_ (um componente de baixo nível) caso precise de algo específico sem mapear em objetos. Por ex, `DB::table('users')->where('active',1)->get()` retorna coleções de objetos stdClass com os dados. Similar a usar `cursor()` ou `RawSQL` no Django. Para maioria dos casos, Eloquent supre bem; para junções complexas ou agregações, pode-se usar funções do query builder ou até `DB::select('raw SQL', [...])`. No Django, usaríamos `extra()` (obsoleto), annotations ou diretamente `cursor().execute(...)` para SQL cru.
    
-   **Migrando conhecimento Django -> Eloquent:** Será relativamente tranquilo: conceitos de relações, métodos de filtragem, etc., são parecidos. Tenha em mente que no Laravel a classe Modelo _é ativa_ – você chama métodos estáticos para query e instâncias para manipular – enquanto no Django o modelo tem um Manager (`objects`) que faz queries. Mas o resultado final (lista de objetos, etc.) é igual. Exemplo de busca com relação: _Buscar posts de um usuário filtrando por status:_ Django: `Post.objects.filter(user=usuario, status='P')`. Laravel: `Post::where('user_id', $usuario->id)->where('status','P')->get()` ou via relação: `$usuario->posts()->where('status','P')->get()`. O segundo é bacana: `$usuario->posts()` dá um Builder já restrito ao usuário (similar a `usuario.post_set` no Django).
    
-   **Validação no modelo:** Diferentemente do Django (que pode usar `clean()` no model ou `ModelForm` para validação), o Eloquent não realiza validação automática ao salvar. A validação ocorre na camada de formulário/request (ver seção 8). Então, a responsabilidade do modelo Eloquent é principalmente mapear para o banco e fornecer métodos úteis (consultas de escopo, por exemplo).
    
-   **Escopos (Scopes):** Laravel permite definir métodos reutilizáveis de query dentro do modelo. Por exemplo, `public function scopeAtivos($query) { return $query->where('active',1); }`. Assim você pode chamar `User::ativos()->get()`. Django poderia conseguir semelhante com managers customizados ou métodos no QuerySet.
    
-   **Serialização:** Muitas vezes queremos serializar um queryset ou coleção. Laravel Eloquent permite converter para array ou JSON facilmente: `$posts->toArray()` ou mesmo `return $posts` em uma rota API já retorna JSON. O Django requer talvez usar `values()` ou um `serializers.serialize` ou DRF ModelSerializer. Em Laravel, existe também os **API Resources** para formatar a saída (ver seção 10).
    
-   **Performance e considerações:** Assim como no Django, é importante evitar N+1 queries (use eager loading), cuidar com consultas enormes (paginar resultados, etc.). O Eloquent não gera SQL muito diferente do Django ORM em complexidade; ambos cobrem JOINs, subqueries (Laravel com Query Builder, Django com Subquery/Exists, etc.). Benchmarks costumam mostrar o Django ORM um pouco mais rápido em operações intensivas de banco ([Django vs. Laravel: A Comparative Guide to Their ORMs](https://blog.stackademic.com/django-vs-laravel-a-comparative-guide-to-their-orms-63ba679fdb58#:~:text=Django%20vs,intensive%20operations)), mas isso raramente é limitante no nível de aplicação (I/O de banco normalmente domina o tempo). Em termos de escala, ambos ORMs aguentam bem milhares de registros – a escolha do banco e índices importa mais.
    

Em resumo, **o Eloquent ORM do Laravel oferece funcionalidade similar ao ORM do Django** (consulta conveniente, lazy loading, relações definidas, etc.), com diferenças principalmente na sintaxe e em quando/onde se definem os detalhes (migração vs modelo). O desenvolvedor Django deve se atentar para definir as migrações corretamente, já que não há o `makemigrations` para fazê-lo automaticamente; fora isso, consultando dados e navegando em relações, logo verá as semelhanças.

## 8. Formulários e validação

No Django, temos a poderosa biblioteca de formulários (`forms.py` com `forms.Form` e `forms.ModelForm`) que trata da renderização HTML e validação de dados de entrada. No Laravel, não existe uma classe de formulário equivalente no core – a abordagem é um pouco diferente, combinando Blade para o HTML do form e classes de Request ou validação manual no controller para validar. Vamos detalhar como lidar com formulários no Laravel:

-   **HTML dos formulários:** Em Laravel, você escreve o HTML do formulário diretamente nos templates Blade ou utiliza componentes para isso. Por exemplo, criar um formulário de cadastro: você escreveria em Blade os `<form action="..." method="POST">`, os inputs `type="text"` ou outros, e incluir `@csrf` dentro do form para proteger contra CSRF (Laravel injeta um token oculto, similar ao `csrf_token` do Django) – **Laravel protege formulários contra CSRF por padrão** sempre que usar a diretiva `@csrf` ([Deployment - Laravel 11.x - The PHP Framework For Web Artisans](https://laravel.com/docs/11.x/deployment#:~:text=The%20debug%20option%20in%20your,file)). Não há um helper nativo no core para gerar campos automaticamente (como `{{ form.as_p }}` no Django). Entretanto, existe um pacote opcional chamado **Laravel Collective** que fornece tags para forms e inputs, mas muitos desenvolvedores preferem escrever HTML puro ou usar frameworks CSS/JS que já fornecem componentes de form.
    
-   **Validação dos dados:** Aqui entra a principal diferença: o Laravel concentra a validação ou no _Controller_ ou em classes separadas chamadas **Form Requests**. Exemplo simples no controller:
    
    ```php
    public function store(Request $request) {
        $data = $request->validate([
            'titulo' => 'required|max:255',
            'email' => 'required|email',
            'idade' => 'nullable|integer|min:0'
        ]);
        // Se chegar aqui, os dados são válidos. (Senão, Laravel redireciona de volta com erros automaticamente)
        Model::create($data);
        return redirect('/sucesso');
    }
    
    ```
    
    O método `$request->validate([...])` aplica as regras e, em caso de falha, redireciona de volta à página anterior com os erros e os inputs antigos (flash data), similar a como o Django re-renderiza o form com erros se usamos `form.is_valid()` e falhar. As regras de validação são declarativas em strings (como acima) ou arrays, e existem muitas disponíveis (required, email, min, max, unique, etc.). Isso lembra as _validators_ do Django (`EmailField`, `MaxLengthValidator` etc.), mas em Laravel usamos uma sintaxe concisa na regra.
    
    -   **Form Request:** Para organizações maiores, podemos criar uma classe dedicada para validação usando `php artisan make:request StorePostRequest`. Essa classe (em `App\Http\Requests`) contém métodos `rules()` onde definimos as regras, e `authorize()` (para controle de autorização se necessário). No controller, em vez de `Request $request`, recebemos `StorePostRequest $request`. Laravel automaticamente valida antes de entrar no método, então podemos assumir que os dados estão válidos e obter com `$request->validated()`. Isso se assemelha a usar um `ModelForm` ou Form no Django, onde a validação está encapsulada na classe do formulário. **Analogamente**: o Form Request do Laravel = Form class do Django (em termos de responsabilidade).
        
    -   **Mensagens de erro:** Laravel vem com mensagens padrão (em inglês, ou traduzidas se configurar locale). Podemos customizar facilmente (no Form Request com método `messages()` ou no validator passando um array de msgs). No Django, temos `error_messages` nos campos ou mensagens globais via i18n.
        
    -   **Validação e Redirecionamento:** Por padrão, se a validação falha, o Laravel redireciona de volta e coloca os erros na sessão (flash) e os valores antigos. Em Blade, podemos acessar erros via a variável `$errors` (instância de MessageBag). Exemplo: para exibir erro de campo 'titulo', podemos colocar no form:
        
        ```blade
        @error('titulo')
            <div class="error">{{ $message }}</div>
        @enderror
        
        ```
        
        A diretiva `@error` exibe o bloco se houver erro para aquele campo, e `$message` contém a mensagem. No Django, faríamos algo como `{{ form.errors.field_name }}` ou usar `{{ form.field_name.errors }}` no template. Podemos também exibir erros gerais. Em Laravel, `{{ $errors->first('field') }}` pega a primeira mensagem, ou iterar em `$errors->all()` para exibir todas. Em suma, a exibição de erros é manual no Blade (ou usando componentes), mas é flexível.
        
-   **Preenchimento e manutenção de estado do form:** No Django, quando o form é re-renderizado após erro, os campos já vêm preenchidos com o valor submetido (`form.cleaned_data` ou `form.data`). No Laravel, usando o mecanismo de “old input”: Blade tem helper `old('campo')` que retorna o valor anterior submetido (se houver falha). Assim: `<input name="titulo" value="{{ old('titulo') }}">` garante que após validação falhar, o campo mostre o que o usuário digitou. Se for um form para editar existente, você pode usar `value="{{ old('titulo', $post->titulo) }}"` – se não tiver valor antigo de falha, usa o do objeto atual. Isso é semelhante a Django usar `form = Form(request.POST)` de volta no template, que mantém `form.data`. Novamente, manual no Laravel mas seguindo um padrão.
    
-   **Formulários baseados em modelo:** No Django, `ModelForm` facilita criar formulários a partir de modelos. No Laravel, não há um equivalente automático. Você precisa definir as regras de validação de acordo com seu modelo (duplicando um pouco o conhecimento do esquema). Pode-se argumentar que migrações e `$fillable` já definem o que é esperado, mas não gera form automaticamente. Muitos desenvolvedores Laravel criam requests de validação para suas entidades, ou usam pacotes se quiserem UI gerada. Contudo, com as ferramentas de Livewire (adiantando: no próximo tópico) é possível criar formulários de forma mais declarativa e interativa.
    
-   **Upload de arquivos:** O Laravel facilita via o objeto Request: `if($request->hasFile('avatar')) { $path = $request->file('avatar')->store('avatars'); }`. Isso move o arquivo para storage e retorna caminho. O Django tem `request.FILES` e campos FileField. Laravel não gera input file automaticamente, mas processa facilmente. Lembrar de colocar `enctype="multipart/form-data"` no form (mesmo detalhe do HTML).
    
-   **Validações customizadas:** Podemos criar regras custom (Laravel: usando `Validator::extend` ou objetos Rule) ou usar classes de _Rule_ existentes (ex: `Rule::unique('users')->ignore($user->id)` para "único exceto o próprio", em edição). No Django, escreveríamos métodos `clean_field` ou validators custom.
    
-   **Mensagens de sucesso/erro via sessão (Flash):** Tanto Laravel quanto Django possuem mecanismo de mensagens one-time. No Laravel, após salvar algo, podemos usar `session()->flash('status', 'Salvo com sucesso!')` e no Blade verificar `session('status')`. O Django tem `messages.add_message` ou `messages.success` e no template `{% if messages %}` loop. Ou seja, padrões similares para dar feedback ao usuário pós-form.
    

Resumindo, **no Laravel a construção de formulários é menos centralizada em uma classe única como no Django**. Você vai montar o HTML (ou usar componentes Blade), e validar no backend usando as ferramentas do framework. A validação é poderosa e comparável (até mais rica em algumas regras prontas, como `email`, `unique` etc.). Pode sentir falta de algo como `{{ form }}` para imprimir campos automaticamente, mas por outro lado, isso lhe dá controle total do HTML (o que muitos preferem, dado que frequentemente customizamos bastante aparência de formulários). Se realmente quiser algo similar a ModelForm, existem pacotes de terceiros, mas dominar o jeito "Laravel" de lidar com formulários é importante.

## 9. Componentização de views e uso de Livewire (e Vue.js, se aplicável)

Aplicações modernas frequentemente exigem interfaces dinâmicas (ex.: atualizações parciais de página, interação em tempo real com servidor). No Django, você talvez tenha usado JavaScript manual, **HTMX** ou Turbo para fazer chamadas AJAX e atualizar partes da página sem recarregar tudo. No Laravel, uma solução popular para isso é o **Livewire**, que permite construir componentes interativos no front-end usando apenas PHP e Blade, sem escrever JavaScript para a lógica de negócios da interface. Vamos entender como componentizar views e onde Livewire e também frameworks JS (como Vue.js) entram:

-   **Por que Livewire?** O Livewire é uma biblioteca (mantida oficialmente pelo ecossistema Laravel) que implementa uma comunicação _realtime_ entre componentes Blade e o servidor. Com ele, você cria componentes com uma classe PHP (que estende `Livewire\Component`) e uma view Blade dedicada. O Livewire cuida de, via AJAX, sincronizar estado e ações do componente com o servidor. Isso é análogo ao conceito do **HTMX** ou **Unpoly** no Django, onde você faz requisições AJAX a views e insere HTML parcial, porém Livewire integra a lógica de estado do componente de forma reativa. Para um dev Django: imagina poder escrever uma view que retorna um fragmento e automaticamente reatualizar só aquele fragmento quando o usuário interage, sem escrever JS custom – essa é a proposta do Livewire.
    
-   **Exemplo de Livewire:** Suponha um componente de contagem (counter) que incrementa ao clicar. Sem Livewire, faríamos um botão que via JS faz request e atualiza o número. Com Livewire, faríamos: `php artisan make:livewire Counter`. Isso gera `app/Http/Livewire/Counter.php` (classe com propriedade `$count` e método `increment`) e `resources/views/livewire/counter.blade.php` (view do componente). Na classe:
    
    ```php
    class Counter extends Component {
        public $count = 0;
        public function increment() { $this->count++; }
        public function render() { return view('livewire.counter'); }
    }
    
    ```
    
    No Blade do componente:
    
    ```blade
    <div>
      <button wire:click="increment">Incrementar</button>
      <h1>{{ $count }}</h1>
    </div>
    
    ```
    
    Ao incluir `<livewire:counter />` em alguma página, ele renderiza o HTML inicial (count=0). Quando o usuário clica no botão, o atributo `wire:click` dispara uma requisição AJAX chamando o método `increment` no servidor, que incrementa `$count` e re-renderiza o componente. O Livewire então diffs o HTML e atualiza apenas o necessário na página. Tudo isso sem recarregar e sem você escrever nenhuma linha de JS. **Analogia:** no Django poderíamos fazer algo semelhante com HTMX: um botão `<button hx-post="/increment" hx-swap="outerHTML">` que retorna um fragmento com o novo valor. A diferença é que Livewire mantém o estado ($count) no servidor automaticamente e re-renderiza usando a própria view Blade, enquanto com HTMX o dev tem que cuidar manualmente do estado e do template parcial.
    
-   **Quando usar Livewire:** É ideal para interfaces com interações complexas dentro de aplicações monolíticas Laravel, onde você quer evitar construir um front-end SPA separado. Exemplos: formulários multi-etapas com validação em tempo real, tabelas com filtros/sort/paginação dinâmicos, componentes como carrosséis, notificações em tempo real, etc., que você normalmente precisaria de JS para atualizar. Se sua aplicação Django já usou coisas como _django-ajax_ ou jQuery para submeter formulários via AJAX, Livewire te permite fazer o mesmo só que escrevendo em PHP a lógica de tratamento.
    
-   **Comparação com Vue.js/React:** Laravel também se integra bem com frameworks JavaScript (até tem o scaffolding Jetstream com Inertia + Vue ou React). A pergunta diz "Livewire ou Vue.js (se aplicável)". Em um projeto _full stack Laravel Blade_, a escolha comum é usar Livewire para não precisar manter duas aplicações (PHP e JS separadas). Vue.js ou React seriam se você quisesse construir um frontend mais desacoplado, ou uma SPA completa e o Laravel só como API (ver seção 10 sobre APIs). Livewire, por outro lado, permite continuar renderizando no servidor (SEO, performance inicial) e ainda assim ter interatividade. Para um dev Django, Livewire pode lembrar **Alpine.js** ou **Stimulus + AJAX** – de fato, Livewire é frequentemente usado em conjunto com Alpine.js para pequenos comportamentos do lado do cliente (Alpine é um mini framework JS declarativo). Vale citar: Livewire V3 (versão recente) agora inclui por baixo dos panos o **Turbo.js** para otimizações e está ainda mais fluido.
    
-   **Componentização de Views no Laravel (fora Livewire):** Mesmo sem Livewire, é boa prática componentizar suas views Blade. Podemos criar componentes Blade (já mencionado na seção 6) para partes repetidas. E o Laravel incentiva organizar a UI em componentes reutilizáveis, assim como no Django poderíamos criar _inclusion tags_ ou templates parciais. Em projetos grandes, às vezes divide-se a aplicação em módulos (pacotes) que incluem seus próprios views e rotas – semelhante a apps Django.
    
-   **Comunicação entre componentes:** Livewire permite componentes aninhados e troca de informações via _events_. Por exemplo, um componente filho emite um evento (ex: "produtoAdicionado") e um componente pai ou ouvinte escuta e reage. Isso é comparável a emitir eventos JS e ouvir no DOM, mas acontece no servidor via Livewire. Para dev Django, isso não tem um paralelo direto, já que Django tradicional não mantém estado no servidor após responder (exceto via canais WebSocket). A propósito, Livewire também suporta interagir com **broadcasting** (ex: receber eventos via WebSockets do servidor e atualizar componente, útil para notificações em tempo real), coisa que no Django faríamos via Django Channels e JS do front.
    
-   **Estado e Sessão:** Livewire mantém o estado dos componentes em sessão/cookies de forma transparente entre requisições AJAX. Isso significa que se você atualizar a página, aquele estado some (a verdade está no servidor). Já uma SPA em Vue manteria estado no cliente (Vuex/store). São abordagens diferentes. Em termos de usabilidade, Livewire facilita muito casos de CRUD simples sem recarga e feedback instantâneo.
    
-   **Exemplo prático:** Imagine um formulário de criação de ordem com produtos dinâmicos. Com Livewire, você pode adicionar campos de produtos de forma dinâmica (um botão "Adicionar item" que via Livewire insere outro bloco de inputs para produto sem reload). O componente Livewire pode validar cada item em tempo real, calcular totais conforme o usuário digita, etc. Tudo isso com código PHP no componente e Blade reagindo. Em Django, isso exigiria bastante JavaScript custom ou o uso de formsets (que não oferecem essa interatividade sem JS).
    
-   **Livewire vs HTMX:** Para quem conhece HTMX (biblioteca JS usada em Django por alguns para interatividade server-driven), a diferença é Livewire ser mais integrado: você trabalha do lado servidor com estado persistente entre requisições, enquanto HTMX faz requisições discretas e substituições de HTML. Ambos evitam escrever muito JS manual. Se você gostou do conceito do HTMX no Django, provavelmente vai gostar de Livewire no Laravel, pois ele segue a mesma filosofia de _“traga a lógica dinâmica para o backend”_.
    
-   **Vue.js (se aplicável):** O Laravel não obriga um ou outro – você pode usar Livewire _e_ também usar Vue.js para componentes específicos. Porém, se você planeja uma interface altamente interativa, talvez considere usar **Inertia.js** com Vue ou React: Inertia permite usar Laravel para rotas e controllers, mas renderizar componentes Vue como páginas, sem usar Blade para a estrutura principal. É quase um meio-termo entre SPA e multi-page. Ainda assim, dado o escopo do roadmap (Blade e Livewire), a ideia é mostrar que **você pode atingir muita interatividade só com Laravel + Blade + Livewire**. Isso diminui a complexidade de ter API + frontend separado.
    

Em resumo, **componentizar as views** no Laravel significa isolar partes reutilizáveis (com Blade components) e usar ferramentas como **Livewire para adicionar interatividade sem escrever JS**. Para um dev Django, é como se o “template” pudesse ter métodos de backend sendo chamados via AJAX automaticamente – o Livewire cuida dessa ponte. É uma tecnologia que vale a pena estudar no contexto Laravel moderno, pois aumenta muito a produtividade em apps fullstack pequenas e médias. (Claro, para apps do porte de um SPA gigante, frameworks JS dedicados podem ser mais indicados, mas aí Laravel atuaria mais como API, que tratamos a seguir.)

## 10. Criação de APIs REST com Laravel (APIs RESTful e Laravel Sanctum)

Quando falamos em construir APIs REST com Laravel, o framework fornece recursos para criar endpoints JSON de forma simples. Não existe um “Laravel REST framework” separado como o Django REST Framework; em vez disso, o próprio Laravel te dá as ferramentas essenciais. Vamos ver como criar APIs e como autenticar com Sanctum, comparando com a experiência de usar DRF no Django.

-   **Rotas de API:** Laravel já possui por convenção o arquivo `routes/api.php`. Rotas definidas ali são automaticamente prefixadas com `/api` e aplicam o middleware `api` (que inclui controle de rate limit e usa um guard de autenticação próprio). Por exemplo, podemos definir em `api.php`:
    
    ```php
    Route::get('/posts', [PostController::class, 'index']);
    Route::get('/posts/{id}', [PostController::class, 'show']);
    Route::post('/posts', [PostController::class, 'store'])->middleware('auth:sanctum');
    
    ```
    
    Aqui estamos criando endpoints GET list, GET detalhe e POST create para posts. Repare que podemos aplicar middleware `auth:sanctum` na rota POST para exigir autenticação (usando Sanctum, falaremos mais). Isso lembra bastante o DRF, onde definimos ViewSets ou APIViews e aplicamos permissões. No Laravel, não há uma classe especial para API; podemos usar os mesmos Controllers (talvez separados em namespace API). O retorno do controller deve ser JSON (ou resposta adequada). Se o método do controller retornar um array ou coleção, o Laravel automaticamente serializa para JSON. Alternativamente, podemos usar a facade `response()->json($data)` para clareza.
    
-   **Controladores de API:** Podemos criar controllers dedicados para API. Por convenção, algumas vezes se cria por exemplo `app/Http/Controllers/Api/PostController` separado do controller que retorna views. É possível até usar **Resource Controllers** (Artisan `make:controller PostController --api`) para gerar métodos index, show, store, update, destroy. A lógica interna seria parecida com DRF ViewSets, mas sem as camadas de Serializers e Response classes – você apenas retorna dados/objetos e o framework converte.
    
-   **Serialização de dados:** No Django REST, definimos Serializers para transformar modelos em JSON e validar input. No Laravel, podemos fazer manualmente (ex: retornar `$post->toArray()` ou construir um array com campos desejados). Porém, Laravel oferece **API Resources (Resource Objects)** para formatação consistente. Podemos criar um Resource: `php artisan make:resource PostResource`. Isso gera `App\Http\Resources\PostResource` contendo um método `toArray($request)` onde definimos como transformar o modelo em array ([Laravel for Beginners: API Resources - Lucky Media](https://www.luckymedia.dev/blog/laravel-11-for-beginners-api#:~:text=Laravel%20for%20Beginners%3A%20API%20Resources,the%20structure%20of%20each)). Por exemplo:
    
    ```php
    return [
      'id' => $this->id,
      'titulo' => $this->titulo,
      'conteudo' => $this->conteudo,
      'autor' => new UserResource($this->user), // nesting resource
      'created_at' => $this->created_at,
    ];
    
    ```
    
    Então no controller, podemos fazer: `return new PostResource($post);` ou para coleção: `return PostResource::collection($posts);`. O Laravel então retorna JSON com esse formato. Isso é muito similar aos Serializers do DRF (ModelSerializer etc.), cumprindo o papel de camada de transformação ([Eloquent: API Resources - Laravel 12.x - The PHP Framework For ...](https://laravel.com/docs/12.x/eloquent-resources#:~:text=,php%20artisan%20make%3Aresource%20UserCollection)). A vantagem é separar a apresentação da API da lógica, evitando expor campos indesejados ou formato diferente. É opcional, mas recomendado em APIs não triviais.
    
-   **Autenticação de API (Laravel Sanctum):** O **Laravel Sanctum** é a solução leve para autenticar APIs e SPAs. Ele suporta dois modos: _tokens de API_ (gerados para uso tipo mobile ou scripts) e _autenticação de SPA usando cookies/sessão_. Em ambos os casos, Sanctum é fácil de implementar. Precisamos instalar (`composer require laravel/sanctum`) e rodar migração (cria tabela `personal_access_tokens`).
    
    -   **Token de API:** Podemos permitir que o usuário logado gere um token via endpoint ou tinker. Esse token pode ser enviado nos headers (Authorization: Bearer ). Sanctum valida esse token e identifica o user. A proteção das rotas acontece com middleware `auth:sanctum`. Isso é similar ao Django REST TokenAuth ou JWT. Sanctum tokens são revogáveis e podemos escopar permissões. Ex: `Auth::user()->createToken('nome')->plainTextToken` gera um token. No cliente, usar esse token.
        
    -   **SPA Cookie:** Quando o front-end é no mesmo domínio (ex: uma SPA Vue hospedada na mesma origem do Laravel, ou até Blade+AJAX), podemos usar Sanctum para login via sessão: o usuário faz login normal (com CSRF protegido), Sanctum emite um cookie de sessão e um cookie XSRF. As próximas requisições XHR carregam automaticamente o cookie de sessão, então o middleware `auth:sanctum` reconhece o usuário (pois está logado na sessão, cookie). É uma forma segura de autenticar SPAs sem expor tokens via JS, aproveitando cookies HTTP-Only. O Sanctum faz um meio-termo entre sessão e API. **Comparação:** No Django, para uma SPA React no mesmo domínio, poderíamos usar `SessionAuthentication` do DRF, com o usuário obtendo cookie session via login normal. Sanctum facilita isso com um endpoint dedicado `/sanctum/csrf-cookie` e instruções de uso ([Laravel Sanctum - The PHP Framework For Web Artisans - Laravel](https://laravel.com/docs/12.x/sanctum#:~:text=Laravel%20Sanctum%20provides%20a%20featherweight,and%20simple%2C%20token%20based%20APIs)).
        
    -   Sanctum é descrito como _"um sistema de autenticação leve para SPAs, mobile e APIs token-based"_ ([Laravel Sanctum - The PHP Framework For Web Artisans - Laravel](https://laravel.com/docs/12.x/sanctum#:~:text=Laravel%20Sanctum%20provides%20a%20featherweight,and%20simple%2C%20token%20based%20APIs)). Exatamente o caso de uso dele. Para um dev Django: imagine que o DRF TokenAuth e SessionAuth fossem unificados e melhorados – é o Sanctum.
        
-   **Permissões e Authorization:** No DRF, temos `IsAuthenticated`, `IsAdminUser`, etc., e podemos criar classes de permissão. No Laravel, podemos usar middleware (ex: `middleware('auth')` já exige login). Para roles mais específicas, podemos definir _policies_. Policies são classes que encapsulam autorização por modelo. Ex: `php artisan make:policy PostPolicy --model=Post` geraria métodos `view`, `create`, `update`, etc., onde podemos escrever lógica (ex: `return $user->id === $post->user_id;` para update). Registramos a policy e então podemos usar métodos do Gate no controller ou – mais elegante – usar _middleware de policy_: Laravel permite mapear automaticamente: `Route::delete('/posts/{post}', [PostController::class,'destroy'])->middleware('can:delete,post');`. Isso checa usando PostPolicy@delete. Bem similar aos DRF `permission_classes` ou Django normal com decorators de permission. Também existe pacote Laravel Permission (Spatie) para roles/permissions no DB.
    
-   **Throttling (limite de requisições):** O middleware `api` já inclui por default um limitador de 60 req/min por usuário (ou IP se sem auth). Podemos configurar no `RouteServiceProvider` ou nas rotas (`->middleware('throttle:nome')`). No DRF, temos _throttling classes_.
    
-   **Versionamento de API:** Laravel não impõe, mas podemos prefixar rotas ou usar subdomínios para versões. No Django DRF, geralmente organizamos via URLs (v1/, v2/). Em Laravel, é comum definir grupo de rotas com prefix 'v1' e namespace de controllers v1. Ou usar packages (Swagger, etc.).
    
-   **Resposta e códigos HTTP:** Em Laravel, usamos `abort(404)` para not found (lança exceção HttpException que o framework trata e retorna JSON de erro se é API route). Ou retornar manualmente `response()->json(['error'=>'msg'], 422)`. Podemos também usar Exceptions custom e registrar renderização condicional (por exemplo, diferenciar se é request API ou web). Geralmente, convém capturar validação falha no Form Request e retornar erros JSON apropriados (Laravel faz algo auto: se for AJAX, retorna 422 com errors em JSON). No DRF, ValidationError vira resposta 400 com JSON automatic. Em Laravel, se usando Form Request, ele redirecionaria (pensando em web). Uma dica: no Form Request, podemos sobrescrever `failedValidation` para retornar JSON. Ou checar `$request->wantsJson()`. Alternativamente, se preferir, há o **Laravel Fortify** que lida com isso de forma headless para auth endpoints.
    
-   **Documentação (OpenAPI/Swagger):** No Django DRF, usamos drf-yasg ou coreapi. No Laravel, há bibliotecas como **Laravel Sanctum** e **Swagger-PHP** para gerar docs. Não coberto no core, mas fácil integrar.
    
-   **Laravel como backend API puro:** Vale notar que muitas vezes usam Laravel só como API (sem Blade). Nesse caso, provavelmente não usaria Livewire ou Blade, e sim um front separado (React/Vue/Angular). O Laravel até tem uma config para dizer que a app é api only (no `config/auth.php` usar guard api etc.). Mas mesmo numa app Blade, nada impede de ter endpoints APIs para alguma funcionalidade (ex: fornecer dados para um componente front-end mais rico).
    

Em resumo, **Laravel facilita criar APIs** com rotas e controllers regulares. **Sanctum** provê autenticação _stateless_ (token) ou _session-based_ para essas APIs de forma muito integrada ao framework. A experiência difere do Django DRF, que tem muitas classes e camadas – no Laravel você escreve mais “simples”, porém a responsabilidade de garantir formatos corretos e validação fica por sua conta (com ajuda de Resources e Form Requests). Para um projeto novo, isso é bem rápido. Por exemplo, **um CRUD completo de API pode ser escrito em poucas linhas usando Resource controllers e API Resources**, enquanto no DRF teríamos Serializers, ViewSets, Routes. Ambos oferecem tudo necessário; Laravel opta pela simplicidade e permite customizar conforme demanda.

## 11. Testes (unitários, de integração e end-to-end)

Testar sua aplicação Laravel é tão importante quanto no Django. Laravel vem com suporte embutido a testes usando PHPUnit (por padrão) e opcionalmente Pest (uma sintaxe alternativa). Vamos ver como estruturar os testes e comparações com unittest/pytest/Django TestCase.

-   **Configuração inicial de testes:** Ao criar um projeto Laravel, a pasta `tests/` já contém dois exemplos: `Feature/ExampleTest.php` e `Unit/ExampleTest.php`. PHPUnit está configurado via `phpunit.xml`. Você roda os testes com `php artisan test` (atalho para executar PHPUnit, já configurado) ([Testing: Getting Started - The PHP Framework For Web Artisans](https://laravel.com/docs/12.x/testing#:~:text=After%20installing%20a%20new%20Laravel,Environment)). Isso é similar a rodar `python manage.py test` (que por padrão usa unittest ou pytest se configurado). O Laravel também suporta executar testes em paralelo facilmente (`php artisan test --parallel`).
    
-   **Tipos de teste:** Laravel distingue _Unit Tests_ e _Feature Tests_:
    
    -   _Unit Tests_ devem testar unidades isoladas (classes, métodos) sem contexto do framework, usando mocks se necessário – semelhante a tests de unidades no Django sem usar a base de dados ou stack web.
        
    -   _Feature Tests_ (ou testes de integração) podem fazer chamadas simulando requisições HTTP e checar respostas, tocando banco de dados, etc. Eles carregam a aplicação Laravel completa (como Django TestCase que usa a database de teste). Em Django chamamos tudo de TestCase ou usamos pytest splitting by directory, mas a ideia é a mesma.
        
-   **Escrevendo testes de integração:** Laravel fornece uma classe base `Tests\TestCase` que configura a aplicação. Podemos usar métodos como `$this->get('/rota')` para simular uma requisição GET e obter a resposta, depois verificar conteúdo ou status. Ex:
    
    ```php
    public function testDashboardRequiresAuth() {
        $response = $this->get('/dashboard');
        $response->assertRedirect('/login');
    }
    
    ```
    
    Isso verifica que acessar /dashboard sem login redireciona para /login. Em Django, faríamos algo com `self.client.get('/dashboard/')` e `self.assertRedirects(response, '/login/')`. Bem parecido. Outro:
    
    ```php
    $response = $this->actingAs($user)->post('/posts', ['titulo'=>'Test', 'conteudo'=>'Lorem']);
    $response->assertStatus(201);
    
    ```
    
    `actingAs` autentica o usuário fornecido (similar a `client.force_login(user)` no Django). E podemos verificar status HTTP, ver se no banco foi criado registro etc. Laravel tem muitos métodos de asserção: `assertSee` (ver texto HTML), `assertJson` (comparar JSON retornado), `assertDatabaseHas` (verificar que há um registro no banco com certas colunas – recurso super útil que checa direto na test database). Esse último não existe nativamente no Django, mas podemos manualmente consultar modelo para verificar.
    
-   **Banco de dados nos testes:** Por padrão, Laravel testes de Feature usam a database especificada em `phpunit.xml` (tipicamente sqlite :memory: ou outro). Ele automaticamente migra antes de rodar? Não exatamente – usualmente usamos a trait `RefreshDatabase` nos testes para migrar o schema no início de cada teste (isso garante isolamento, semelhante a Django que cria um DB de teste e aplica migrations). A trait cuida de rodar migrations ou transações conforme configurado. No Django, o test runner cria um DB de teste e destrói ao fim; no Laravel, `RefreshDatabase` recria/trunca as tabelas para cada teste ou usa transação se suportado. O uso é:
    
    ```php
    class PostTest extends TestCase {
        use RefreshDatabase;
        public function testCriaPost() {
           // ...
        }
    }
    
    ```
    
    Isso limpa o DB antes de cada teste. Alternativamente, há `DatabaseMigrations` trait que roda migrate:refresh em cada teste. Para grandes suites, há considerações de performance (pode-se migrar apenas uma vez antes da suite e usar transações). No geral, similar ao Django, onde por padrão cada TestCase é transacional ou recria DB.
    
-   **Mocks e fakes:** Laravel integra o Mockery (framework de mock do PHPUnit) para simular dependências. Também tem “fakes” para coisas como filas (jobs), eventos, mail, etc. Ex: `Mail::fake()` impede envio real de email e permite verificar que um email foi enviado via `Mail::assertSent(ClasseEmail::class)`. No Django, usaríamos `mail.outbox` ou libs como pytest-django mailbox. Para filas, `Bus::fake()` similarmente. Essa facilidade de fakes está bem integrada no Laravel.
    
-   **Testes unitários simples:** Para funções puras ou classes utilitárias, pode usar PHPUnit como de costume: `$this->assertEquals( expected, actual )` etc. Isso não envolve o framework todo, são rápidos. Equivalente a Django tests usando unittest.TestCase sem DB.
    
-   **Pest PHP:** Além do PHPUnit, muitos na comunidade usam Pest, que é um DSL para testes em PHP que deixa a sintaxe mais próxima de JS/Python (funções globais em vez de métodos de classe). Não é obrigatório, mas se ver `it('descrição')->assertTrue(...);` é Pest, não estranhe. O Laravel 9+ suporta Pest facilmente.
    
-   **Testes end-to-end (E2E):** Para testes de navegador reais (clicar botões, etc.), o Laravel oferece o **Dusk**, que é uma ferramenta que usa o ChromeDriver para automatizar o browser ([Laravel Dusk - Laravel 12.x - The PHP Framework For Web Artisans](https://laravel.com/docs/12.x/dusk#:~:text=Artisans%20laravel,or%20Selenium%20on%20your)). Com Dusk, você escreve testes estilo "visit page, click, assert see text" de forma fluente. Por exemplo:
    
    ```php
    $browser->visit('/login')
            ->type('email', 'user@example.com')
            ->type('password', 'secret')
            ->press('Login')
            ->assertPathIs('/dashboard');
    
    ```
    
    Isso roda num navegador headless. Dusk lembra o Selenium ou Playwright. No Django, não há uma ferramenta oficial integrada tão user-friendly para E2E (há LiveServerTestCase com Selenium, mas é mais cru). Então Dusk é um ponto a mais do Laravel para E2E. Claro, podemos usar qualquer ferramenta externa (Cypress, etc.) para testar tanto Laravel quanto Django.
    
-   **Cobertura de teste:** Podemos gerar coverage com PHPUnit Xdebug, similar a usar coverage.py no Django.
    
-   **Teste de APIs:** Para endpoints JSON, Laravel tem asserções específicas, como `->assertJsonStructure([...])` para verificar o formato do JSON, `->assertJsonFragment(['key'=>'value'])` para checar um pedaço. No Django DRF, usaríamos `self.assertEqual(response.json(), expected)` ou utilidades do DRF testing.
    
-   **Continuous Integration:** Nada impede usar GitHub Actions, CircleCI etc. O Artisan test retorna código de saída adequado; integração CI de Laravel é straightforward como no Django (ensuring env, DB config etc.).
    
-   **Tinker e shell nos testes:** No Django podemos abrir `manage.py shell` e manipular objetos para inspeção. Em Laravel, temos o `php artisan tinker` ([My experience of moving from Laravel to Django - Roman Sorin](https://romansorin.com/blog/my-experience-of-moving-from-laravel-to-django#:~:text=Both%20Laravel%20and%20Django%20have,you%20want%20to%20interact%20with)), que abre um REPL (usando PsySH). Podemos ali criar usuários, debug, etc., inclusive durante desenvolvimento de testes para ver comportamentos. Tinker é poderoso e carrega autoload, ao contrário do shell do Django que precisa de imports manualmente ([My experience of moving from Laravel to Django - Roman Sorin](https://romansorin.com/blog/my-experience-of-moving-from-laravel-to-django#:~:text=Both%20Laravel%20and%20Django%20have,you%20want%20to%20interact%20with)).
    

Em suma, **Laravel possui um ecossistema de testes completo**. A experiência de escrever testes de unidade e integração será familiar para quem já escreveu testes no Django: criar dados (no Laravel, usar modelos ou _factories_ Faker integradas), chamar a funcionalidade (um método, uma rota via $this->get(), etc.) e verificar o resultado (estado no banco, resposta HTTP, output esperado). Ambos frameworks incentivam testes. Talvez a diferença está em algumas conveniências do Laravel como asserções prontas (ex: `assertDatabaseHas` economiza escrever consulta pra verificar existência de registro) e a separação unit/feature. O Django com pytest-django chega perto em ergonomia. De qualquer forma, um desenvolvedor experiente em Python/Django não terá dificuldades em adotar PHPUnit e construir uma boa suíte de testes para garantir a qualidade do aplicativo Laravel.

## 12. Deploy da aplicação (em ambiente de produção)

Publicar sua aplicação Laravel em produção envolve algumas etapas semelhantes ao deploy de uma aplicação Django, mas adaptadas ao ambiente PHP. Vamos cobrir configuração de servidor, uso do `.env`, otimizações de performance e comparar com práticas de deploy do Django.

-   **Servidor Web (Apache/Nginx) e PHP-FPM:** Diferente do Django, que normalmente roda com Gunicorn/Uvicorn (WSGI/ASGI) atrás de um Nginx, no Laravel a aplicação roda sobre servidores HTTP nativos (Apache ou Nginx) com módulo PHP ou PHP-FPM. Em produção, configura-se geralmente o Nginx para repassar requisições PHP para o processo PHP-FPM. Por exemplo, no Nginx:
    
    ```nginx
    root /var/www/sua-app/public;
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
    
    ```
    
    Isso garante que todas requisições entrem pelo `public/index.php` (front controller do Laravel), exceto arquivos estáticos existentes em `public` (ex: CSS, imagens). No Apache, seria habilitar `mod_php` ou usar proxy fcgi. O importante: **o docroot deve ser apontado para `public/`**, não à raiz do projeto, por segurança (assim, arquivos sensíveis como .env ou código não ficam acessíveis via HTTP) ([Laravel Series Part 1 — Environment Setup & Laravel Installation | by Tharjalan Jeyaradnam | Medium](https://medium.com/@tharju004/laravel-series-part-1-environment-setup-laravel-installation-d67257751d6c#:~:text=If%20you%20want%20to%20view,is%20running%20on%20the%20server)). Essa configuração de servidor cumpre o papel de levantar o Django via WSGI. Em serviços gerenciados (PaaS) como Heroku, Vapor, etc., isso é abstraído.
    
-   **Variáveis de Ambiente (.env):** Em produção, você terá um arquivo .env com settings específicos (APP_ENV=production, APP_DEBUG=false, keys, credenciais banco, etc.). Certifique-se de **desligar o debug** (`APP_DEBUG=false`) para não expor stack trace sensível aos usuários ([Deployment - Laravel 11.x - The PHP Framework For Web Artisans](https://laravel.com/docs/11.x/deployment#:~:text=The%20debug%20option%20in%20your,file)). Isso equivale a `DEBUG=False` no Django settings. O `.env` não deve ser exposto e idealmente as variáveis poderiam ser definidas no sistema (o Laravel lê do .env e popula `$_ENV`). Muitas vezes, monta-se o .env durante o CI/CD preenchendo valores de secrets.
    
-   **Instalação de dependências:** No deploy, rodamos `composer install --no-dev --optimize-autoloader` para instalar pacotes sem os de desenvolvimento e otimizar o autoloader PSR (carregamento de classes mais eficiente). Esse passo é análogo a rodar `pip install -r requirements.txt` e eventuais otimizações de bytecode (no PHP, o OPcache já cuida de cache de bytecode). Falando nisso, **habilitar OPcache** no PHP de produção é crucial para performance (evita recompilar os scripts a cada request). A maioria dos hosts já habilita por padrão em `php.ini` (equivalente a usar PyPy or cache de bytecode .pyc no Python, que já acontece).
    
-   **Migrações e Seeding:** Assim como no Django precisamos rodar migrations na produção (`manage.py migrate`), no Laravel rodamos `php artisan migrate --force`. O `--force` é para rodar fora do ambiente local (evita prompt de confirmação). Se tiver seeders necessários, rodar `php artisan db:seed`. Em sistemas zero-downtime, considere estratégias de migração segura (Laravel suporta _migrations rollback_ e você pode usar _maintenance mode_ durante migração se for alterar algo crítico).
    
-   **Otimizações específicas do Laravel:** O Laravel oferece comandos Artisan para cache de configuração, rotas e views, consolidando-os em arquivos únicos para desempenho:
    
    -   `php artisan config:cache` – combina todos configs em um só cache, reduzindo acessos a disco para carregar config ([Deployment - Laravel 11.x - The PHP Framework For Web Artisans](https://laravel.com/docs/11.x/deployment#:~:text=This%20command%20will%20combine%20all,when%20loading%20your%20configuration%20values)). (Lembre-se de rodar novamente se mudar .env ou config files em deploys futuros.)
        
    -   `php artisan route:cache` – compila todas rotas em um único método, acelerando resolução de rotas (bom para apps com muitas rotas) ([Deployment - Laravel 11.x - The PHP Framework For Web Artisans](https://laravel.com/docs/11.x/deployment#:~:text=If%20you%20are%20building%20a,command%20during%20your%20deployment%20process)). Obs: rotas com closures não serão cacheadas, então em produção use controllers para todas rotas.
        
    -   `php artisan view:cache` – pré-compila os Blade templates em arquivos PHP, evitando custo de compilação on-demand ([Deployment - Laravel 11.x - The PHP Framework For Web Artisans](https://laravel.com/docs/11.x/deployment#:~:text=the%20,your%20deployment%20process)). Semelhante a “pré-renderizar” templates; Django faz lazy compila mas não tem comando direto, embora possamos usar cached template loader.
        
    -   `php artisan optimize` – roda todos acima de uma vez ([Deployment - Laravel 11.x - The PHP Framework For Web Artisans](https://laravel.com/docs/11.x/deployment#:~:text=When%20deploying%20your%20application%20to,of%20your%20application%27s%20deployment%20process)). Deve ser executado no deploy após instalar dependências e antes de começar a servir ([Deployment - Laravel 11.x - The PHP Framework For Web Artisans](https://laravel.com/docs/11.x/deployment#:~:text=When%20deploying%20your%20application%20to,of%20your%20application%27s%20deployment%20process)).
        
    -   Você também pode cachear _events_ (`event:cache`) e _facades discovery_ etc., mas os principais são config/route/view ([Deployment - Laravel 11.x - The PHP Framework For Web Artisans](https://laravel.com/docs/11.x/deployment#:~:text=Caching%20Routes)).
        
    -   Para limpar caches (durante troubleshooting ou novo deploy): `php artisan optimize:clear` limpa tudo (config, route, view) ([Deployment - Laravel 11.x - The PHP Framework For Web Artisans](https://laravel.com/docs/11.x/deployment#:~:text=The%20,in%20the%20default%20cache%20driver)).
        
-   **Assets (CSS/JS):** Se você usou SCSS/JS com build (Webpack/Mix ou Vite no Laravel 9+), rode a build de produção (ex: `npm run build`). Isso gerará assets minificados em `public/build` ou similar. Configure para servir arquivos estáticos diretamente pelo servidor web (Nginx já vai fazer isso pois `public/` é exposto). No Django, equivalentes seriam rodar `collectstatic` e servir via Nginx. No Laravel, não há um collectstatic porque já estão em `public/`. Apenas certifique de não servir arquivos sensíveis.
    
-   **Segurança:**
    
    -   Defina uma APP_KEY (Laravel já gera uma no .env ao criar projeto). Em produção essa key deve estar setada e segura, pois é usada para criptografia de sessões e dados. Equivale à SECRET_KEY do Django.
        
    -   Verifique permissões de arquivos e pastas: `storage/` e `bootstrap/cache/` precisam ser graváveis pelo processo do web server. No Django, análogo seria garantir permissão de escrita em diretórios de media ou logs se usados.
        
    -   Desative listagem de diretório no servidor web e outras exposições. Se configurado certo docroot, `public/` só tem index.php e assets, nada mais. (No Django, normalmente Nginx configura para servir /static e /media, e proxy pro WSGI para o resto).
        
    -   Ative HTTPS no servidor web, use configs de segurança habituais (headers, etc.). Laravel tem Middleware para forçar HTTPS (EnsureHttps).
        
    -   Monitore erros: em prod, com debug false, erros são logados em `storage/logs/laravel.log`. Use ferramentas tipo Sentry (existe sdk Laravel) se precisar, assim como faríamos com Django Sentry.
        
-   **Deploy Zero-downtime:** Ferramentas como Laravel Envoy ou Deployer.php podem orquestrar deploys com zero downtime (criar pastas release, swap symlink). Similar ao usar Fabric/Ansible ou Git CI for Django. Laravel Forge (serviço SaaS) automatiza muitos passos de deploy em VPS, assim como Zappa/Elastic Beanstalk podem fazer pra Django.
    
-   **Escalabilidade:** Em produção sob carga, considere usar cache de sessão e cache de dados em Redis/Memcached em vez de arquivo. Laravel por padrão salva sessões em arquivo (storage/framework/sessions). Mude `SESSION_DRIVER=redis` por ex., para performance e facilitar escalabilidade (vários servidores web compartilhando sessão). No Django, usaríamos cache session or DB session accordingly. Configurar um cache driver (Redis) e usar `Cache` facade em partes críticas para cache de queries, etc., também ajuda (Django similar com cache framework).
    
-   **Queue (fila) e Scheduler:** Se sua aplicação usa processamento assíncrono (Jobs) ou tarefas agendadas, no deploy você precisa rodar os workers e scheduler. Laravel usa `php artisan queue:work` (geralmente rodando via supervisord ou systemd) e um cron para `php artisan schedule:run` a cada minuto. No Django, usaríamos Celery para fila e cron ou celery-beat para agendado. Só lembre de configurar esses serviços no ambiente produtivo.
    
-   **Deploy com Docker:** Alternativamente, pode containerizar Laravel (PHP-FPM + Nginx em containers) e orquestrar. Há imagens oficiais PHP e tutoriais para isso. Inclusive, se estiver usando Oracle via Docker, pode incorporar no compose. O Laravel Sail, por exemplo, é um conjunto de configurações Docker para dev, que pode ser adaptado a produção escalando contêineres.
    
-   **Comparação geral com Django deploy:** Ambos requerem: configurar servidor web, apontar para aplicação, garantir dependências instaladas, rodar migrations, colocar debug off, configurar estáticos. No Laravel, há a vantagem de um servidor web tradicional servir a aplicação, o que muitas hospedagens baratas suportam facilmente (basta FTP dos arquivos, configurar Apache, etc.), enquanto Django geralmente precisa de um contêiner de aplicação. Por outro lado, em ambientes mais robustos, você vai configurar PHP-FPM similarmente a como configuraria Gunicorn. Em suma, o deploy de Laravel pode ser até mais simples em certas hospedagens compartilhadas (que já vêm com PHP), mas em ambientes profissionais ambas exigem um entendimento de infraestrutura. Ferramentas especializadas como Laravel Forge ou Vapor podem abstrair isso para Laravel (Forge configura um VPS com nginx/php, Vapor deploya em AWS serverless), assim como no Django há Divio, Zappa, etc.
    

Após o deploy, faça testes finais em produção para garantir que as rotas estão acessíveis, assets carregando, sessões funcionando (cookie de sessão Laravel chamado `XSRF-TOKEN` para CSRF e `laravel_session` para sessão – similares aos cookies de sessionid e csrftoken do Django). Monitore logs e performance (pode usar Laravel Telescope in dev para monitorar queries, ou Nylo/Clockwork; em prod, talvez desabilitar telescope). Habilitar caching de config/route (como mencionado) vai diminuir o overhead de bootstrap. Com tudo isso, sua aplicação Laravel estará pronta para servir em escala de produção de forma confiável.

## 13. Trilha de estudo sugerida para dominar Laravel (tópicos adicionais)

Além do roadmap prático acima, é importante consolidar conhecimentos adicionais para se tornar proficiente em Laravel e seu ecossistema. Abaixo, listamos tópicos e analogias com conceitos do Django/Python para orientar seus estudos:

-   **PHP Moderno e OOP:** Aprenda os recursos modernos do PHP (versão 8+): _tipos escalares e de retorno_, _Properties Tipadas_, _Operador Null coalescing_, _Arrow functions_, _Attributes (anotações)_, _Enums_, etc. Laravel faz uso intenso de recursos de OOP – por exemplo, **Traits** (mixin) são usados em vários lugares do framework. Se vindo de Python, você já conhece orientação a objetos; transfira esse conhecimento para PHP (classes, herança, interfaces) e entenda particularidades como _tipagem dinâmica coerção_, _gerenciamento de memória_ (coletor de lixo) e o fato de PHP ser sincronoso (a princípio não async como Python pode ser). Felizmente, PHP 8 é bem robusto e possui até JIT. Bom domínio de PHP lhe permitirá extender o Laravel, criar packages, etc., com facilidade. Foque também em boas práticas: PSR standards (equivalente a PEP8 do Python), design patterns comuns em Laravel (Factory para seeds, Singleton no container IoC, etc.), e uso correto de exceptions. Estude como o Laravel usa injeção de dependência (via type-hinting de classes em controllers e outros lugares, algo similar a _dependency injection_ manual que às vezes fazemos no Django).
    
-   **Artisan (CLI do Laravel):** O **Artisan** é o comando de linha de Laravel, equivalente ao `manage.py` do Django (ou CLI do Flask). Domine comandos chave: `php artisan list` (lista todos), `php artisan make:model/controller/migration/seeder` (geração de código), `php artisan migrate`, `db:seed`, `tinker` (shell interativo) ([My experience of moving from Laravel to Django - Roman Sorin](https://romansorin.com/blog/my-experience-of-moving-from-laravel-to-django#:~:text=Both%20Laravel%20and%20Django%20have,you%20want%20to%20interact%20with)), `queue:work`, `schedule:run`, `config:cache`, etc. Assim como no Django usamos muito `manage.py`, no Laravel você usará o Artisan diariamente para tarefas de desenvolvimento e deploy. Vale criar seus próprios comandos (Artisan Command) se precisar automatizar algo específico – similar a adicionar comandos custom no Django.
    
-   **Composer (Gerenciador de Pacotes):** O **Composer** para PHP é análogo ao **pip**/Poetry para Python, ou npm para Node ([Laravel Series Part 1 — Environment Setup & Laravel Installation | by Tharjalan Jeyaradnam | Medium](https://medium.com/@tharju004/laravel-series-part-1-environment-setup-laravel-installation-d67257751d6c#:~:text=https%3A%2F%2Fwww,Which%20is%20really%20nice%20Text)). Com ele você adiciona bibliotecas (pacotes) ao seu projeto via `composer require nome/pacote`. Entenda o arquivo `composer.json` (lista de dependências, versões, autoloading PSR-4 das suas classes) e o `composer.lock` (snapshot de versões instaladas). No Laravel, raramente você baixa libs manualmente – tudo é via Composer/Packagist (o "PyPI" do PHP). Aprenda a diferenciar dependências de require e require-dev (como libs de debug, IDE helpers). O Composer também permite scripts (hooks ao instalar, etc.). Importante: ao trabalhar em equipe, todos devem rodar `composer install` para sincronizar dependências, assim como fariam com pip. Comparando, Composer integra aspectos do virtualenv isolando pacotes por projeto (instala em `vendor/` local), semelhante a pip dentro de um venv.
    
-   **Pacotes e Ecossistema Laravel:** O Laravel tem uma comunidade vibrante e muitos pacotes úteis. Alguns destaques para estudar/utilizar:
    
    -   **Debugging:** _Laravel Debugbar_ (barra de debug no browser, como o Django Debug Toolbar), _Laravel Telescope_ (ferramenta oficial para monitorar requests, queries, jobs – ótima em dev).
        
    -   **Autenticação/Autorização extra:** _Laravel Socialite_ (login social OAuth), _spatie/laravel-permission_ (gerenciamento de roles/permissions em DB, análogo a django-guardian ou sistema de permissões custom do Django).
        
    -   **API/Serialização:** _spatie/fractal_ ou usar o próprio _API Resources_ do Laravel como vimos.
        
    -   **Uploads e Imagens:** _spatie/laravel-medialibrary_ (facilita uploads, versões de imagens), _Intervention Image_ (manipulação de imagens).
        
    -   **Ferramentas dev:** _barryvdh/laravel-ide-helper_ (gera docblocks para melhorar auto-complete no IDE), _laravel-mix_ (se usar Webpack para assets), ou Vite (novo padrão).
        
    -   **Integração com terceiros:** Mail (há drivers para Mailgun, Postmark, etc.), Payments (ex: _Laravel Cashier_ para Stripe – similar a usar Stripe libs em Django), _laravel-slack_ etc.
        
    -   **Laravel Livewire** (já discutido) e _Alpine.js_ se for usar Livewire para complementar JS.
        
    -   **Queues:** _Laravel Horizon_ (dashboard para supervisão de filas Redis, se usar muito jobs – Django não tem equivalente nativo; seria Celery Flower mais ou menos).
        
    -   **Testes:** _pestphp/pest_ se quiser testar Pest, _laravel/dusk_ para browser tests.
        
    -   **Docker/Devops:** _Laravel Sail_ (ambiente Docker oficial para dev), _Laravel Envoy_ (scripts de deploy similar a Fabric).
        
    
    Explore o Packagist (packagist.org) para procurar pacotes Laravel antes de reinventar a roda. A comunidade Spatie, por exemplo, tem dezenas de pacotes Laravel úteis. Só tenha discernimento para escolher pacotes bem mantidos.
    
-   **Segurança em aplicações Laravel:** Assim como no Django, diversas preocupações de segurança já são tratadas pelo framework, mas o desenvolvedor deve conhecer e seguir boas práticas:
    
    -   **CSRF:** Laravel automaticamente protege POST/PUT/DELETE etc. via token (incluso por `@csrf` nas views). Verifique que todas suas forms usem essa diretiva. Para APIs stateless, use Sanctum ou tokens JWT e proteja contra CSRF se utilizar cookies de sessão em SPAs ([Laravel Sanctum - The PHP Framework For Web Artisans - Laravel](https://laravel.com/docs/12.x/sanctum#:~:text=Laravel%20Sanctum%20provides%20a%20featherweight,and%20simple%2C%20token%20based%20APIs)).
        
    -   **XSS:** O Blade escapa conteúdo de variáveis por padrão (`{{ }}`), então evite usar `{!! !!}` salvo quando necessário e seguro. Igual ao Django escapando por default.
        
    -   **SQL Injection:** Usando o Query Builder ou Eloquent, os inputs são paramerizados internamente, prevenindo SQL injection (similar ao ORM Django). Nunca concatenar valores em queries brutas sem param. Laravel também tem validação para formato de dados (por exemplo, ensure numeric).
        
    -   **Autorização:** Utilize gates/policies para restringir ações de usuários apenas aos recursos que devem acessar (ex: policy para User editar apenas a si próprio). Isso evita falhas do tipo _Insecure Direct Object Reference_. Em Django, usaríamos decorators/perms ou logica no view. No Laravel, podemos usar middleware `can` ou manual `Gate::allows`.
        
    -   **Encryption:** O Laravel fornece utilitários de criptografia simétrica (facade `Crypt`) para armazenar dados sensíveis cifrados (Django tem Signer/fernet). Use-os se precisar guardar tokens ou informações sensíveis no banco.
        
    -   **Headers de segurança:** Considere usar o middleware `SecureHeaders` ou configurar seu servidor para adicionar cabeçalhos como Content Security Policy, X-Frame-Options, etc., dependendo da necessidade.
        
    -   **Atualizações de Dependências:** Mantenha o Laravel e pacotes atualizados, aplicando patches de segurança. Laravel lança versões menores regularmente e LTS para versões principais.
        
    -   **Teste de Penetração:** Assim como qualquer app web, considere boas práticas de pentest: testar rate limiting (Laravel já tem throttle middleware), proteção contra força bruta (usar throttle no login), etc.
        
-   **Performance, Cache e Otimização:** Além das caches de config/rota/view discutidas:
    
    -   **Cache de Dados:** Utilize o Cache do Laravel (Redis/Memcached/file) para armazenar resultados de consultas pesadas ou APIs externas. Ex: `$posts = Cache::remember('posts_page1', 60, fn() => Post::latest()->take(10)->get());`. No Django, faríamos parecido com cache_page ou cache queries manual.
        
    -   **Cache de Sessão:** Como dito, usar Redis para sessions se escalar horizontalmente.
        
    -   **OPcache:** Assegure que está ativo em prod – isso diminui muito o tempo de resposta do PHP.
        
    -   **Optimização de consultas:** Use o profiler (Debugbar/Telescope) em dev para identificar N+1 queries e adicione `->with()` para eager loading. Crie índices no banco para campos de filtro/junção importantes (através de migrações). O Eloquent não cria índices automaticamente para Foreign Keys, então adicionar `$table->index('user_id')` se necessário na migration.
        
    -   **Queue para tarefas demoradas:** Enfileire processamento de emails, geração de relatórios, etc., para não segurar o response. No Django, a gente usaria Celery; no Laravel, o sistema de Queue builtin + workers.
        
    -   **Pré-carga (Preloading):** PHP 7.4+ tem opcache preload – avançado, mas pode melhorar performance carregando certas classes no opcache na inicialização do FPM. Só em apps de altíssima carga vale explorar.
        
    -   **Escala horizontal:** Laravel é stateless entre requests (sessões podem ser guardadas em DB/Redis), então escalar em múltiplos servidores é tranquilo (similar ao Django). Use um balancer (HAProxy, Nginx, etc.) se necessário.
        
    -   **Profiling em produção:** Ferramentas como Blackfire ou NewRelic podem ser conectadas para monitorar performance em profundidade. Útil mas não específico do Laravel.
        
-   **Boas práticas de arquitetura com Laravel:** Conforme sua aplicação cresce, você pode aplicar padrões de projeto para manter o código manutenível:
    
    -   **Service Layer (Camada de Serviço):** Considere mover regras de negócio complexas para classes de serviço em vez de colocar tudo no controller ou modelo. Por exemplo, uma classe `OrderService` que orquestra criação de pedido, estoque, pagamento. O controller chama essa classe. No Django, às vezes colocamos lógica em utils ou no próprio model (fat models). No Laravel, alguns preferem _fat services_ vs fat models. Laravel não impõe onde pôr lógica, então defina uma convenção.
        
    -   **Repository Pattern:** Bastante discutido na comunidade Laravel – criar repositórios que abstraem chamadas ao Eloquent (interfaces) para facilitar testes e potencial troca de ORM. Alguns acham desnecessário pois Eloquent já é uma abstração. Mas em cenários complexos (multi-banco, leitura/escrita separada) pode ser útil. É similar ao uso de DAOs no Python ou repos em Domain Driven Design.
        
    -   **DTOs e Casos de Uso:** Em aplicações enormes, pode-se usar pattern de Data Transfer Objects para validar/transportar dados entre camadas (no Laravel, Form Requests já atuam um pouco como DTO validados) e classes de _Use Case_ ou _Actions_ para cada caso de uso (ex: `RegisterNewUserAction` que engloba o processo de registro). Isso ajuda a testar isoladamente. O Django não adotou explicitamente essas camadas em sua filosofia, mas nada impede de usar lá também. Em Laravel, existe até um pacote "Laravel Actions".
        
    -   **Event-Driven:** Laravel suporta um sistema de Eventos e Listeners interno. Por exemplo, disparar evento `OrderShipped` e ter listeners (envia email, atualiza estoque). Isso promove baixo acoplamento. No Django, usamos signals para algo parecido. Cuidado para não abusar e perder rastreabilidade, mas para certos domínios, eventos ajudam.
        
    -   **MVC Estrito vs Flexível:** O Laravel permite lógica no Model, Controller, ou até diretamente na Route (closures), mas mantenha uma separação clara. Reutilize código – se dois controllers fazem algo semelhante, mova para serviço ou helper. Escreva métodos no Model para regras relacionadas ao dado (ex: `$post->publish()` method que altera estado e salva, encapsulando isso).
        
    -   **Padronize convenções em equipe:** Por exemplo, nome de métodos, estrutura de pastas (podemos criar subpastas em `app/` como `Services`, `Repositories`, etc.). Laravel não impede, você que organiza. Já no Django, a estrutura de app oferece um esqueleto. Em Laravel, se o projeto crescer muito, às vezes vale modularizar criando pacotes (cada um com modelos, controllers, etc., podendo ser carregados via Composer). É um nível avançado, mas bom saber que existe (ex.: montar seu código como se fossem pequenos pacotes instaláveis, similar a aplicações Django reutilizáveis).
        
-   **Trabalhando com Bancos de Dados diferentes:** Já abordamos o Oracle, mas convém se familiarizar com particularidades:
    
    -   **MySQL/MariaDB:** Padrão Laravel. Cuidados: use Engine InnoDB (para suportar transactions, foreign keys). Laravel migrations permitem especificar charset (utf8mb4 para emojis, etc.).
        
    -   **PostgreSQL:** Totalmente suportado (driver `pgsql`). Laravel aproveita recursos PG em alguns pontos (ex: tipo JSON, podemos usar ->json() em migração; full text search manual via DB::raw, etc.). Se você vem do Django e prefere PG, fique à vontade. Apenas instale extensão PHP pgsql e configure .env.
        
    -   **SQLite:** Bom para testes e dev, e até pequenos sistemas single-user. Suporta transactions, etc., mas cuidado que esquemas complexos (ex: alterar colunas) algumas vezes têm limitações e Laravel emula via "create new table / copy" nas migrações. Em geral, funciona bem para protótipo.
        
    -   **Oracle:** Já cobrimos via `yajra/oci8`. Lembre-se de diferenças de Oracle SQL (nomes de coluna maiúsculo, limite de tamanho de identifiers) – o pacote cuida de muitas diferenças, mas teste bem suas queries.
        
    -   **SQL Server:** Laravel suporta via driver `sqlsrv` (extensão PHP pdo_sqlsrv da Microsoft). Semelhante ao Oracle, requer drivers extras, mas pacote core do Laravel já tem suporte se extensão instalada.
        
    -   Em resumo, Laravel é neutro de SGBD na maioria do uso do Eloquent. Só cuidado com funções específicas SQL se usar (ex: `DB::raw('DATE()')` pode mudar sintaxe entre bancos). Prefira métodos do query builder que se traduzem corretamente.
        
-   **Comunidade e Recursos:** Por fim, aproveite a comunidade Laravel:
    
    -   **Documentação Oficial:** extremamente bem feita (docs.laravel.com), sempre atualizada.
        
    -   **Laracasts:** plataforma de vídeos com **muitas** séries sobre Laravel e PHP, incluindo do básico ao avançado, testes, Livewire, etc. (algumas grátis, maioria paga, mas vale se for usar muito Laravel).
        
    -   **Forums e Discord:** the Laravel subreddit, Laravel.io forums, Laracasts forum, e Discords comunitários são ativos.
        
    -   **Conferências e Blogs:** Laracon (conf anual), blog oficial Laravel News, e vários devs (Matt Stauffer, Christoph Rumpel, etc.) publicam dicas. Assim como em Django temos o DjangoCon, Django Blog, etc.
        
    -   **Comparativo de conceitos:** Sempre que ficar confuso com algo em Laravel, tente traçar um paralelo com o que você já sabe em Django – muitas vezes existe (como procuramos fazer neste roadmap). Ex.: "Como faço um middleware no Laravel?" (resposta: criar classe que implemente `handle`, registrar em Kernel – similar a middleware Django). "Tem algo como signals?" (Laravel Events). "Templates tem tags custom?" (Blade directives e components). Usar essa analogia pode acelerar o aprendizado.
        

Com essa trilha de estudos e o roadmap apresentado, você estará bem equipado para desenvolver uma aplicação **FullStack Laravel** robusta. Lembre que, embora a sintaxe e algumas práticas mudem, os fundamentos de web dev (MVC, HTTP, banco de dados, segurança) permanecem. Aproveite o aprendizado de Django para escrever código Laravel bem estruturado e seguro, e logo você será produtivo também nesse framework. Boa codificação!

**Sources:**

-   Documentação Laravel (instalação e conceitos) ([Installation - Laravel 10.x - The PHP Framework For Web Artisans](https://laravel.com/docs/10.x#:~:text=via%20Composer%27s%20%60create)) ([Installation - Laravel 10.x - The PHP Framework For Web Artisans](https://laravel.com/docs/10.x#:~:text=3%20php%20artisan%20serve)) ([Installation - Laravel 10.x - The PHP Framework For Web Artisans](https://laravel.com/docs/10.x#:~:text=If%20you%20are%20developing%20on,Redis%20locally%2C%20consider%20using%20DBngin)) ([Deployment - Laravel 11.x - The PHP Framework For Web Artisans](https://laravel.com/docs/11.x/deployment#:~:text=If%20you%20are%20building%20a,command%20during%20your%20deployment%20process))
    
-   Artigos comparativos e experiências de migração ([My experience of moving from Laravel to Django - Roman Sorin](https://romansorin.com/blog/my-experience-of-moving-from-laravel-to-django#:~:text=,approach%2C%20these%20are%20always%20classes)) ([My experience of moving from Laravel to Django - Roman Sorin](https://romansorin.com/blog/my-experience-of-moving-from-laravel-to-django#:~:text=Unlike%20Django%2C%20defining%20fields%20is,instance%20from%20the%20database%20representation)) ([Authentication in Laravel Using Breeze - Kinsta®](https://kinsta.com/blog/laravel-breeze/#:~:text=Laravel%20Breeze%20is%20an%20authentication,also%20has%20an%20API%20version))
    
-   Documentação e artigos sobre Oracle no Laravel ([A Quick Guide to Using Laravel With Oracle Database](https://adevait.com/laravel/how-to-use-laravel-with-oracle-database#:~:text=%27oracle%27%20%3D,env%28%27DB_CHARSET%27%2C%20%27AL32UTF8)) ([A Quick Guide to Using Laravel With Oracle Database](https://adevait.com/laravel/how-to-use-laravel-with-oracle-database#:~:text=with%20the%20yajra%2Flaravel,Laravel%20working%20with%20Oracle%20Database))
    
-   Documentação Laravel Livewire e Blade ([My experience of moving from Laravel to Django - Roman Sorin](https://romansorin.com/blog/my-experience-of-moving-from-laravel-to-django#:~:text=,in%20a%20Django))
    
-   Kinsta – Comparativo Django vs Laravel e Laravel Breeze ([Authentication in Laravel Using Breeze - Kinsta®](https://kinsta.com/blog/laravel-breeze/#:~:text=Laravel%20Breeze%20is%20an%20authentication,also%20has%20an%20API%20version)) ([Laravel Sanctum - The PHP Framework For Web Artisans - Laravel](https://laravel.com/docs/12.x/sanctum#:~:text=Laravel%20Sanctum%20provides%20a%20featherweight,and%20simple%2C%20token%20based%20APIs))
    
-   Medium e Blogs (estrutura de projeto, etc.) ([Laravel — P3: Directory Structure Basics | by Dino Cajic | Geek Culture | Medium](https://medium.com/geekculture/laravel-p3-directory-structure-basics-d922acc135c#:~:text=app%2F%20Console%2F%20Exceptions%2F%20Http%2F%20Controllers%2F,php%20Providers))
