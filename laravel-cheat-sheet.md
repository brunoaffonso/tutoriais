# Laravel 12 Cheat Sheet

## Instalação e Configuração

### Instalação
```bash
# Via Composer
composer create-project laravel/laravel projeto-nome

# Via Laravel Installer
composer global require laravel/installer
laravel new projeto-nome
```

### Configuração
- Configurar `.env` com as credenciais do banco de dados
- Gerar chave de aplicação:
```bash
php artisan key:generate
```

## Comandos Artisan

### Básicos
```bash
php artisan                     # Lista todos os comandos
php artisan list               # Lista todos os comandos
php artisan help [comando]     # Mostra ajuda sobre um comando
php artisan --version          # Mostra a versão do Laravel
php artisan serve              # Inicia o servidor de desenvolvimento (http://localhost:8000)
php artisan about              # Exibe informações básicas sobre o ambiente Laravel
```

### Gerenciamento de Cache
```bash
php artisan cache:clear        # Limpa o cache da aplicação
php artisan config:clear       # Limpa o cache de configuração
php artisan route:clear        # Limpa o cache de rotas
php artisan view:clear         # Limpa o cache de views
php artisan clear-compiled     # Remove os arquivos compilados (bootstrap/compiled.php)
php artisan optimize           # Otimiza a aplicação para melhor performance
php artisan optimize:clear     # Limpa arquivos de cache otimizados
```

### Database e Migrations
```bash
php artisan migrate                   # Executa as migrações
php artisan migrate:fresh             # Derruba todas as tabelas e executa as migrações
php artisan migrate:refresh           # Reverte e executa todas as migrações
php artisan migrate:reset             # Reverte todas as migrações
php artisan migrate:rollback          # Reverte a última migração
php artisan migrate:status            # Status de cada migração
php artisan db:seed                   # Executa os seeders
php artisan db:wipe                   # Apaga todos os dados de todas as tabelas
```

### Geradores
```bash
php artisan make:model Nome                   # Cria um modelo
php artisan make:model Nome -m                # Cria um modelo com migração
php artisan make:model Nome -mfsc             # Cria um modelo com migração, factory, seeder e controller

php artisan make:migration criar_tabela_nome  # Cria uma migração
php artisan make:seeder NomeSeeder            # Cria um seeder
php artisan make:factory NomeFactory          # Cria uma factory

php artisan make:controller NomeController                   # Cria um controller
php artisan make:controller NomeController --resource        # Cria um controller resource
php artisan make:controller NomeController --api             # Cria um controller API
php artisan make:controller NomeController --invokable       # Cria um controller de ação única

php artisan make:middleware NomeMiddleware    # Cria um middleware
php artisan make:request NomeRequest          # Cria um form request (validação)
php artisan make:policy NomePolicy            # Cria uma policy
php artisan make:event NomeEvent              # Cria um evento
php artisan make:listener NomeListener        # Cria um listener
php artisan make:mail NomeMail                # Cria uma classe de e-mail
php artisan make:notification NomeNotification # Cria uma notificação
php artisan make:provider NomeProvider        # Cria um service provider
php artisan make:command NomeComando          # Cria um comando Artisan

php artisan make:test NomeTest                # Cria um teste de feature
php artisan make:test NomeTest --unit         # Cria um teste unitário
```

### Autenticação e Autorização
```bash
php artisan make:auth              # Gera as views/controllers de autenticação (antes do Laravel 6)
php artisan ui bootstrap --auth    # Gera scaffold de autenticação com Bootstrap
php artisan ui vue --auth          # Gera scaffold de autenticação com Vue

# Usando Laravel Breeze, Jetstream ou Fortify
composer require laravel/breeze --dev
php artisan breeze:install         # Instala o Breeze (auth scaffolding)

composer require laravel/jetstream
php artisan jetstream:install livewire # Instala Jetstream com Livewire
php artisan jetstream:install inertia  # Instala Jetstream com Inertia

php artisan make:policy PostPolicy --model=Post # Cria uma policy para o modelo Post
```

### Rotas
```bash
php artisan route:list            # Lista todas as rotas
php artisan route:clear           # Limpa o cache de rotas
php artisan route:cache           # Cria cache das rotas (aumenta performance)
```

### Tratamento de Filas
```bash
php artisan queue:work            # Inicia o processamento de filas
php artisan queue:listen          # Inicia o processamento de filas (reinicia após cada job)
php artisan queue:restart         # Reinicia os workers de fila
php artisan queue:failed          # Lista todos os jobs falhos
php artisan queue:failed-table    # Cria uma migração para a tabela de falhas
php artisan queue:flush           # Apaga todos os jobs falhos
php artisan queue:retry all       # Tenta novamente todos os jobs falhos
php artisan queue:retry [id]      # Tenta novamente um job falho específico
```

### Scheduler
```bash
php artisan schedule:run          # Executa os comandos agendados
php artisan schedule:list         # Lista os comandos agendados
php artisan schedule:work         # Executa o scheduler em foreground
```

### Outros
```bash
php artisan storage:link          # Cria um link simbólico de public/storage para storage/app/public
php artisan vendor:publish        # Publica assets de pacotes
php artisan view:cache            # Cria cache das views
php artisan event:generate        # Gera classes para os eventos e listeners
php artisan lang:publish          # Publica arquivos de tradução
```

## Estrutura de Diretórios

### Principais Diretórios
- **app/**: Contém o código principal da aplicação
  - **Http/Controllers/**: Controllers da aplicação
  - **Http/Middleware/**: Middleware da aplicação
  - **Http/Requests/**: Form requests para validação
  - **Models/**: Modelos Eloquent
  - **Providers/**: Service providers
  - **Events/**: Eventos da aplicação
  - **Listeners/**: Listeners para eventos
  - **Mail/**: Classes para envio de emails
  - **Notifications/**: Notificações
  - **Policies/**: Políticas de autorização
  
- **bootstrap/**: Arquivos de inicialização
- **config/**: Arquivos de configuração
- **database/**
  - **factories/**: Factories para testes
  - **migrations/**: Migrações do banco de dados
  - **seeders/**: Seeders para popular o banco
  
- **public/**: Ponto de entrada da aplicação, arquivos públicos
- **resources/**
  - **css/**: Arquivos CSS
  - **js/**: Arquivos JavaScript
  - **views/**: Views da aplicação (Blade)
  - **lang/**: Arquivos de tradução
  
- **routes/**
  - **web.php**: Rotas web
  - **api.php**: Rotas API
  - **console.php**: Rotas de console
  - **channels.php**: Rotas de broadcasting
  
- **storage/**: Armazenamento (logs, uploads, cache)
- **tests/**: Testes automatizados
- **vendor/**: Dependências (gerenciadas pelo Composer)

## Rotas

### Tipos de Rotas
```php
// routes/web.php - Rotas com sessão/CSRF
Route::get('/pagina', function () {
    return view('pagina');
});

// routes/api.php - Rotas sem estado/Tokens API
Route::get('/api/dados', [ApiController::class, 'dados']);

// Métodos de rota
Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);
Route::patch($uri, $callback);
Route::delete($uri, $callback);
Route::options($uri, $callback);

// Múltiplos métodos
Route::match(['get', 'post'], '/form', [FormController::class, 'handle']);
Route::any('/qualquer', [MeuController::class, 'qualquer']);
```

### Rotas com Parâmetros
```php
// Parâmetro obrigatório
Route::get('/posts/{id}', [PostController::class, 'show']);

// Parâmetro opcional
Route::get('/user/{name?}', function ($name = 'Guest') {
    return "Olá, {$name}";
});

// Validação via expressão regular
Route::get('/user/{id}', function ($id) {
    //
})->where('id', '[0-9]+');

// Vários parâmetros com validação
Route::get('/posts/{id}/{slug}', [PostController::class, 'show'])
    ->where(['id' => '[0-9]+', 'slug' => '[A-Za-z0-9\-]+']);
```

### Grupos de Rotas
```php
// Grupo com middleware
Route::middleware(['auth'])->group(function () {
    Route::get('/dashboard', [DashboardController::class, 'index']);
    Route::get('/perfil', [ProfileController::class, 'show']);
});

// Grupo com prefixo
Route::prefix('admin')->group(function () {
    Route::get('/usuarios', [AdminController::class, 'users']);
    Route::get('/posts', [AdminController::class, 'posts']);
});

// Grupo com namespace (em versões anteriores)
Route::namespace('Admin')->group(function () {
    // Controllers no namespace "App\Http\Controllers\Admin"
});

// Grupo com nome
Route::name('admin.')->group(function () {
    Route::get('/dashboard', [AdminController::class, 'dashboard'])->name('dashboard');
    // nome da rota: 'admin.dashboard'
});
```

### Rotas de Recursos
```php
// Rotas de recursos completas
Route::resource('posts', PostController::class);

// Rotas de recursos específicas
Route::resource('fotos', FotoController::class)->only([
    'index', 'show'
]);

// Excluir algumas rotas de recursos
Route::resource('comentarios', ComentarioController::class)->except([
    'create', 'store', 'update', 'destroy'
]);

// Rotas de API (sem create/edit)
Route::apiResource('produtos', ProdutoController::class);

// Vários recursos
Route::resources([
    'fotos' => FotoController::class,
    'posts' => PostController::class,
]);
```

## Controladores

### Estrutura Básica de Controlador
```php
namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function index()
    {
        $posts = Post::all();
        return view('posts.index', ['posts' => $posts]);
    }
    
    public function show($id)
    {
        $post = Post::findOrFail($id);
        return view('posts.show', ['post' => $post]);
    }
    
    public function create()
    {
        return view('posts.create');
    }
    
    public function store(Request $request)
    {
        $validated = $request->validate([
            'title' => 'required|max:255',
            'content' => 'required',
        ]);
        
        Post::create($validated);
        
        return redirect()->route('posts.index');
    }
    
    public function edit($id)
    {
        $post = Post::findOrFail($id);
        return view('posts.edit', ['post' => $post]);
    }
    
    public function update(Request $request, $id)
    {
        $validated = $request->validate([
            'title' => 'required|max:255',
            'content' => 'required',
        ]);
        
        $post = Post::findOrFail($id);
        $post->update($validated);
        
        return redirect()->route('posts.show', $post->id);
    }
    
    public function destroy($id)
    {
        $post = Post::findOrFail($id);
        $post->delete();
        
        return redirect()->route('posts.index');
    }
}
```

### Injeção de Dependência
```php
public function index(Request $request, PostService $postService)
{
    $posts = $postService->getAllPosts();
    return view('posts.index', ['posts' => $posts]);
}
```

## Middlewares

### Middlewares Comuns
```php
// app/Http/Kernel.php contém middlewares padrão:
// - \App\Http\Middleware\TrustProxies::class
// - \Illuminate\Http\Middleware\HandleCors::class
// - \App\Http\Middleware\PreventRequestsDuringMaintenance::class
// - \Illuminate\Foundation\Http\Middleware\ValidatePostSize::class
// - \App\Http\Middleware\TrimStrings::class
// - \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class
```

### Criando um Middleware
```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class CheckAge
{
    public function handle(Request $request, Closure $next)
    {
        if ($request->age < 18) {
            return redirect('home');
        }

        return $next($request);
    }
}
```

### Registrando um Middleware
```php
// Em app/Http/Kernel.php

// Global Middleware
protected $middleware = [
    // ...
    \App\Http\Middleware\CheckAge::class,
];

// Grupos de Middleware
protected $middlewareGroups = [
    'web' => [
        // ...
    ],
    'api' => [
        // ...
    ],
];

// Middleware com Alias
protected $middlewareAliases = [
    'auth' => \App\Http\Middleware\Authenticate::class,
    'age' => \App\Http\Middleware\CheckAge::class,
];
```

## Banco de Dados e Eloquent

### Migrações
```php
// database/migrations/xxxx_xx_xx_create_posts_table.php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up()
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->text('content');
            $table->boolean('is_published')->default(false);
            $table->foreignId('user_id')->constrained();
            $table->timestamps();
            $table->softDeletes();
        });
    }

    public function down()
    {
        Schema::dropIfExists('posts');
    }
};
```

### Seeders
```php
// database/seeders/UserSeeder.php
namespace Database\Seeders;

use App\Models\User;
use Illuminate\Database\Seeder;

class UserSeeder extends Seeder
{
    public function run()
    {
        User::factory(10)->create();
        
        User::create([
            'name' => 'Admin User',
            'email' => 'admin@exemplo.com',
            'password' => bcrypt('password'),
        ]);
    }
}

// database/seeders/DatabaseSeeder.php
public function run()
{
    $this->call([
        UserSeeder::class,
        PostSeeder::class,
    ]);
}
```

### Factories
```php
// database/factories/PostFactory.php
namespace Database\Factories;

use App\Models\Post;
use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;

class PostFactory extends Factory
{
    protected $model = Post::class;

    public function definition()
    {
        return [
            'title' => $this->faker->sentence,
            'content' => $this->faker->paragraphs(3, true),
            'is_published' => $this->faker->boolean(80),
            'user_id' => User::factory(),
        ];
    }
    
    public function published()
    {
        return $this->state([
            'is_published' => true,
        ]);
    }
}
```

### Modelos Eloquent
```php
// app/Models/Post.php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class Post extends Model
{
    use HasFactory, SoftDeletes;
    
    protected $fillable = [
        'title', 'content', 'is_published', 'user_id'
    ];
    
    protected $casts = [
        'is_published' => 'boolean',
    ];
    
    // Relacionamento: um post pertence a um usuário
    public function user()
    {
        return $this->belongsTo(User::class);
    }
    
    // Relacionamento: um post tem muitos comentários
    public function comments()
    {
        return $this->hasMany(Comment::class);
    }
    
    // Scope: apenas posts publicados
    public function scopePublished($query)
    {
        return $query->where('is_published', true);
    }
    
    // Acessor: título em maiúsculas
    public function getTitleUppercaseAttribute()
    {
        return strtoupper($this->title);
    }
    
    // Mutator: para title
    public function setTitleAttribute($value)
    {
        $this->attributes['title'] = ucfirst($value);
    }
}
```

### Operações Eloquent (CRUD)
```php
// Criar
$post = new Post;
$post->title = 'Meu título';
$post->content = 'Conteúdo do post';
$post->save();

// Criar com mass assignment
$post = Post::create([
    'title' => 'Meu título',
    'content' => 'Conteúdo do post',
    'user_id' => 1,
]);

// Ler - Buscar todos
$posts = Post::all();

// Ler - Buscar por ID
$post = Post::find(1);
$post = Post::findOrFail(1); // Lança exceção se não existir

// Ler - Com Where
$posts = Post::where('is_published', true)->get();
$post = Post::where('slug', 'meu-post')->first();

// Ler - Com ordenação
$posts = Post::orderBy('created_at', 'desc')->get();

// Ler - Com paginação
$posts = Post::paginate(15);

// Ler - Com relacionamentos
$posts = Post::with('user', 'comments')->get();

// Atualizar
$post = Post::find(1);
$post->title = 'Novo título';
$post->save();

// Atualizar com mass assignment
$post->update([
    'title' => 'Novo título',
    'content' => 'Novo conteúdo',
]);

// Excluir
$post = Post::find(1);
$post->delete();

// Excluir por ID
Post::destroy(1);
Post::destroy([1, 2, 3]);

// Soft delete (se SoftDeletes estiver habilitado)
$post->delete(); // Não exclui do banco, apenas marca como excluído

// Restaurar soft delete
$post = Post::withTrashed()->find(1);
$post->restore();

// Exclusão permanente
$post->forceDelete();
```

### Relacionamentos
```php
// Definições

// Um para Um (1:1)
public function profile()
{
    return $this->hasOne(Profile::class);
}

// Um para Muitos (1:N)
public function posts()
{
    return $this->hasMany(Post::class);
}

// Muitos para Um (N:1)
public function user()
{
    return $this->belongsTo(User::class);
}

// Muitos para Muitos (N:N)
public function roles()
{
    return $this->belongsToMany(Role::class);
}

// Muitos para Muitos com tabela pivô personalizada
public function roles()
{
    return $this->belongsToMany(Role::class, 'user_roles');
}

// Muitos para Muitos com campos extras na tabela pivô
public function roles()
{
    return $this->belongsToMany(Role::class)->withPivot('active', 'created_by');
}

// Has Many Through (Tem muitos através de)
// Um user tem muitos comments através de posts
public function comments()
{
    return $this->hasManyThrough(Comment::class, Post::class);
}

// Polimórfico um-para-um
public function image()
{
    return $this->morphOne(Image::class, 'imageable');
}

// Polimórfico um-para-muitos
public function comments()
{
    return $this->morphMany(Comment::class, 'commentable');
}

// Polimórfico muitos-para-muitos
public function tags()
{
    return $this->morphToMany(Tag::class, 'taggable');
}
```

## Views e Blade

### Estrutura Básica de Blade
```php
// resources/views/welcome.blade.php
<!DOCTYPE html>
<html>
<head>
    <title>{{ $title ?? 'Meu Site' }}</title>
</head>
<body>
    <h1>{{ $heading }}</h1>
    
    <div>
        {!! $content !!}  <!-- Não escapa HTML -->
    </div>
</body>
</html>
```

### Diretivas Blade
```php
// Condicionais
@if ($condition)
    Isso será mostrado se condition for true
@elseif ($otherCondition)
    Isso será mostrado se otherCondition for true
@else
    Caso contrário, isso será mostrado
@endif

@unless ($condition)
    Isso será mostrado se condition for false
@endunless

@isset($variable)
    O $variable está definido
@endisset

@empty($variable)
    O $variable está vazio
@endempty

// Switch
@switch($value)
    @case(1)
        Valor é 1
        @break
    @case(2)
        Valor é 2
        @break
    @default
        Outro valor
@endswitch

// Loops
@for ($i = 0; $i < 10; $i++)
    O valor é {{ $i }}
@endfor

@foreach ($items as $item)
    <p>Item: {{ $item }}</p>
@endforeach

@forelse ($items as $item)
    <p>Item: {{ $item }}</p>
@empty
    <p>Sem itens para mostrar</p>
@endforelse

@while ($condition)
    <p>Repetindo...</p>
@endwhile

// Loop variáveis
@foreach ($users as $user)
    {{ $loop->index }}       // 0, 1, 2...
    {{ $loop->iteration }}   // 1, 2, 3...
    {{ $loop->remaining }}   // contagem restante
    {{ $loop->count }}       // contagem total
    {{ $loop->first }}       // boolean - é o primeiro?
    {{ $loop->last }}        // boolean - é o último?
    {{ $loop->even }}        // boolean - é par?
    {{ $loop->odd }}         // boolean - é ímpar?
    {{ $loop->depth }}       // profundidade do loop
    {{ $loop->parent }}      // parent loop
@endforeach

// Incluindo sub-views
@include('partials.header')
@include('partials.sidebar', ['active' => 'home'])

// Componentes
@component('alert')
    <strong>Aviso!</strong> Algo aconteceu!
@endcomponent

// Definindo seções (para layouts)
@section('title', 'Título da Página')  // Seção de linha única

@section('content')
    <p>Conteúdo da página</p>
@endsection

// Estendendo layouts
@extends('layouts.app')

// Slots (para componentes)
<x-alert type="error" :message="$message" />

<x-alert>
    <x-slot name="title">Erro!</x-slot>
    Algo deu errado!
</x-alert>

// Comentários
{{-- Este comentário não aparecerá no HTML --}}
```

### Layouts
```php
// resources/views/layouts/app.blade.php
<!DOCTYPE html>
<html>
<head>
    <title>@yield('title', 'Meu Site')</title>
    @stack('styles')
</head>
<body>
    <header>
        @include('partials.navigation')
    </header>
    
    <main>
        @yield('content')
    </main>
    
    <footer>
        @include('partials.footer')
    </footer>
    
    @stack('scripts')
</body>
</html>

// Uso do layout em uma view
// resources/views/home.blade.php
@extends('layouts.app')

@section('title', 'Home')

@section('content')
    <h1>Bem-vindo ao site!</h1>
    <p>Conteúdo da página inicial.</p>
@endsection

@push('scripts')
    <script src="/js/home.js"></script>
@endpush
```

## Validação

### Validação no Controller
```php
public function store(Request $request)
{
    $validated = $request->validate([
        'title' => 'required|string|max:255',
        'body' => 'required|string',
        'publish_at' => 'nullable|date',
        'email' => 'required|email|unique:users,email',
        'age' => 'required|numeric|min:18',
        'image' => 'nullable|image|max:2048',
        'category_id' => 'required|exists:categories,id',
    ]);
    
    // Continua com a criação...
}
```

### Form Request Validation
```php
// app/Http/Requests/StorePostRequest.php
namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StorePostRequest extends FormRequest
{
    public function authorize()
    {
        return true; // ou lógica de autorização
    }

    public function rules()
    {
        return [
            'title' => 'required|string|max:255',
            'body' => 'required|string',
            'category_id' => 'required|exists:categories,id',
        ];
    }
    
    public function messages()
    {
        return [
            'title.required' => 'O título é obrigatório',
            'title.max' => 'O título não pode ter mais de :max caracteres',
        ];
    }
    
    public function attributes()
    {
        return [
            'title' => 'título',
            'body' => 'conteúdo',
        ];
    }
}

// Uso no controller
public function store(StorePostRequest $request)
{
    // Validado automaticamente
    $validated = $request->validated();
    
    // Continua com a criação...
}
```

### Regras de Validação Comuns
```
required            // campo é obrigatório
required_if:foo,bar // obrigatório se campo 'foo' for 'bar'
required_unless:foo,bar // obrigatório a menos que 'foo' seja 'bar'
required_with:foo,bar // obrigatório quando qualquer dos campos está presente
required_with_all:foo,bar // obrigatório quando todos os campos estão presentes
required_without:foo,bar // obrigatório quando qualquer dos campos não está presente
required_without_all:foo,bar // obrigatório quando todos os campos não estão presentes

string              // deve ser uma string
numeric             // deve ser número (int ou float)
integer             // deve ser inteiro
boolean             // deve ser booleano
array               // deve ser array
json                // deve ser JSON válido

min:8               // tamanho/valor mínimo
max:255             // tamanho/valor máximo
size:10             // tamanho/valor exato
between:5,10        // tamanho/valor entre

email               // formato de email
url                 // formato de URL
ip                  // endereço IP
uuid                // UUID
date                // data válida
date_format:Y-m-d   // data em formato específico
date_equals:today   // igual à data específica
after:today         // depois da data
after_or_equal:today // depois ou igual à data
before:today        // antes da data
before_or_equal:today // antes ou igual à data

same:field          // igual ao campo 'field'
different:field     // diferente do campo 'field'
unique:table,column // único na coluna da tabela
exists:table,column // existe na coluna da tabela
in:foo,bar,baz      // valor deve estar na lista
not_in:foo,bar,baz  // valor não deve estar na lista

image               // arquivo de imagem (jpg, jpeg, png, bmp, gif, svg, webp)
mimes:jpg,png       // tipos MIME específicos
mimetypes:image/jpeg,image/png // tipos MIME específicos
dimensions:min_width=100,max_width=1000,min_height=100,max_height=1000 // dimensões da imagem
```

## Autenticação

### Controllers de Autenticação
```php
// LoginController
public function login(Request $request)
{
    $credentials = $request->validate([
        'email' => 'required|email',
        'password' => 'required',
    ]);

    if (Auth::attempt($credentials)) {
        $request->session()->regenerate();
        return redirect()->intended('dashboard');
    }

    return back()->withErrors([
        'email' => 'As credenciais fornecidas não correspondem aos nossos registros.',
    ]);
}

public function logout(Request $request)
{
    Auth::logout();
    $request->session()->invalidate();
    $request->session()->regenerateToken();
    return redirect('/');
}
```

### Helpers de Autenticação
```php
// Verificar se o usuário está autenticado
if (Auth::check()) { /* ... */ }

// Obter o usuário autenticado
$user = Auth::user();
$id = Auth::id();

// Autenticar manualmente um usuário
Auth::login($user);
Auth::loginUsingId($id);

// Desconectar
Auth::logout();

// Em Blade
@auth
    <!-- Usuário está autenticado -->
@endauth

@guest
    <!-- Usuário não está autenticado -->
@endguest
```

## Autorização

### Gates e Policies
```php
// Definindo Gates (em AuthServiceProvider.php)
public function boot()
{
    $this->registerPolicies();

    Gate::define('update-post', function ($user, $post) {
        return $user->id === $post->user_id;
    });
    
    Gate::define('admin', function ($user) {
        return $user->is_admin;
    });
}

// Usando Gates
if (Gate::allows('update-post', $post)) {
    // O usuário pode atualizar o post
}

if (Gate::denies('update-post', $post)) {
    // O usuário não pode atualizar o post
}
```

### Policies
```php
// app/Policies/PostPolicy.php
namespace App\Policies;

use App\Models\Post;
use App\Models\User;
use Illuminate\Auth\Access\HandlesAuthorization;

class PostPolicy
{
    use HandlesAuthorization;

    public function viewAny(User $user)
    {
        return true;
    }

    public function view(User $user, Post $post)
    {
        return true;
    }

    public function create(User $user)
    {
        return true;
    }

    public function update(User $user, Post $post)
    {
        return $user->id === $post->user_id;
    }

    public function delete(User $user, Post $post)
    {
        return $user->id === $post->user_id;
    }
}

// Registrando Policies
protected $policies = [
    Post::class => PostPolicy::class,
];

// Uso de Policies
if ($user->can('update', $post)) {
    // O usuário pode atualizar o post
}

// Na rota ou controller
$this->authorize('update', $post);

// Em Blade
@can('update', $post)
    <!-- Pode atualizar o post -->
@endcan

@cannot('update', $post)
    <!-- Não pode atualizar o post -->
@endcannot
```

## Filas (Queues)

### Configuração
```php
// config/queue.php define os drivers disponíveis:
// - sync (síncrono, sem fila)
// - database
// - redis
// - beanstalkd
// - sqs (Amazon SQS)
// - etc.

// .env
QUEUE_CONNECTION=redis
```

### Criando Jobs
```php
// app/Jobs/ProcessPodcast.php
namespace App\Jobs;

use App\Models\Podcast;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class ProcessPodcast implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    protected $podcast;
    
    public function __construct(Podcast $podcast)
    {
        $this->podcast = $podcast;
    }

    public function handle()
    {
        // Processar o podcast...
    }
    
    // Opcional: executado se o job falhar
    public function failed(\Throwable $exception)
    {
        // Notificar equipe sobre falha...
    }
}
```

### Despachando Jobs
```php
// Despachar para fila padrão
ProcessPodcast::dispatch($podcast);

// Sintaxe alternativa
dispatch(new ProcessPodcast($podcast));

// Despachar para uma fila específica
ProcessPodcast::dispatch($podcast)->onQueue('processing');

// Despachar com delay
ProcessPodcast::dispatch($podcast)->delay(now()->addMinutes(10));

// Encadear jobs
ProcessPodcast::withChain([
    new OptimizePodcast($podcast),
    new ReleasePodcast($podcast),
])->dispatch();
```

### Executando Workers
```bash
# Processar jobs uma vez e encerrar
php artisan queue:work

# Continuar processando jobs até cancelar
php artisan queue:work --daemon

# Especificar conexão e fila
php artisan queue:work redis --queue=high,default

# Limitar tempo e tentativas
php artisan queue:work --tries=3 --timeout=90
```

## Eventos

### Definindo Eventos
```php
// app/Events/OrderShipped.php
namespace App\Events;

use App\Models\Order;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class OrderShipped
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public $order;

    public function __construct(Order $order)
    {
        $this->order = $order;
    }
}
```

### Listeners
```php
// app/Listeners/SendShipmentNotification.php
namespace App\Listeners;

use App\Events\OrderShipped;
use App\Notifications\OrderShippedNotification;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;

class SendShipmentNotification implements ShouldQueue
{
    public function handle(OrderShipped $event)
    {
        $event->order->user->notify(
            new OrderShippedNotification($event->order)
        );
    }
}
```

### Registrando Eventos e Listeners
```php
// app/Providers/EventServiceProvider.php
protected $listen = [
    \App\Events\OrderShipped::class => [
        \App\Listeners\SendShipmentNotification::class,
        \App\Listeners\UpdateInventory::class,
    ],
];
```

### Disparando Eventos
```php
// Usando a classe de evento
event(new OrderShipped($order));

// Usando helper
OrderShipped::dispatch($order);
```

## Notificações

### Criando Notificações
```php
// app/Notifications/InvoicePaid.php
namespace App\Notifications;

use App\Models\Invoice;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;

class InvoicePaid extends Notification implements ShouldQueue
{
    use Queueable;

    protected $invoice;

    public function __construct(Invoice $invoice)
    {
        $this->invoice = $invoice;
    }

    // Canais de entrega
    public function via($notifiable)
    {
        return ['mail', 'database', 'slack'];
    }

    // Email
    public function toMail($notifiable)
    {
        return (new MailMessage)
                    ->subject('Fatura Paga')
                    ->greeting('Olá ' . $notifiable->name)
                    ->line('Sua fatura foi paga com sucesso.')
                    ->action('Ver Fatura', url('/invoices/' . $this->invoice->id))
                    ->line('Obrigado por usar nosso aplicativo!');
    }

    // Para armazenamento no banco de dados
    public function toDatabase($notifiable)
    {
        return [
            'invoice_id' => $this->invoice->id,
            'amount' => $this->invoice->amount,
        ];
    }
    
    // Para Slack
    public function toSlack($notifiable)
    {
        return (new SlackMessage)
                    ->success()
                    ->content('Fatura ' . $this->invoice->id . ' foi paga!');
    }
}
```

### Enviando Notificações
```php
// Enviar a um único usuário
$user->notify(new InvoicePaid($invoice));

// Enviar via Notification Facade
Notification::send($users, new InvoicePaid($invoice));

// Enviar para uma route notifiable (sem model)
Notification::route('mail', 'example@example.com')
            ->route('slack', 'https://hooks.slack.com/...')
            ->notify(new InvoicePaid($invoice));
```

## API

### API Resources (Transformadores JSON)
```php
// app/Http/Resources/UserResource.php
namespace App\Http\Resources;

use Illuminate\Http\Resources\Json\JsonResource;

class UserResource extends JsonResource
{
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->email,
            'created_at' => $this->created_at,
            'posts' => PostResource::collection($this->whenLoaded('posts')),
        ];
    }
}

// Uso em Controllers
public function show(User $user)
{
    return new UserResource($user);
}

public function index()
{
    return UserResource::collection(User::with('posts')->paginate());
}
```

### API Sanctum (Autenticação para API)
```php
// Instalação
composer require laravel/sanctum
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
php artisan migrate

// Controller de Autenticação
public function login(Request $request)
{
    $request->validate([
        'email' => 'required|email',
        'password' => 'required',
    ]);

    $user = User::where('email', $request->email)->first();

    if (! $user || ! Hash::check($request->password, $user->password)) {
        return response()->json([
            'message' => 'Invalid credentials'
        ], 401);
    }

    $token = $user->createToken('api-token')->plainTextToken;

    return response()->json([
        'user' => $user,
        'token' => $token,
    ]);
}

public function logout(Request $request)
{
    $request->user()->currentAccessToken()->delete();

    return response()->json(['message' => 'Logged out']);
}

// Rotas Protegidas
Route::middleware('auth:sanctum')->group(function () {
    Route::get('/user', function (Request $request) {
        return $request->user();
    });
    
    Route::apiResource('posts', PostController::class);
});
```

## Testes

### Testes de Feature
```php
// tests/Feature/PostTest.php
namespace Tests\Feature;

use App\Models\Post;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class PostTest extends TestCase
{
    use RefreshDatabase;

    public function test_user_can_see_posts()
    {
        // Criar dados de teste
        $post = Post::factory()->create();

        // Fazer requisição
        $response = $this->get('/posts');

        // Verificar resposta
        $response->assertStatus(200);
        $response->assertSee($post->title);
    }

    public function test_authenticated_user_can_create_post()
    {
        // Criar usuário
        $user = User::factory()->create();

        // Dados do post
        $postData = [
            'title' => 'Meu novo post',
            'content' => 'Conteúdo do post'
        ];

        // Fazer requisição como usuário autenticado
        $response = $this->actingAs($user)
                         ->post('/posts', $postData);

        // Verificar redirecionamento
        $response->assertRedirect('/posts');

        // Verificar se o post foi salvo no banco
        $this->assertDatabaseHas('posts', $postData);
    }
}
```

### Testes Unitários
```php
// tests/Unit/UserTest.php
namespace Tests\Unit;

use App\Models\User;
use PHPUnit\Framework\TestCase;

class UserTest extends TestCase
{
    public function test_user_has_full_name_accessor()
    {
        $user = new User([
            'first_name' => 'John',
            'last_name' => 'Doe',
        ]);

        $this->assertEquals('John Doe', $user->full_name);
    }
}
```

### Assertions de Teste Comuns
```php
// Resposta HTTP
$response->assertStatus(200);
$response->assertOk();              // Status 200
$response->assertCreated();         // Status 201
$response->assertNoContent();       // Status 204
$response->assertRedirect('/home');
$response->assertNotFound();        // Status 404
$response->assertUnauthorized();    // Status 401
$response->assertForbidden();       // Status 403

// Conteúdo da Resposta
$response->assertSee('Texto na página');
$response->assertSeeText('Texto sem HTML');
$response->assertDontSee('Texto ausente');
$response->assertSeeInOrder(['Primeiro', 'Segundo']);
$response->assertJsonCount(3);
$response->assertJson(['nome' => 'valor']);
$response->assertJsonPath('user.name', 'John');
$response->assertJsonStructure(['user' => ['id', 'name']]);

// Banco de Dados
$this->assertDatabaseHas('users', ['email' => 'john@example.com']);
$this->assertDatabaseMissing('users', ['email' => 'john@example.com']);
$this->assertDatabaseCount('users', 10);
$this->assertModelExists($user);
$this->assertModelMissing($deletedUser);

// Views
$response->assertViewIs('users.index');
$response->assertViewHas('users');
$response->assertViewHasAll(['users', 'posts']);
$response->assertViewMissing('admins');
```

## Artisan Personalizados

### Criando um Comando Artisan
```php
// app/Console/Commands/SendEmails.php
namespace App\Console\Commands;

use App\Models\User;
use App\Notifications\WeeklyNewsletter;
use Illuminate\Console\Command;

class SendEmails extends Command
{
    protected $signature = 'emails:send {user? : ID do usuário} {--queue : Executar em fila}';
    
    protected $description = 'Envia emails para usuários';

    public function handle()
    {
        // Argumento opcional
        $userId = $this->argument('user');
        
        // Opção booleana
        $shouldQueue = $this->option('queue');
        
        // Interativo com o usuário
        if (!$userId && $this->confirm('Enviar para todos os usuários?')) {
            $users = User::all();
        } else {
            $users = User::when($userId, function ($query) use ($userId) {
                return $query->where('id', $userId);
            })->get();
        }
        
        $count = $users->count();
        
        // Barra de progresso
        $bar = $this->output->createProgressBar($count);
        $bar->start();
        
        foreach ($users as $user) {
            if ($shouldQueue) {
                $user->notify((new WeeklyNewsletter())->onQueue('emails'));
            } else {
                $user->notify(new WeeklyNewsletter());
            }
            
            $bar->advance();
        }
        
        $bar->finish();
        
        // Mensagem de feedback colorida
        $this->info("\nEmails enviados para $count usuários!");
        
        return Command::SUCCESS;
    }
}
```

### Registrando o Comando
```php
// app/Console/Kernel.php
protected $commands = [
    \App\Console\Commands\SendEmails::class,
];
```

### Agendando Tarefas
```php
// app/Console/Kernel.php
protected function schedule(Schedule $schedule)
{
    // Executar comando Artisan
    $schedule->command('emails:send --queue')
             ->dailyAt('8:00')
             ->withoutOverlapping()
             ->emailOutputTo('admin@example.com');
    
    // Executar job
    $schedule->job(new ProcessPodcasts)
             ->everyTwoHours();
    
    // Executar closure
    $schedule->call(function () {
        DB::table('recent_users')->delete();
    })->daily();
    
    // Intervalos comuns
    $schedule->command('command')->everyMinute();
    $schedule->command('command')->everyFiveMinutes();
    $schedule->command('command')->everyFifteenMinutes();
    $schedule->command('command')->everyThirtyMinutes();
    $schedule->command('command')->hourly();
    $schedule->command('command')->daily();
    $schedule->command('command')->dailyAt('13:00');
    $schedule->command('command')->twiceDaily(8, 20);
    $schedule->command('command')->weekly();
    $schedule->command('command')->weekdays();
    $schedule->command('command')->weekends();
    $schedule->command('command')->monthly();
    $schedule->command('command')->lastDayOfMonth();
    $schedule->command('command')->quarterly();
    $schedule->command('command')->yearly();
    
    // Timezone
    $schedule->command('command')
             ->weekly()
             ->timezone('America/Sao_Paulo');
}
```

## Helpers Úteis

### Funções Helpers
```php
// Arrays e Objetos
array_get($array, 'key', 'default');  // Obter valor de array aninhado
collect($array);  // Criar Collection
data_get($target, 'key');  // Obter valor de array/objeto aninhado
object_get($object, 'key');  // Obter propriedade de objeto

// Strings
__('messages.welcome');  // Traduzir
str($string)->camel();   // camelCase
str($string)->snake();   // snake_case
str($string)->studly();  // StudlyCase
str($string)->slug();    // url-amigavel
str($string)->plural();  // pluralizar
str($string)->singular(); // singularizar
str($string)->limit(100); // Limitar tamanho
Str::random(40);  // String aleatória

// Rotas e URLs
route('users.show', $user);  // Gerar URL para rota nomeada
url('/posts');  // Gerar URL completa
secure_url('/posts');  // Gerar URL HTTPS
asset('img/photo.jpg');  // URL para recurso público
mix('js/app.js');  // URL para recurso versionado (Laravel Mix)

// Diversos
now();  // Instância Carbon para data/hora atual
today();  // Instância Carbon para hoje
auth()->user();  // Usuário autenticado
config('app.timezone');  // Valor de configuração
csrf_field();  // Campo CSRF para formulários
dd($var);  // Dump and Die (depuração)
dump($var);  // Var dump (depuração)
env('APP_ENV', 'production');  // Variável de ambiente
logger('Debug message');  // Registrar mensagem no log
session('key');  // Obter valor da sessão
old('input');  // Valor anterior de input
bcrypt('senha');  // Hash de senha
report($exception);  // Reportar exceção
rescue(function () { /* Código que pode falhar */ }, $rescue, $report = true);  // Capturar exceções
retry(5, function () { /* Código que pode falhar */ }, 100);  // Tentar novamente
```

### Collections
```php
// Criando Collections
$collection = collect([1, 2, 3]);

// Métodos Comuns
$collection->all();  // Todos os itens como array
$collection->avg();  // Média dos valores
$collection->chunk(2);  // Dividir em pedaços
$collection->collapse();  // Achatar arrays aninhados
$collection->combine($values);  // Combinar com outro array
$collection->contains('value');  // Verificar se contém
$collection->count();  // Contar itens
$collection->diff([2, 3]);  // Diferença entre collections
$collection->each(function ($item) {});  // Loop em cada item
$collection->filter(function ($item) {});  // Filtrar itens
$collection->first();  // Primeiro item
$collection->firstWhere('key', 'value');  // Primeiro que satisfaz condição
$collection->flatMap(function ($item) {});  // Map + flatten
$collection->flatten();  // Achatar arrays aninhados
$collection->flip();  // Trocar chaves e valores
$collection->forget('key');  // Remover item por chave
$collection->get('key');  // Obter item por chave
$collection->groupBy('key');  // Agrupar por chave
$collection->has('key');  // Verificar se chave existe
$collection->implode('key', ',');  // Juntar valores como string
$collection->intersect([1, 2]);  // Interseção entre collections
$collection->isEmpty();  // Verificar se vazio
$collection->isNotEmpty();  // Verificar se não vazio
$collection->keyBy('id');  // Usar campo como chave
$collection->keys();  // Obter chaves
$collection->last();  // Último item
$collection->map(function ($item) {});  // Transformar itens
$collection->max();  // Valor máximo
$collection->merge([4, 5]);  // Mesclar com outro array
$collection->min();  // Valor mínimo
$collection->pluck('name');  // Extrair valores de uma chave
$collection->prepend(0);  // Adicionar ao início
$collection->pull('key');  // Obter e remover item
$collection->push(4);  // Adicionar ao final
$collection->put('key', 'value');  // Definir item por chave
$collection->random();  // Item aleatório
$collection->reduce(function ($carry, $item) {});  // Reduzir a um valor
$collection->reject(function ($item) {});  // Inverso do filter
$collection->reverse();  // Inverter ordem
$collection->search('value');  // Buscar item e retornar chave
$collection->shift();  // Remover e retornar primeiro item
$collection->shuffle();  // Embaralhar itens
$collection->skip(1);  // Pular N itens
$collection->slice(1, 2);  // Extrair fatia
$collection->sort();  // Ordenar
$collection->sortBy('key');  // Ordenar por chave
$collection->sortByDesc('key');  // Ordenar por chave (decrescente)
$collection->splice(1, 2);  // Remover e retornar fatia
$collection->sum();  // Somar valores
$collection->take(2);  // Pegar N primeiros
$collection->toArray();  // Converter para array
$collection->toJson();  // Converter para JSON
$collection->transform(function ($item) {});  // Modificar todos os itens
$collection->unique();  // Remover duplicações
$collection->values();  // Resetar chaves para índices numéricos
$collection->where('key', 'value');  // Filtrar por chave/valor
$collection->whereIn('key', [1, 2]);  // Filtrar por chave/valores
$collection->zip([4, 5]);  // Mesclar com arrays

// Pipeline de methods
$result = $collection
    ->filter(function ($item) { return $item > 1; })
    ->map(function ($item) { return $item * 2; })
    ->reduce(function ($carry, $item) { return $carry + $item; }, 0);
```

## Pacotes Úteis

### Laravel Livewire
```bash
composer require livewire/livewire
```
Framework para criar interfaces dinâmicas sem escrever JavaScript

### Laravel Breeze
```bash
composer require laravel/breeze --dev
php artisan breeze:install
```
Implementação simples de autenticação com Blade/Tailwind

### Laravel Jetstream
```bash
composer require laravel/jetstream
php artisan jetstream:install livewire
# ou
php artisan jetstream:install inertia
```
Scaffold de autenticação completo com recursos avançados

### Laravel Horizon
```bash
composer require laravel/horizon
php artisan horizon:install
```
Interface de gerenciamento de filas Redis

### Laravel Telescope
```bash
composer require laravel/telescope --dev
php artisan telescope:install
```
Ferramenta de depuração para desenvolvimento

### Laravel Sanctum
```bash
composer require laravel/sanctum
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
php artisan migrate
```
Sistema de autenticação leve para SPAs e APIs

### Laravel Scout
```bash
composer require laravel/scout
php artisan vendor:publish --provider="Laravel\Scout\ScoutServiceProvider"
```
Busca full-text para modelos Eloquent

### Laravel Socialite
```bash
composer require laravel/socialite
```
Login OAuth com provedores sociais (Google, Facebook, Twitter, etc.)

### Spatie Permission
```bash
composer require spatie/laravel-permission
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"
php artisan migrate
```
Gerenciamento de permissões e funções

### Laravel Excel
```bash
composer require maatwebsite/excel
```
Importação e exportação de arquivos Excel e CSV

### Laravel Debugbar
```bash
composer require barryvdh/laravel-debugbar --dev
```
Barra de depuração para desenvolvimento
