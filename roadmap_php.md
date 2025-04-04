# Roadmap de 15 Dias para Aprender PHP (Full Stack com PostgreSQL)

**Perfil do aluno:** Desenvolvedor já experiente em Python, precisando de agilidade para dominar PHP puro (sem frameworks) em um contexto full stack. Cada dia foca em um tópico-chave, com prática hands-on para consolidar o conhecimento. O conteúdo é direto ao ponto, aproveitando sua familiaridade prévia com lógica de programação.

## Dia 1: **Introdução ao PHP**

-   **Visão geral do PHP:** Entenda o que é PHP e como funciona. PHP é uma linguagem de script **lado do servidor**, amplamente usada para desenvolvimento web e embutida no HTML. Diferentemente de Python, o PHP é executado dentro de um servidor web (Apache, Nginx ou o servidor embutido do PHP) e gera HTML dinâmico que é enviado ao navegador.
    
-   **Setup do ambiente:** Instale um servidor web com suporte a PHP. Opções comuns: **XAMPP** ou **Wamp** (que incluem Apache + PHP + PostgreSQL) ou use o servidor interno do PHP (comando `php -S localhost:8000`). Configure o ambiente e verifique se o PHP está rodando (`php -v` no terminal para checar a versão).
    
-   **Olá Mundo:** Crie seu primeiro script PHP. Por exemplo, em um arquivo **`index.php`**, escreva:
    
    ```php
    <?php 
      echo "Olá, eu sou um script PHP!"; 
    ?>
    
    ```
    
    Salve dentro da pasta do servidor (por exemplo, `htdocs` no XAMPP) e acesse via navegador (ex: `http://localhost/index.php`). Assim você confirma que ambiente está OK e já vê a sintaxe básica de inserir PHP em HTML.
    
-   **Comparação com Python:** Note as diferenças básicas: em PHP, código é delimitado por `<?php ... ?>` dentro de HTML, ponto e vírgula terminam comandos, e a tipagem é dinâmica (como Python) porém PHP faz conversões de tipo automáticas em muitos casos. Compreender esses contrastes vai ajudá-lo a transferir seus conhecimentos de Python para PHP rapidamente.
    

## Dia 2: **Sintaxe Básica e Estrutura da Linguagem**

-   **Variáveis e tipos:** Aprenda a declarar variáveis em PHP (prefixo `$`). Não é necessário declarar tipo (tipagem dinâmica). Ex: `$nome = "João"; $idade = 30;`. Conheça os principais **tipos primitivos**: string, inteiro, float, booleano, arrays (falaremos mais sobre arrays no Dia 3) e o tipo _NULL_.
    
-   **Saída de dados:** Use `echo` ou `print` para imprimir valores. Ex: `echo "Nome: $nome";`. Veja como concatenar strings (operador `.` em PHP, diferente do `+` ou f-string no Python).
    
-   **Operadores:** Estude operadores aritméticos (`+ - * / %`), de comparação (`== === != !== < > <= >=`) e lógicos (`&&`, `||`, `!`). Fique atento às diferenças: `===` verifica igualdade **e** mesmo tipo (em Python, `==` já faz isso, pois Python é fortemente tipado). Em PHP, `==` faz conversão de tipo ao comparar, então prefira `===` para evitar surpresas.
    
-   **Sintaxe básica:** Entenda a estrutura de um arquivo PHP: você pode misturar HTML e PHP livremente. Ao abrir a tag `<?php`, você insere código PHP; ao fechá-la `?>`, volta ao contexto de HTML. Por convenção, scripts puramente lógicos podem ser só PHP (começando com `<?php` e sem fechar a tag se não for seguir HTML).
    
-   **Comentários:** Use `// comentário` ou `# comentário` para linhas simples, e `/* ... */` para comentários de bloco, semelhante ao Python (que usa `#` e `''' '''`).
    

## Dia 3: **Manipulação de Dados e Arrays**

-   **Arrays em PHP:** Os _arrays_ do PHP funcionam como listas **e** dicionários do Python em uma só estrutura. São na verdade mapas ordenados. Você pode ter arrays **indexados** (numéricos) ou **associativos** (chave=>valor). Ex: `$nums = [1, 2, 3]; $aluno = ["nome"=>"Ana", "idade"=>20];`. Pratique criar e acessar arrays: `echo $nums[0]; echo $aluno["nome"];`.
    
-   **Funções úteis de array:** Explore funções nativas para manipular arrays (equivalentes a métodos de lista/dict do Python). Por exemplo: `array_push` (adiciona elemento no final), `array_pop` (remove último), `count` (tamanho do array), `foreach` (itera sobre arrays, veremos mais em estruturas de controle). Experimente ordenar arrays com `sort`/`rsort` ou extrair partes com `array_slice`.
    
-   **Strings e manipulação de texto:** Revise funções para strings (já que texto é dado comum em web). Conheça `strlen` (tamanho da string), `substr` (substrings), `str_replace` (substituição), dentre outras. Compare com funções Python (len(), slicing, replace()) para ver analogias.
    
-   **Manipulação de datas:** PHP tem a classe DateTime e funções como `date()` para formatar datas. Se o projeto lida com datas, teste obter data atual: `echo date("Y-m-d H:i:s");`.
    
-   **Conversão de tipos:** Saiba converter tipos manualmente se preciso: `(int)$var`, `(string)$var`, etc., embora PHP converta muita coisa automaticamente. Entenda situações em que conversões automáticas ocorrem (por exemplo, strings numéricas em contexto aritmético viram números).
    

## Dia 4: **Estruturas de Controle e Funções**

-   **Condicionais:** Use `if`, `else`, `elseif` em PHP (sintaxe similar ao Python, tirando os `:` e indentação). Ex:
    
    ```php
    if ($idade >= 18) {
        echo "Maior de idade";
    } elseif ($idade >= 12) {
        echo "Adolescente";
    } else {
        echo "Criança";
    }
    
    ```
    
    Também conheça o `switch` (equivalente ao `match/case` em outras linguagens) para múltiplas condições sobre a mesma variável.
    
-   **Loops:** Pratique o loop `while`, `do...while` e principalmente `for` e `foreach`. Exemplo de `for`:
    
    ```php
    for ($i = 0; $i < 5; $i++) {
        echo $i;
    }
    
    ```
    
    O `foreach` é muito usado para iterar arrays de forma simples:
    
    ```php
    foreach ($aluno as $chave => $valor) {
        echo "$chave: $valor";
    }
    
    ```
    
    (No caso de arrays indexados, você pode usar `foreach ($lista as $valor)` diretamente).
    
-   **Funções:** Aprenda a declarar funções com `function nomeFunc($param1, $param2 = "valorPadrao") { ... }`. PHP não exige anotar retorno ou tipos, mas a partir do PHP 7 você pode opcionalmente declarar tipos de parâmetros e retorno (ex.: `function soma(int $a, int $b): int { return $a+$b; }`).
    
-   **Escopo:** Assim como Python, variáveis dentro de funções são locais. Para acessar uma variável global dentro da função, use `global $nomeVar;` ou passe como parâmetro. Entenda quando usar variáveis globais ou superglobais (como `$_POST`, etc.).
    
-   **Include/Require:** Introduza a modularização de código separando funções/utilidades em arquivos diferentes. Use `include 'arquivo.php';` ou `require 'arquivo.php';` para incorporar arquivos (similar a imports em Python, mas aqui insere o código do arquivo). `require` lança erro fatal se o arquivo não for encontrado (parando a execução), enquanto `include` emite um aviso e segue em frente – prefira `require` para arquivos essenciais.
    

## Dia 5: **Manipulação de Formulários e Requisições HTTP**

-   **Formulários HTML:** Revise rapidamente a sintaxe de formulários HTML (`<form>` e seus `<input>`). Crie um formulário simples (método GET e POST) para enviar dados ao PHP. Exemplo:
    
    ```html
    <form method="POST" action="recebe.php">
      Nome: <input name="nome" type="text">
      <button type="submit">Enviar</button>
    </form>
    
    ```
    
-   **Superglobais $_GET e $_POST:** No PHP, os dados enviados via formulário são acessados através dos arrays superglobais. Em `recebe.php`, capture os valores: `$_GET['nome']` (para method="GET") ou `$_POST['nome']` (para POST). Lembre-se: essas variáveis existem de forma global e associativa (chave é o **name** do input).
    
-   **Tratando entrada:** Faça validação simples dos dados do formulário. Verifique se campos foram preenchidos (`isset($_POST['campo'])`) e aplique sanitização básica (por agora, use `htmlspecialchars()` para evitar caracteres especiais no output, prevenindo XSS). **Dica:** Deixe a parte aprofundada de segurança para o Dia 10, aqui foque em capturar e usar os dados.
    
-   **Requisições condicionadas:** Use `$_SERVER['REQUEST_METHOD']` para checar se o acesso é GET ou POST, podendo assim num mesmo script mostrar um formulário (se GET) ou processar dados (se POST). Isso permite que o próprio script trate a lógica de apresentação x processamento.
    
-   **Feedback ao usuário:** Após processar um formulário (por ex., salvar dados), envie feedback. Você pode redirecionar usando `header("Location: pagina.php")` ou simplesmente exibir uma mensagem de sucesso/erro. Aprenda a mandar headers _antes_ de qualquer saída HTML (pois PHP envia cabeçalhos HTTP automaticamente junto com a saída).
    

## Dia 6: **Sessões, Cookies e Autenticação Básica**

-   **Cookies:** Entenda o que são cookies HTTP – pequenos dados armazenados no navegador do usuário. Aprenda a definir cookies no PHP com `setcookie("nome", "valor", expiracao)`. Por exemplo:
    
    ```php
    setcookie("usuario", "Joao", time()+3600); // cookie que expira em 1 hora
    
    ```
    
    Esse cookie estará disponível em `$_COOKIE['usuario']` nas próximas requisições. Lembre-se que cookies só ficam disponíveis **depois** da resposta atual (ou seja, defina e veja o resultado ao recarregar a página).
    
-   **Sessões:** Sessions permitem armazenar dados no servidor para cada usuário (identificado por um cookie de sessão automático). Inicie a sessão com `session_start()` no topo do script (antes de qualquer output). Depois, use o array superglobal `$_SESSION` para guardar/ler dados. Ex:
    
    ```php
    session_start();
    $_SESSION['auth'] = true;
    $_SESSION['usuario'] = "Joao";
    
    ```
    
    Esses dados persistem entre páginas enquanto a sessão durar (ou até serem removidos com `session_destroy()` ou manualmente unset).
    
-   **Login simples (autenticação):** Combine formulários, sessões e cookies para criar um fluxo básico de autenticação. Por exemplo:
    
    -   Crie um formulário de login (usuário/senha).
        
    -   Ao enviar, verifique as credenciais (pode ser um usuário hardcodeado ou vindo do banco futuramente).
        
    -   Se corretas, faça `$_SESSION['logado'] = true` e armazene identificador do usuário.
        
    -   Proteja páginas verificando no topo: `session_start(); if(!isset($_SESSION['logado'])) { header("Location: login.php"); exit; }`.  
        Isso garante que só usuários autenticados vejam o conteúdo.
        
-   **Diferenças para Python (Flask/Django):** Em Python (Flask, Django), sessões e cookies também existem mas são gerenciadas via objetos request/response. Em PHP, você controla manualmente usando as funções nativas mencionadas. Conceitualmente é igual: cookies no cliente, sessão no servidor linkada por ID de sessão.
    

## Dia 7: **Conexão com PostgreSQL e Manipulação de Banco de Dados**

-   **Extensões do PHP para PostgreSQL:** Use a extensão **PDO (PHP Data Objects)** com driver PostgreSQL para conectar e executar queries de forma segura. (Alternativamente, PHP oferece funções nativas pg_* como `pg_connect`, mas vamos focar em PDO pela flexibilidade e segurança).
    
-   **Conectando ao banco:** Primeiro, assegure-se de ter o PostgreSQL rodando e crie um banco de testes. No PHP, faça a conexão:
    
    ```php
    $dsn = "pgsql:host=localhost;port=5432;dbname=seubanco;";
    $user = "seu_usuario"; $password = "sua_senha";
    try {
        $pdo = new PDO($dsn, $user, $password);
        // Definir modo de erro para excepcionar:
        $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
        echo "Conectado ao PostgreSQL!";
    } catch (PDOException $e) {
        echo "Erro na conexão: " . $e->getMessage();
    }
    
    ```
    
    Isso estabelece a conexão. Lembre de habilitar a extensão `pdo_pgsql` no _php.ini_ se ainda não estiver ativa (no XAMPP geralmente já vem ativa).
    
-   **Consultas SQL (SELECT):** Com a conexão `$pdo`, recupere dados. Exemplo:
    
    ```php
    $sql = "SELECT id, nome, email FROM clientes";
    foreach ($pdo->query($sql) as $row) {
        echo $row['nome'] . " - " . $row['email'];
    }
    
    ```
    
    Você pode iterar diretamente o objeto PDOStatement. Para consultas parametrizadas ou mais controle, use `prepare` e `execute`. Ex:
    
    ```php
    $stmt = $pdo->prepare("SELECT * FROM clientes WHERE id = :id");
    $stmt->execute([':id' => 5]);
    $cliente = $stmt->fetch(PDO::FETCH_ASSOC);
    
    ```
    
-   **Manipulação de dados (INSERT/UPDATE/DELETE):** Use prepare/execute também. Exemplo insert:
    
    ```php
    $stmt = $pdo->prepare("INSERT INTO clientes(nome, email) VALUES (:nome, :email)");
    $stmt->execute([':nome' => "Ana", ':email' => "ana@example.com"]);
    
    ```
    
    Após um INSERT, você pode obter o último ID inserido via `$pdo->lastInsertId()`. Para UPDATE/DELETE, é similar: prepara a query com placeholders, executa com os dados. Capture erros com try/catch (ex.: violação de chave primária, etc.).
    
-   **Fechando conexão:** Em PHP, a conexão fecha automaticamente no fim do script. Para explícitamente fechar, atribua null: `$pdo = null;`. Na prática, não é obrigatório, mas bom saber.
    

## Dia 8: **CRUD Completo com PHP e PostgreSQL**

-   **Projeto CRUD**: Hoje você vai criar uma pequena aplicação CRUD (Create, Read, Update, Delete) para praticar todo o fluxo de front + back + banco. Escolha um domínio simples, por exemplo, **Gerenciador de Tarefas** ou **Cadastro de Produtos**.
    
-   **C – Create (Criar):** Desenvolva um formulário HTML para adicionar um registro (nova tarefa ou produto). Ao enviar (POST), um script PHP insere os dados na tabela PostgreSQL correspondente (`INSERT`). Após inserir, redirecione de volta para a listagem ou exiba mensagem de sucesso.
    
-   **R – Read (Ler/Listar):** Crie uma página que lista todos os registros do banco em uma tabela HTML. Use SELECT no PHP para buscar todos os itens e monte o HTML dinamicamente com `echo` ou escapando dentro do bloco PHP. Exiba também opções “Editar” e “Excluir” ao lado de cada item.
    
-   **U – Update (Atualizar):** Ao clicar em “Editar”, leve o usuário a um formulário preenchido com os dados atuais (use um SELECT pelo ID do item e preencha os `<input value="...">`). Ao salvar (POST), execute um UPDATE na tabela. Use a estrutura do Dia 5 (verificar se POST, etc.) para reaproveitar a página tanto para mostrar o form preenchido (GET) quanto para processar a atualização (POST).
    
-   **D – Delete (Deletar):** Implemente a exclusão. Pode ser via um formulário com botão "Excluir" para cada item ou via link que passa o ID por query string (GET). Ao receber a ação, faça um DELETE no banco. Por segurança, confirme a ação (pode ser um simples `if(isset($_GET['delete_id'])) { ... }`). Após excluir, redirecione ou recarregue a lista.
    
-   **Organização básica:** Mesmo sem um framework, comece a separar seu código. Por exemplo, crie um arquivo `db.php` para inicializar a conexão PDO (inclua ele em todas as páginas que precisam acessar o banco). Isso evita repetir código. Mantenha também templates simples para header/footer da página (HTML padrão) para reutilizar.
    

## Dia 9: **Manipulação de Arquivos e Uploads**

-   **Sistema de arquivos:** Aprenda a ler e escrever arquivos no servidor com PHP. Funções úteis: `fopen`, `fwrite`, `fread`, `file_get_contents`, `file_put_contents`. Por exemplo, para salvar log de acessos:
    
    ```php
    $linha = date('Y-m-d H:i:s')." - acesso de ".$_SERVER['REMOTE_ADDR']."\n";
    file_put_contents("acessos.log", $linha, FILE_APPEND);
    
    ```
    
    E para ler um arquivo inteiro: `$conteudo = file_get_contents("acessos.log");`. Lembre de ter permissões adequadas na pasta.
    
-   **Uploads de arquivos (formulário):** Crie um formulário HTML com `enctype="multipart/form-data"` e um `<input type="file" name="arquivo">` para o usuário selecionar um arquivo. Ao enviar, PHP disponibiliza o superglobal `$_FILES`. Ele contém informações como nome original, tipo, tamanho e caminho temporário.
    
-   **Processando upload:** No PHP, verifique `$_FILES['arquivo']['error']` (0 significa upload ok). Então mova o arquivo do temp para um destino final usando `move_uploaded_file($_FILES['arquivo']['tmp_name'], "uploads/".$_FILES['arquivo']['name']);`. Crie antes a pasta **uploads/** e dê permissão de escrita a ela.
    
-   **Validando arquivos:** Implemente checagens básicas de segurança: tamanho máximo (campo `size` em `$_FILES`), tipo MIME e extensão (por exemplo, só permitir `.jpg`/`.png` se for imagem). Evite usar o nome original diretamente para salvar — poderia sobrescrever algo ou conter caracteres indesejados. Você pode gerar um nome único (ex: usando `uniqid()` ou um hash do tempo) ou ao menos filtrar caracteres perigosos.
    
-   **Leitura de arquivos CSV/Texto:** Como exercício adicional, tente ler um arquivo CSV simples usando `fgetcsv` e inserir os dados no banco, ou gerar um CSV a partir de dados do banco. Isso integra manipulação de arquivo com banco de dados, consolidando conhecimentos.
    

## Dia 10: **Segurança Básica no PHP**

-   **Injeção de SQL:** Reforce o uso de **queries parametrizadas** (prepared statements com bind de variáveis) ao invés de concatenar strings SQL com dados diretamente. Lembre que no Dia 7 usamos PDO com placeholders (`:nome`, `:email` etc.) – isso previne SQL Injection efetivamente. Sempre valide e filtre dados antes de usá-los na query.
    
-   **XSS (Cross-Site Scripting):** Ao exibir dados vindos do usuário na página, sanitizar é obrigatório. Use `htmlspecialchars($dado)` para converter caracteres especiais em entidades HTML, evitando que scripts maliciosos injete código no navegador de outros usuários. Por exemplo, se um usuário malicioso digitar `<script>alert('xss')</script>` em um campo e você simplesmente fizer `echo $campo`, isso executaria o JS no browser de quem visse. `htmlspecialchars` neutraliza `<` `>` etc., prevenindo a execução.
    
-   **CSRF (Cross-Site Request Forgery):** Em aplicações maiores, ataques CSRF podem ser um problema (falsificação de requisição atravessada). Para uma proteção básica, implemente tokens de sessão nos formulários críticos. Exemplo: gere um token aleatório `$_SESSION['csrf'] = bin2hex(random_bytes(32));` e inclua no form `<input type="hidden" name="csrf_token" value="...">`. No processamento, verifique se `$_POST['csrf_token']` bate com `$_SESSION['csrf']`. Isso garante que o form enviado veio da sua aplicação.
    
-   **Gerenciamento de Senhas:** Se no seu projeto final houver cadastro de usuários, **não guarde senhas em texto puro!** Use `password_hash()` para guardar o hash e `password_verify()` para verificar. O PHP por padrão usa BCrypt via essas funções, o que é adequado para ambientes de produção.
    
-   **Configurações do PHP:** Conheça configurações importantes de segurança no `php.ini`: `display_errors` (em produção deve ser OFF para não vazar informações sensíveis), `allow_url_include` (mantenha OFF, previne incluir código remoto), `session.cookie_httponly` (ON para impedir acesso JavaScript aos cookies de sessão), etc. Essas práticas básicas fortalecem sua aplicação contra ataques comuns.
    

## Dia 11: **Conceitos Básicos de MVC e Organização de Código**

-   **Arquitetura MVC:** Embora esteja codificando em PHP "puro", é valioso conhecer o padrão **Model-View-Controller** usado nos frameworks. Em PHP, **Model** seriam as partes que acessam dados (consultas no PostgreSQL, por ex.), **View** é a saída (HTML gerado, templates) e **Controller** é a lógica que conecta as duas coisas (scripts que recebem requisições, chamam modelos e carregam views).
    
-   **Separação de responsabilidades:** Refatore seu projeto CRUD dos dias anteriores introduzindo uma organização simples:
    
    -   **Model:** crie funções PHP separadas para operações no banco (por ex: `buscarTodosClientes()`, `inserirCliente($dados)` etc., que internamente usam PDO). Mantenha essas funções em um arquivo separado (ex: `modelo.php`) e inclua onde necessário.
        
    -   **View:** separe o layout HTML em arquivos de template. Por exemplo, um `header.php` com topo da página (doctype, cabeçalho, menu) e um `footer.php` com fechamento de tags. Use `include` para envolver o conteúdo dinâmico entre header e footer. Para partes de listagem ou formulários, você pode ter arquivos específicos e apenas preencher os dados.
        
    -   **Controller:** use seus arquivos principais (ex: `clientes.php`, `clientes_editar.php`) como controladores: eles chamam funções do Model e invocam as Views. Por exemplo, em `clientes.php` (listagem) você faria `$lista = buscarTodosClientes();` e depois inclui um `clientes_view.php` que itera `$lista` e exibe em HTML.
        
-   **Benefícios:** Essa estrutura evita duplicação de código e facilita manutenção. Se precisar mudar a forma de acesso a dados, você ajusta o Model. Se for alterar o layout, mexe só na View. Para um desenvolvedor Python acostumado com Django/Flask, isso faz sentido (Django segue MTV, similar ao MVC).
    
-   **Organização em pastas:** Estruture seu projeto em diretórios lógicos: por exemplo, uma pasta **models/**, **views/**, **controllers/** (ou **public/** para arquivos acessíveis diretamente). Em aplicações PHP puras sem framework, o entry point geralmente são os arquivos na raiz (ou public) que atuam como controladores. Essa organização limpa prepara terreno para eventualmente aprender um framework MVC (como Laravel), mas mesmo sem ele seu código estará compreensível.
    

## Dia 12: **Integração com APIs Externas**

-   **Cliente HTTP no PHP:** Para consumir APIs de terceiros (por exemplo, uma API de clima, mapas, etc.), use ferramentas do PHP para fazer requisições HTTP. A opção mais simples embutida é `file_get_contents` com URL ou `fopen`, mas o ideal é usar cURL ou wrappers.
    
-   **Usando cURL:** Ative a extensão cURL no PHP (se já não estiver). Faça uma requisição GET simples:
    
    ```php
    $ch = curl_init("https://viacep.com.br/ws/01001000/json/");
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $resposta = curl_exec($ch);
    curl_close($ch);
    $dados = json_decode($resposta, true);
    echo "Cidade: ".$dados["localidade"];
    
    ```
    
    Nesse exemplo, consumimos a API pública ViaCEP para obter um endereço a partir do CEP e exibimos a cidade. Entenda o fluxo: inicializar curl, configurar opções, executar e depois decodificar JSON de resposta com `json_decode` (definindo `true` para obter array associativo).
    
-   **Tratamento de erros e HTTP:** Verifique se a requisição foi bem-sucedida. Com cURL, use `curl_errno`/`curl_error` ou verifique o HTTP status se a API fornece. Para chamadas seguras (HTTPS), cURL normalmente já verifica certificado; se tiver problemas em dev, há opções para ignorar verificação (CURLOPT_SSL_VERIFYPEER), mas evite em produção.
    
-   **Autenticação em APIs:** Aprenda a chamar APIs que exigem token ou chave. Isso pode ser via headers HTTP. Com cURL, adiciona-se headers com `curl_setopt($ch, CURLOPT_HTTPHEADER, ["Authorization: Bearer SEU_TOKEN"]);`. Para simplificar, teste com alguma API gratuita que não exige auth ou usa chave na URL (por exemplo, API de clima OpenWeather com chave de API).
    
-   **APIs REST vs SOAP:** Tenha noção de formatos: a maioria hoje é REST/JSON. PHP tem facilidades para JSON (funções nativas) e também suporta XML se necessário (funções XML ou SimpleXML). Se topar com uma API SOAP, o PHP também possui extensão SOAP, mas é menos comum atualmente. Foque em REST/JSON, que cobre grande parte dos casos modernos.
    

## Dia 13: **Introdução ao uso de JavaScript e AJAX no PHP**

-   **Relembrando o front-end:** Revise rapidamente JavaScript básico no navegador (se não tiver familiaridade). Como desenvolvedor Python, pense no JS como a linguagem para manipular o DOM e fazer chamadas assíncronas. Nenhum servidor é necessário para JS rodar – ele executa no cliente (navegador).
    
-   **Comunicação AJAX:** Aprenda a fazer requisições assíncronas do front-end para o back-end PHP sem recarregar a página. Usando **Fetch API** (moderna) ou **XMLHttpRequest** (legado) no JavaScript:
    
    ```js
    fetch("dados.php?produto_id=123")
      .then(res => res.json())
      .then(data => {
         console.log(data.nome);
         // atualizar parte da pagina com os dados recebidos
      });
    
    ```
    
    No exemplo, fazemos GET para "dados.php" passando um parâmetro. O PHP (`dados.php`) receberá `$_GET['produto_id']`, buscará no banco (por exemplo, detalhes do produto) e retornará um JSON com `echo json_encode($resultado)`. Assim, o JS recebe `data` já como objeto.
    
-   **Lado PHP para AJAX:** No script PHP que atende a requisição AJAX, determine o formato de resposta. Se espera JSON, defina header apropriado `header('Content-Type: application/json');` e use `json_encode`. Se for enviar HTML fragmento, prepare a saída normalmente (por exemplo, retornar um `<div>...</div>` preenchido).
    
-   **Atualizando a página dinamicamente:** Com a resposta recebida no JavaScript, manipule o DOM para exibir novos dados sem reload. Por exemplo, preencher um campo, adicionar uma linha em tabela, mostrar uma mensagem, etc., usando `document.querySelector` e ajustando `innerHTML` ou criando elementos.
    
-   **Exemplo prático:** Implemente no seu projeto um pequeno recurso AJAX. Por exemplo, em um sistema de tarefas, faça um botão "Marcar como Concluída" que via AJAX informa ao servidor e atualiza a interface (muda cor da tarefa para riscada). Ou um campo de busca que sugere resultados enquanto o usuário digita (fazendo requests ao servidor filtrando dados). Isso demonstra a integração full stack: PHP fornecendo dados para o front-end dinamicamente.
    

## Dia 14: **Deploy básico de uma aplicação PHP**

-   **Preparando o ambiente de produção:** Para colocar sua aplicação online, escolha uma solução de hospedagem. Pode ser um host compartilhado com suporte a PHP (muitos oferecem painel com PHP e banco de dados), um VPS configurado manualmente (LAMP stack: Linux, Apache, MySQL/PostgreSQL, PHP) ou plataformas na nuvem. Como é um projeto simples, uma hospedagem compartilhada ou um droplet na DigitalOcean são opções viáveis.
    
-   **Configurações no servidor:** Se for um servidor Linux, instale Apache/Nginx, PHP e PostgreSQL. Configure o virtual host para seu site (por ex, apontar domínio ou IP para a pasta do projeto). Coloque os arquivos PHP no diretório público (geralmente `/var/www/html` no Apache).
    
-   **Migrando o banco de dados:** Importe/exporte seu banco PostgreSQL. Você pode usar dump (`pg_dump` no local e `psql` no servidor) ou usar uma ferramenta GUI. Assegure-se de atualizar as credenciais de conexão no seu código (`host`, `user`, `password`, etc.) para as do servidor. Em produção, essas credenciais devem ficar em local seguro – idealmente em variáveis de ambiente ou arquivo de config fora do webroot, mas para simplicidade mantenha no config PHP mesmo, cuidando para não expor.
    
-   **Permissões e segurança no deploy:** Verifique permissões de pastas (por exemplo, pasta de upload deve ser gravável pelo servidor web, mas de preferência não listável publicamente). Coloque `.htaccess` negando acesso a arquivos sensíveis (como arquivos .php que não deveriam ser acessados diretamente, ou arquivos de config). Desative o `display_errors` no php.ini de produção para não vazar caminho interno em caso de erro — use logs para depuração.
    
-   **Teste pós-deploy:** Após subir tudo, teste a aplicação online. Faça um checklist: páginas carregando, formulários enviando, conexão com DB funcionando, sessões mantendo estado, recursos AJAX operantes e e-mails (se houver envio de email, por exemplo) entregando. Resolva eventuais diferenças de versão de PHP ou banco. Com tudo ok, parabéns: sua aplicação PHP está no ar!
    

## Dia 15: **Projeto Final para Consolidar os Aprendizados**

-   **Desafio final:** Dedique o dia para construir (ou finalizar) um projeto completo que utilize todos os tópicos abordados. Pode ser uma extensão do CRUD que você vem evoluindo ou um novo mini-projeto. O importante é cobrir **front-end + back-end + banco**.
    
-   **Requisitos sugeridos:** Inclua autenticação de usuário (sessão/cookies), operações CRUD com PostgreSQL, upload de ao menos um tipo de arquivo, uso de uma API externa, e interação AJAX para melhorar a UX. Por exemplo, um **Blog simples**: usuários cadastram (com senha hash), logam, postam artigos (com upload de imagem de capa), podem editar/excluir posts, e há um widget lateral consumindo uma API de terceiros (por ex., mostrando clima ou cotação) via AJAX sem recarregar a página.
    
-   **Planejamento do código:** Estruture o projeto em pastas (Dia 11) antes de começar a codar tudo. Liste as páginas necessárias (ex: `index.php` lista posts, `post_novo.php` formulário de novo post, `login.php`, etc.). Codifique passo a passo, testando cada parte. Reaproveite código escrito nos dias anteriores: sua biblioteca de funções (DB, utilidades) e templates de layout.
    
-   **Teste integral:** Com o projeto pronto, faça testes completos em fluxo: cadastro -> login -> cria post -> visualiza listagem -> edita -> deleta -> logout, etc. Teste inputs inválidos (segurança), confira no banco se os dados estão corretos (password hash, por exemplo). Corrija bugs encontrados e melhore onde notar problemas de usabilidade.
    
-   **Reflexão e próximos passos:** Após concluir, você terá passado por todos os aspectos essenciais do desenvolvimento web com PHP puro. Revisite pontos que ficaram menos claros e consulte a documentação oficial do PHP para aprofundar conceitos avançados conforme necessário. A partir daqui, você pode se sentir confortável para pegar um projeto PHP, ou mesmo aprender um framework PHP moderno (como Laravel ou Symfony) com base sólida. Bom trabalho e bons códigos!
