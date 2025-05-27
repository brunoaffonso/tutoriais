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