O Livewire é uma biblioteca full-stack para Laravel que permite criar interfaces dinâmicas e interativas usando apenas PHP, sem precisar escrever JavaScript. Ele funciona como uma ponte entre o backend PHP e o frontend, permitindo que você construa componentes reativos de forma similar ao que faria com Vue.js ou React, mas mantendo toda a lógica no servidor.

## Como funciona o Livewire

O Livewire utiliza AJAX para comunicação entre o frontend e backend, mas de forma transparente. Quando uma propriedade do componente muda ou um método é chamado, o Livewire automaticamente envia uma requisição para o servidor, processa a mudança e atualiza apenas as partes necessárias da página.

## Conceitos principais

**Componentes**: São classes PHP que estendem `Livewire\Component` e possuem propriedades públicas que são automaticamente sincronizadas com o frontend.

**Propriedades reativas**: Qualquer propriedade pública da classe é automaticamente "observada" e mudanças nela refletem na interface.

**Métodos**: Podem ser chamados diretamente do frontend usando diretivas como `wire:click`.

## Instalação e configuração básica

```bash
composer require livewire/livewire
php artisan livewire:publish --config
```

## Exemplos práticos

Vou criar alguns exemplos progressivos para você entender melhor:

---
```php
<?php

namespace App\Http\Livewire;

use Livewire\Component;

class Counter extends Component
{
    // Propriedade reativa - será sincronizada automaticamente
    public $count = 0;

    // Método que pode ser chamado do frontend
    public function increment()
    {
        $this->count++;
    }

    public function decrement()
    {
        $this->count--;
    }

    public function reset()
    {
        $this->count = 0;
    }

    public function render()
    {
        return view('livewire.counter');
    }
}
```
---
```html
{{-- resources/views/livewire/counter.blade.php --}}
<div class="p-6 bg-white rounded-lg shadow-md">
    <h2 class="text-2xl font-bold mb-4">Contador: {{ $count }}</h2>
    
    <div class="space-x-2">
        <button wire:click="increment" 
                class="px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600">
            +
        </button>
        
        <button wire:click="decrement" 
                class="px-4 py-2 bg-red-500 text-white rounded hover:bg-red-600">
            -
        </button>
        
        <button wire:click="reset" 
                class="px-4 py-2 bg-gray-500 text-white rounded hover:bg-gray-600">
            Reset
        </button>
    </div>
</div>
```
---
```php
<?php

namespace App\Http\Livewire;

use Livewire\Component;

class TodoList extends Component
{
    public $todos = [];
    public $newTodo = '';

    // Regras de validação
    protected $rules = [
        'newTodo' => 'required|min:3|max:255',
    ];

    // Mensagens personalizadas de validação
    protected $messages = [
        'newTodo.required' => 'O campo tarefa é obrigatório.',
        'newTodo.min' => 'A tarefa deve ter pelo menos 3 caracteres.',
        'newTodo.max' => 'A tarefa não pode ter mais de 255 caracteres.',
    ];

    public function mount()
    {
        // Dados iniciais (normalmente viriam do banco)
        $this->todos = [
            ['id' => 1, 'text' => 'Aprender Livewire', 'completed' => false],
            ['id' => 2, 'text' => 'Fazer compras', 'completed' => true],
        ];
    }

    public function addTodo()
    {
        // Valida os dados
        $this->validate();

        // Adiciona novo todo
        $this->todos[] = [
            'id' => count($this->todos) + 1,
            'text' => $this->newTodo,
            'completed' => false,
        ];

        // Limpa o campo
        $this->newTodo = '';

        // Opcional: Mostrar mensagem de sucesso
        session()->flash('message', 'Tarefa adicionada com sucesso!');
    }

    public function toggleTodo($index)
    {
        $this->todos[$index]['completed'] = !$this->todos[$index]['completed'];
    }

    public function removeTodo($index)
    {
        unset($this->todos[$index]);
        $this->todos = array_values($this->todos); // Reindexar array
    }

    public function render()
    {
        return view('livewire.todo-list');
    }
}
```
---
```html
{{-- resources/views/livewire/todo-list.blade.php --}}
<div class="max-w-md mx-auto bg-white rounded-lg shadow-md p-6">
    <h2 class="text-2xl font-bold mb-4">Lista de Tarefas</h2>

    {{-- Mensagem de sucesso --}}
    @if (session()->has('message'))
        <div class="bg-green-100 border border-green-400 text-green-700 px-4 py-3 rounded mb-4">
            {{ session('message') }}
        </div>
    @endif

    {{-- Formulário para adicionar nova tarefa --}}
    <form wire:submit.prevent="addTodo" class="mb-4">
        <div class="flex gap-2">
            <input type="text" 
                   wire:model="newTodo" 
                   placeholder="Digite uma nova tarefa..."
                   class="flex-1 border border-gray-300 rounded px-3 py-2 focus:outline-none focus:border-blue-500">
            
            <button type="submit" 
                    class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
                Adicionar
            </button>
        </div>
        
        {{-- Erro de validação --}}
        @error('newTodo')
            <span class="text-red-500 text-sm mt-1 block">{{ $message }}</span>
        @enderror
    </form>

    {{-- Lista de tarefas --}}
    <div class="space-y-2">
        @forelse($todos as $index => $todo)
            <div class="flex items-center gap-2 p-2 border rounded {{ $todo['completed'] ? 'bg-gray-100' : 'bg-white' }}">
                <input type="checkbox" 
                       wire:click="toggleTodo({{ $index }})"
                       {{ $todo['completed'] ? 'checked' : '' }}
                       class="rounded">
                
                <span class="flex-1 {{ $todo['completed'] ? 'line-through text-gray-500' : '' }}">
                    {{ $todo['text'] }}
                </span>
                
                <button wire:click="removeTodo({{ $index }})" 
                        class="text-red-500 hover:text-red-700">
                    ✕
                </button>
            </div>
        @empty
            <p class="text-gray-500 text-center py-4">Nenhuma tarefa encontrada.</p>
        @endforelse
    </div>

    <div class="mt-4 text-sm text-gray-600">
        Total: {{ count($todos) }} tarefas
    </div>
</div>
```
---
```php
<?php

namespace App\Http\Livewire;

use Livewire\Component;
use Livewire\WithPagination;
use App\Models\User;

class UserSearch extends Component
{
    use WithPagination;

    public $search = '';
    public $perPage = 5;

    protected $paginationTheme = 'bootstrap';

    // Sempre que $search mudar, resetar a paginação
    public function updatingSearch()
    {
        $this->resetPage();
    }

    public function updatingPerPage()
    {
        $this->resetPage();
    }

    public function render()
    {
        $users = User::query()
            ->when($this->search, function($query) {
                $query->where('name', 'like', '%' . $this->search . '%')
                      ->orWhere('email', 'like', '%' . $this->search . '%');
            })
            ->paginate($this->perPage);

        return view('livewire.user-search', compact('users'));
    }
}

// Exemplo com método mais avançado para filtros múltiplos
class AdvancedUserSearch extends Component
{
    use WithPagination;

    public $search = '';
    public $status = '';
    public $sortField = 'name';
    public $sortDirection = 'asc';
    public $perPage = 10;

    protected $queryString = [
        'search' => ['except' => ''],
        'status' => ['except' => ''],
        'sortField' => ['except' => 'name'],
        'sortDirection' => ['except' => 'asc'],
    ];

    public function updatingSearch()
    {
        $this->resetPage();
    }

    public function sortBy($field)
    {
        if ($this->sortField === $field) {
            $this->sortDirection = $this->sortDirection === 'asc' ? 'desc' : 'asc';
        } else {
            $this->sortDirection = 'asc';
        }
        
        $this->sortField = $field;
    }

    public function render()
    {
        $users = User::query()
            ->when($this->search, function($query) {
                $query->where(function($subQuery) {
                    $subQuery->where('name', 'like', '%' . $this->search . '%')
                             ->orWhere('email', 'like', '%' . $this->search . '%');
                });
            })
            ->when($this->status, function($query) {
                $query->where('status', $this->status);
            })
            ->orderBy($this->sortField, $this->sortDirection)
            ->paginate($this->perPage);

        return view('livewire.advanced-user-search', compact('users'));
    }
}
```
---
```html
{{-- resources/views/livewire/user-search.blade.php --}}
<div class="container mx-auto p-4">
    <div class="bg-white rounded-lg shadow p-6">
        <h2 class="text-2xl font-bold mb-4">Buscar Usuários</h2>

        {{-- Controles de busca --}}
        <div class="flex gap-4 mb-4">
            <div class="flex-1">
                <input type="text" 
                       wire:model.live.debounce.300ms="search"
                       placeholder="Buscar por nome ou email..."
                       class="w-full border border-gray-300 rounded px-3 py-2 focus:outline-none focus:border-blue-500">
            </div>
            
            <select wire:model.live="perPage" 
                    class="border border-gray-300 rounded px-3 py-2">
                <option value="5">5 por página</option>
                <option value="10">10 por página</option>
                <option value="25">25 por página</option>
            </select>
        </div>

        {{-- Loading indicator --}}
        <div wire:loading wire:target="search" class="text-blue-500 mb-2">
            Buscando...
        </div>

        {{-- Resultados --}}
        @if($users->count() > 0)
            <div class="overflow-x-auto">
                <table class="min-w-full bg-white">
                    <thead class="bg-gray-50">
                        <tr>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">Nome</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">Email</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">Criado em</th>
                        </tr>
                    </thead>
                    <tbody class="divide-y divide-gray-200">
                        @foreach($users as $user)
                            <tr>
                                <td class="px-6 py-4 whitespace-nowrap">{{ $user->name }}</td>
                                <td class="px-6 py-4 whitespace-nowrap">{{ $user->email }}</td>
                                <td class="px-6 py-4 whitespace-nowrap">{{ $user->created_at->format('d/m/Y') }}</td>
                            </tr>
                        @endforeach
                    </tbody>
                </table>
            </div>

            {{-- Paginação --}}
            <div class="mt-4">
                {{ $users->links() }}
            </div>
        @else
            <div class="text-center py-8 text-gray-500">
                @if($search)
                    Nenhum usuário encontrado para "{{ $search }}"
                @else
                    Nenhum usuário encontrado.
                @endif
            </div>
        @endif
    </div>
</div>
```
---
```php
<?php

// 1. CRIANDO COMPONENTES
// Para criar um novo componente Livewire:
// php artisan make:livewire ComponentName

// 2. INCLUINDO NO BLADE
// Em qualquer view Blade, você pode incluir um componente assim:

/*
{{- Em uma view blade qualquer -}}
@extends('layouts.app')

@section('content')
<div class="container">
    {{-- Incluindo o componente contador --}}
    <livewire:counter />
    
    {{-- Ou usando a sintaxe alternativa --}}
    @livewire('counter')
    
    {{-- Passando parâmetros iniciais --}}
    <livewire:todo-list :initial-todos="['Tarefa 1', 'Tarefa 2']" />
</div>
@endsection
*/

// 3. DIRETIVAS IMPORTANTES DO LIVEWIRE

/*
{{-- WIRE:MODEL - Vinculação de dados bidirecional --}}
<input type="text" wire:model="propriedade">

{{-- WIRE:MODEL.LIVE - Atualização em tempo real --}}
<input type="text" wire:model.live="search">

{{-- WIRE:MODEL.DEBOUNCE - Atraso para evitar muitas requisições --}}
<input type="text" wire:model.live.debounce.300ms="search">

{{-- WIRE:CLICK - Chamar métodos do componente --}}
<button wire:click="metodo">Clique</button>

{{-- WIRE:SUBMIT - Para formulários --}}
<form wire:submit.prevent="salvar">

{{-- WIRE:LOADING - Mostrar durante carregamento --}}
<div wire:loading>Carregando...</div>

{{-- WIRE:LOADING.TARGET - Loading específico para um elemento --}}
<div wire:loading wire:target="buscar">Buscando...</div>

{{-- WIRE:IGNORE - Ignorar elemento nas atualizações --}}
<div wire:ignore>
    {{-- Conteúdo que não deve ser atualizado pelo Livewire --}}
</div>
*/

// 4. CICLO DE VIDA DOS COMPONENTES

class ExampleComponent extends Component
{
    public $name;
    
    // Executado uma única vez quando o componente é montado
    public function mount($initialName = '')
    {
        $this->name = $initialName;
    }
    
    // Executado sempre que uma propriedade é atualizada
    public function updated($propertyName)
    {
        // Executado para qualquer propriedade
    }
    
    // Executado quando uma propriedade específica é atualizada
    public function updatedName($value)
    {
        $this->name = strtoupper($value);
    }
    
    // Executado antes de uma propriedade ser atualizada
    public function updatingName($value)
    {
        // Validações ou modificações antes da atualização
    }
}

// 5. CONFIGURAÇÃO NO LAYOUT PRINCIPAL
/*
{{-- No seu layout principal (ex: resources/views/layouts/app.blade.php) --}}
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Minha App Laravel</title>
    
    {{-- Styles do Livewire --}}
    @livewireStyles
</head>
<body>
    @yield('content')
    
    {{-- Scripts do Livewire (antes do fechamento do body) --}}
    @livewireScripts
</body>
</html>
*/

// 6. ROTAS PARA OS COMPONENTES
/*
// routes/web.php
Route::get('/counter', function () {
    return view('pages.counter');
});

Route::get('/todos', function () {
    return view('pages.todos');
});

Route::get('/users', function () {
    return view('pages.users');
});
*/
```
---

## Principais vantagens do Livewire

**Desenvolvimento mais rápido**: Você escreve apenas PHP, sem necessidade de APIs separadas ou JavaScript complexo.

**Reatividade**: As mudanças são refletidas automaticamente na interface sem page reload.

**Integração nativa**: Funciona perfeitamente com o ecossistema Laravel (validação, autorização, etc.).

**SEO-friendly**: Como o conteúdo é renderizado no servidor, é melhor para SEO que SPAs tradicionais.

## Dicas importantes

**Performance**: Use `wire:model.live.debounce` para inputs de busca para evitar muitas requisições.

**Validação**: Aproveite todo o sistema de validação do Laravel diretamente nos componentes.

**Loading states**: Use `wire:loading` para melhorar a experiência do usuário.

**Paginação**: O `WithPagination` trait facilita muito a implementação de paginação reativa.

**Query strings**: Use `$queryString` para manter o estado na URL.

Para começar, eu recomendo que você crie um projeto de teste e implemente o componente contador primeiro, depois evolua para os exemplos mais complexos. O Livewire tem uma curva de aprendizado bem suave e você logo estará criando interfaces complexas apenas com PHP!

---

# Livewire & Blade - Cheat Sheet Completo

## 📋 Diretivas Wire: Essenciais

### `wire:model` - Vinculação de Dados
```blade
<!-- Vinculação básica -->
<input wire:model="nome">

<!-- Vinculação em tempo real -->
<input wire:model.live="busca">

<!-- Com debounce (atraso) -->
<input wire:model.live.debounce.500ms="busca">

<!-- Lazy (só atualiza no blur/enter) -->
<input wire:model.lazy="email">

<!-- Para arrays/objetos -->
<input wire:model="usuario.nome">
<input wire:model="tags.0">
```

### `wire:click` - Eventos de Clique
```blade
<!-- Chamar método básico -->
<button wire:click="salvar">Salvar</button>

<!-- Passar parâmetros -->
<button wire:click="deletar({{ $id }})">Deletar</button>

<!-- Múltiplos parâmetros -->
<button wire:click="editar({{ $id }}, '{{ $nome }}')">Editar</button>

<!-- Modificadores -->
<button wire:click.prevent="enviar">Prevenir padrão</button>
<button wire:click.stop="acao">Parar propagação</button>
<button wire:click.once="inicializar">Executar uma vez</button>
<button wire:click.self="fechar">Só se clicado diretamente</button>
```

### `wire:submit` - Formulários
```blade
<!-- Formulário básico -->
<form wire:submit="salvar">
    <input wire:model="nome">
    <button type="submit">Enviar</button>
</form>

<!-- Prevenir reload da página -->
<form wire:submit.prevent="salvar">
    <!-- inputs -->
</form>
```

### `wire:keydown` - Eventos de Teclado
```blade
<!-- Teclas específicas -->
<input wire:keydown.enter="buscar">
<input wire:keydown.escape="limpar">
<input wire:keydown.tab="proximoCampo">

<!-- Combinações -->
<input wire:keydown.ctrl.s="salvarRapido">
<input wire:keydown.shift.tab="campoAnterior">

<!-- Qualquer tecla -->
<input wire:keydown="teclaPress">
```

### `wire:change` - Mudanças em Elementos
```blade
<!-- Select boxes -->
<select wire:change="filtrarPor">
    <option value="todos">Todos</option>
    <option value="ativos">Ativos</option>
</select>

<!-- Checkboxes -->
<input type="checkbox" wire:change="toggleTodos">

<!-- File inputs -->
<input type="file" wire:change="uploadArquivo">
```

## 🔄 Estados de Loading

### `wire:loading` - Indicadores de Carregamento
```blade
<!-- Loading geral -->
<div wire:loading>Carregando...</div>

<!-- Loading para ação específica -->
<div wire:loading wire:target="salvar">Salvando...</div>

<!-- Loading para múltiplas ações -->
<div wire:loading wire:target="salvar,deletar">Processando...</div>

<!-- Loading para propriedad -->
<div wire:loading wire:target="busca">Buscando...</div>

<!-- Modificadores de display -->
<div wire:loading.inline>Carregando...</div>
<div wire:loading.block>Carregando...</div>
<div wire:loading.flex>Carregando...</div>

<!-- Remover elemento durante loading -->
<button wire:loading.remove wire:target="enviar">Enviar</button>

<!-- Mostrar apenas durante loading -->
<div wire:loading.delay>Aguarde...</div>

<!-- Loading com delay (evita flicker) -->
<div wire:loading.delay.shortest>Carregando...</div> <!-- 50ms -->
<div wire:loading.delay.shorter>Carregando...</div>  <!-- 100ms -->
<div wire:loading.delay.short>Carregando...</div>    <!-- 150ms -->
<div wire:loading.delay.long>Carregando...</div>     <!-- 500ms -->
<div wire:loading.delay.longer>Carregando...</div>   <!-- 1000ms -->
<div wire:loading.delay.longest>Carregando...</div>  <!-- 2000ms -->
```

### `wire:loading` com Atributos
```blade
<!-- Desabilitar durante loading -->
<button wire:loading.attr="disabled" wire:target="salvar">
    Salvar
</button>

<!-- Adicionar classes durante loading -->
<button wire:loading.class="opacity-50" wire:target="processar">
    Processar
</button>

<!-- Remover classes durante loading -->
<button wire:loading.class.remove="bg-blue-500" wire:target="acao">
    Executar
</button>
```

## 🎛️ Controle de Renderização

### `wire:ignore` - Ignorar Atualizações
```blade
<!-- Ignorar elemento completamente -->
<div wire:ignore>
    <script>
        // JavaScript que não deve ser re-executado
        initializeChart();
    </script>
</div>

<!-- Ignorar apenas filhos -->
<div wire:ignore.self>
    <div id="mapa"></div>
</div>
```

### `wire:key` - Forçar Re-renderização
```blade
<!-- Em loops, para performance -->
@foreach($items as $item)
    <div wire:key="item-{{ $item->id }}">
        {{ $item->nome }}
    </div>
@endforeach

<!-- Para componentes dinâmicos -->
<livewire:dynamic-component wire:key="comp-{{ $id }}" />
```

## 🔄 Eventos Customizados

### Disparar Eventos
```php
// No componente PHP
$this->dispatch('usuario-salvo', userId: $user->id);
$this->dispatch('refresh-lista');

// Para componente específico
$this->dispatch('atualizar')->to('nome-do-componente');

// Para todos os componentes
$this->dispatch('notificacao', message: 'Sucesso!')->to('*');
```

### Escutar Eventos
```blade
<!-- No Blade -->
<div wire:on="usuario-salvo">
    Usuário foi salvo!
</div>

<!-- Com JavaScript -->
<script>
    Livewire.on('notificacao', (event) => {
        alert(event.message);
    });
</script>
```

```php
// No componente PHP
protected $listeners = ['refresh-lista' => 'carregarLista'];

// Ou usando atributo
#[On('usuario-atualizado')]
public function handleUserUpdated($userId) {
    // lógica aqui
}
```

## 📊 Paginação

### Trait WithPagination
```php
use Livewire\WithPagination;

class MinhaLista extends Component
{
    use WithPagination;
    
    protected $paginationTheme = 'bootstrap'; // ou 'tailwind'
    
    public function render()
    {
        return view('livewire.minha-lista', [
            'items' => Item::paginate(10)
        ]);
    }
}
```

```blade
<!-- Na view -->
{{ $items->links() }}

<!-- Personalizada -->
{{ $items->appends(request()->query())->links() }}
```

## 📁 Upload de Arquivos

### `wire:model` com Arquivos
```blade
<!-- Upload simples -->
<input type="file" wire:model="foto">

<!-- Upload múltiplo -->
<input type="file" wire:model="fotos" multiple>

<!-- Preview de imagem -->
@if ($foto)
    Prévia:
    <img src="{{ $foto->temporaryUrl() }}" width="200">
@endif

<!-- Progress bar -->
<div wire:loading wire:target="foto">Enviando...</div>
```

```php
// No componente
use Livewire\WithFileUploads;

class Upload extends Component
{
    use WithFileUploads;
    
    public $foto;
    
    public function salvar()
    {
        $this->validate([
            'foto' => 'image|max:1024', // 1MB Max
        ]);
        
        $path = $this->foto->store('fotos');
    }
}
```

## 🔍 Validação em Tempo Real

### Validação Automática
```php
class FormComponent extends Component
{
    public $email;
    public $nome;
    
    protected $rules = [
        'email' => 'required|email',
        'nome' => 'required|min:3',
    ];
    
    protected $messages = [
        'email.required' => 'Email é obrigatório.',
        'email.email' => 'Email deve ser válido.',
    ];
    
    // Validar quando propriedade muda
    public function updated($propertyName)
    {
        $this->validateOnly($propertyName);
    }
}
```

```blade
<!-- Mostrar erros -->
<input wire:model.live="email">
@error('email') 
    <span class="error">{{ $message }}</span> 
@enderror

<!-- Classe condicional -->
<input wire:model="nome" 
       class="@error('nome') border-red-500 @enderror">
```

## 🎨 Classes e Estilos Dinâmicos

### Classes Condicionais com Blade
```blade
<!-- Blade básico -->
<div class="{{ $ativo ? 'bg-green-500' : 'bg-gray-500' }}">

<!-- Múltiplas condições -->
<div @class([
    'p-4',
    'border',
    'bg-green-500' => $sucesso,
    'bg-red-500' => $erro,
    'opacity-50' => $desabilitado,
])>

<!-- Com arrays -->
<div @class([
    'btn',
    'btn-primary' => $tipo === 'primary',
    'btn-secondary' => $tipo === 'secondary',
])>
```

### Estilos Dinâmicos
```blade
<!-- Style condicional -->
<div @style([
    'color: red' => $erro,
    'font-weight: bold' => $importante,
    'display: none' => !$visivel,
])>

<!-- Style com variáveis -->
<div style="width: {{ $progresso }}%; background-color: {{ $cor }};">
```

## 📋 Loops e Condicionais Avançados

### Blade Loops
```blade
<!-- Loop básico -->
@foreach($items as $item)
    <div wire:key="{{ $item->id }}">{{ $item->nome }}</div>
@endforeach

<!-- Com índice -->
@foreach($items as $index => $item)
    <div wire:key="{{ $index }}">{{ $loop->iteration }}: {{ $item->nome }}</div>
@endforeach

<!-- Variável $loop -->
@foreach($items as $item)
    @if($loop->first)
        <h3>Primeiro item</h3>
    @endif
    
    <div class="{{ $loop->odd ? 'bg-gray-100' : 'bg-white' }}">
        {{ $item->nome }}
        
        @if($loop->last)
            <p>Último item</p>
        @endif
    </div>
@endforeach

<!-- Com fallback -->
@forelse($items as $item)
    <div>{{ $item->nome }}</div>
@empty
    <p>Nenhum item encontrado</p>
@endforelse
```

### Condicionais
```blade
<!-- If básico -->
@if($usuario->isAdmin())
    <button>Admin Panel</button>
@elseif($usuario->isModerador())
    <button>Moderar</button>
@else
    <span>Usuário comum</span>
@endif

<!-- Unless (se não) -->
@unless($usuario->isBanned())
    <p>Bem-vindo!</p>
@endunless

<!-- Empty/Isset -->
@empty($lista)
    <p>Lista vazia</p>
@endempty

@isset($variavel)
    <p>{{ $variavel }}</p>
@endisset
```

## 🔧 Métodos Úteis do Componente

### Redirecionamentos
```php
// Redirect simples
return redirect()->to('/dashboard');

// Redirect com rota nomeada
return redirect()->route('home');

// Redirect com parâmetros
return redirect()->route('post.show', $post);

// Redirect com flash message
return redirect()->route('posts.index')
    ->with('success', 'Post criado!');
```

### Flash Messages
```php
// No componente
session()->flash('message', 'Operação realizada!');
session()->flash('error', 'Algo deu errado!');

// Na view
@if (session()->has('message'))
    <div class="alert alert-success">
        {{ session('message') }}
    </div>
@endif
```

### Reset de Propriedades
```php
// Reset propriedade específica
$this->reset('nome');

// Reset múltiplas
$this->reset(['nome', 'email']);

// Reset todas (exceto as protegidas)
$this->reset();
```

## 🚀 Performance e Otimização

### Lazy Loading
```blade
<!-- Carregar componente quando visível -->
<livewire:large-component lazy />

<!-- Com placeholder -->
<livewire:data-table lazy>
    <div>Carregando tabela...</div>
</livewire:data-table>
```

### Defer (Diferir Carregamento)
```blade
<!-- Carregar após página principal -->
<livewire:analytics-widget defer />
```

### Query String
```php
class SearchComponent extends Component
{
    public $search = '';
    public $page = 1;
    
    protected $queryString = [
        'search' => ['except' => ''],
        'page' => ['except' => 1],
    ];
}
```

## 🎯 Dicas Pro

### Polling (Atualização Automática)
```blade
<!-- Atualizar a cada 5 segundos -->
<div wire:poll.5s>
    Dados em tempo real: {{ $contador }}
</div>

<!-- Polling em método específico -->
<div wire:poll.2s="atualizarDados">
    {{ $ultimaAtualizacao }}
</div>

<!-- Parar polling quando invisível -->
<div wire:poll.visible="refresh">
    Conteúdo
</div>
```

### Offline Detection
```blade
<!-- Mostrar quando offline -->
<div wire:offline>
    Você está offline!
</div>

<!-- Esconder quando offline -->
<div wire:online>
    Conteúdo online
</div>
```

### Dirty Detection (Mudanças não salvas)
```blade
<!-- Mostrar quando há mudanças -->
<div wire:dirty>
    Você tem alterações não salvas
</div>

<!-- Para campo específico -->
<div wire:dirty.target="nome">
    Nome foi alterado
</div>
```

### Alpine.js Integration
```blade
<!-- Combinar Livewire com Alpine -->
<div x-data="{ open: false }">
    <button @click="open = !open" 
            wire:click="loadData">
        Toggle
    </button>
    
    <div x-show="open" 
         wire:loading.class="opacity-50">
        {{ $data }}
    </div>
</div>
```

## 📱 Responsividade e Mobile

### Touch Events
```blade
<!-- Eventos de toque -->
<div wire:touchstart="iniciarTouch"
     wire:touchend="finalizarTouch"
     wire:touchmove="moverTouch">
    Área de toque
</div>
```

### Viewport Meta
```blade
<!-- No layout -->
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<!-- Prevenir zoom em inputs -->
<input wire:model="busca" 
       style="font-size: 16px;"> <!-- iOS não faz zoom se >= 16px -->
```

---

## 🏆 Comandos Artisan Úteis

```bash
# Criar componente
php artisan make:livewire NomeDoComponente

# Criar com inline view
php artisan make:livewire NomeDoComponente --inline

# Listar todos os componentes
php artisan livewire:list

# Publicar arquivos do Livewire
php artisan livewire:publish --config
php artisan livewire:publish --assets

# Limpar cache do Livewire
php artisan livewire:clear-cache
```

Este cheat sheet cobre as funcionalidades mais importantes do Livewire. Mantenha-o como referência rápida durante o desenvolvimento!

---

Este cheat sheet cobre praticamente todas as funcionalidades importantes do Livewire e suas integrações com Blade. Algumas observações importantes para maximizar seu uso:

## Dicas de Uso Eficiente

**Combine diretivas**: Muitas diretivas podem ser usadas juntas, como `wire:model.live.debounce.300ms` para busca em tempo real otimizada.

**Use wire:key em loops**: Essencial para performance quando você tem listas dinâmicas que podem ser reordenadas.

**Aproveite os modificadores**: `.prevent`, `.stop`, `.once`, `.self` são muito úteis para controlar comportamentos específicos.

**Loading states são cruciais**: Sempre forneça feedback visual durante operações que podem demorar.

**Validação em tempo real**: Use `validateOnly()` para validar campos conforme o usuário digita.

**Eventos customizados**: Muito poderosos para comunicação entre componentes ou integração com JavaScript.

## Padrões Recomendados

**Estrutura de pastas**: Organize componentes em subpastas quando necessário (`php artisan make:livewire Forms/UserForm`).

**Nomeação**: Use PascalCase para classes e kebab-case para uso no Blade.

**Performance**: Use `lazy` e `defer` para componentes pesados que não precisam carregar imediatamente.

**SEO**: Lembre-se que Livewire renderiza no servidor, então é SEO-friendly por padrão.

Este cheat sheet será sua referência rápida conforme você desenvolve. Com o tempo, esses padrões se tornarão naturais e você estará criando interfaces reativas complexas muito rapidamente!

---

## O que é o `wire:model`?

O `wire:model` cria uma **vinculação bidirecional** (two-way data binding) entre um elemento HTML e uma propriedade do seu componente Livewire. Isso significa:

- Quando você digita no input, a propriedade PHP é automaticamente atualizada
- Quando a propriedade PHP muda, o valor do input é automaticamente atualizado na tela

```php
// No componente PHP
class MinhaClasse extends Component 
{
    public $nome = 'João'; // Esta propriedade será sincronizada
}
```

```blade
<!-- No Blade -->
<input wire:model="nome" type="text">
<p>Olá, {{ $nome }}!</p> <!-- Atualiza automaticamente -->
```

O "model" aqui não se refere aos Models do Eloquent, mas sim ao conceito de **Model** do padrão MVC - representa os dados/estado do componente.

---

## Explicação Detalhada dos Itens do Cheat Sheet

### 🔄 **wire:model** (Vinculação de Dados)

**`.live`** - Atualiza instantaneamente conforme você digita
```blade
<input wire:model.live="busca"> <!-- Cada letra digitada atualiza -->
```

**`.debounce`** - Adiciona atraso para evitar muitas requisições
```blade
<input wire:model.live.debounce.300ms="busca"> <!-- Espera 300ms após parar de digitar -->
```

**`.lazy`** - Só atualiza quando sai do campo (blur) ou pressiona Enter
```blade
<input wire:model.lazy="email"> <!-- Mais eficiente para campos que não precisam de atualização imediata -->
```

### 🖱️ **wire:click** (Eventos de Clique)

**Modificadores de comportamento:**
- `.prevent` - Equivale ao `preventDefault()` do JavaScript
- `.stop` - Para a propagação do evento (não "borbulha" para elementos pais)
- `.once` - Executa apenas uma vez, depois para de funcionar
- `.self` - Só executa se clicar diretamente no elemento (não em filhos)

```blade
<form wire:submit.prevent="salvar"> <!-- Não recarrega a página -->
<div wire:click.self="fechar"> <!-- Só fecha se clicar no fundo, não no conteúdo -->
```

### ⌨️ **wire:keydown** (Eventos de Teclado)

Permite capturar teclas específicas sem JavaScript:
```blade
<input wire:keydown.enter="buscar"> <!-- Enter para buscar -->
<input wire:keydown.escape="limpar"> <!-- ESC para limpar -->
<input wire:keydown.ctrl.s="salvarRapido"> <!-- Ctrl+S para salvar -->
```

### 🔄 **wire:loading** (Estados de Carregamento)

**Por que é importante?** Livewire faz requisições AJAX. Durante essas requisições, o usuário pode não saber que algo está acontecendo.

**`.target`** - Especifica qual ação está sendo monitorada
```blade
<div wire:loading wire:target="buscar">Buscando...</div> <!-- Só aparece quando o método 'buscar' está executando -->
```

**Modificadores de delay** - Evitam "flicker" (piscar rápido) em operações muito rápidas
```blade
<div wire:loading.delay.short>Carregando...</div> <!-- Só aparece se demorar mais que 150ms -->
```

### 🎛️ **wire:ignore** (Controle de Renderização)

**Por que usar?** Quando o Livewire atualiza o componente, ele re-renderiza o HTML. Isso pode quebrar JavaScript de terceiros.

```blade
<div wire:ignore>
    <div id="mapa"></div>
    <script>
        // Este JavaScript não será perdido nas atualizações
        initializeGoogleMaps();
    </script>
</div>
```

### 🔑 **wire:key** (Otimização de Performance)

Em loops, o Livewire precisa saber qual elemento HTML corresponde a qual item do array. Sem `wire:key`, ele pode re-renderizar tudo.

```blade
@foreach($usuarios as $usuario)
    <div wire:key="user-{{ $usuario->id }}"> <!-- Livewire sabe que este div representa este usuário específico -->
        {{ $usuario->nome }}
    </div>
@endforeach
```

### 📡 **Eventos Customizados**

Permitem comunicação entre componentes ou com JavaScript:

```php
// Componente A dispara evento
$this->dispatch('usuario-criado', userId: $user->id);
```

```php
// Componente B escuta evento
protected $listeners = ['usuario-criado' => 'atualizarLista'];
```

### 📊 **WithPagination** (Paginação)

Trait que adiciona paginação reativa aos seus componentes:
```php
use WithPagination;

public function render()
{
    return view('livewire.lista', [
        'items' => Item::paginate(10) // Paginação automática
    ]);
}
```

### 📁 **WithFileUploads** (Upload de Arquivos)

Trait que permite upload de arquivos com preview e validação:
```php
use WithFileUploads;

public $foto;

public function updatedFoto()
{
    $this->validate(['foto' => 'image|max:1024']);
}
```

### ✅ **Validação em Tempo Real**

**`validateOnly()`** - Valida apenas um campo específico, não todos
```php
public function updatedEmail()
{
    $this->validateOnly('email'); // Só valida email, não nome, telefone, etc.
}
```

### 🎨 **@class e @style** (Classes/Estilos Dinâmicos)

**@class** - Nova diretiva do Blade para classes condicionais:
```blade
<div @class([
    'btn',                    // Sempre presente
    'btn-primary' => $ativo,  // Só se $ativo for true
    'btn-disabled' => !$ativo // Só se $ativo for false
])>
```

### 🔄 **Polling** (Atualização Automática)

Atualiza o componente automaticamente em intervalos:
```blade
<div wire:poll.5s> <!-- Atualiza a cada 5 segundos -->
    Visitantes online: {{ $contadorOnline }}
</div>
```

**`.visible`** - Só faz polling quando o elemento está visível (economiza recursos)

### 📱 **Offline/Online Detection**

Detecta quando o usuário perde/recupera conexão:
```blade
<div wire:offline>Você está offline!</div>
<div wire:online>Conexão restaurada!</div>
```

### 🔍 **Dirty Detection**

Detecta quando há mudanças não salvas:
```blade
<div wire:dirty>Você tem alterações não salvas</div>
```

### 🚀 **Lazy Loading**

**`lazy`** - Componente só carrega quando necessário (economiza recursos iniciais)
**`defer`** - Componente carrega após o carregamento principal da página

```blade
<livewire:dashboard-heavy lazy /> <!-- Só carrega quando scrollar até ele -->
<livewire:analytics defer />      <!-- Carrega depois que a página principal carregar -->
```

---

## Resumo do Fluxo

1. **wire:model** sincroniza dados entre PHP e HTML
2. **wire:click/keydown** capturam interações do usuário
3. **wire:loading** mostra feedback visual
4. **wire:ignore/key** otimizam performance
5. **Eventos** permitem comunicação
6. **Traits** adicionam funcionalidades complexas
7. **Modificadores** controlam comportamentos específicos

O Livewire abstrai toda a complexidade do JavaScript/AJAX, deixando você focar na lógica PHP!
