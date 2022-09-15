---
title: Usando IndexedDB
slug: Web/API/IndexedDB_API/Using_IndexedDB
tags:
  - API IndexedDB Tutorial Avançado
translation_of: Web/API/IndexedDB_API/Using_IndexedDB
original_slug: Web/API/IndexedDB_API/Usando_IndexedDB
---
<div class="summary">
<p>IndexedDB é uma forma de armazenar dados no navegador do usuário. Com ele você pode criar aplicações web com possibilidade de fazer query sem necessidade de conexão, suas aplicações podem funcionar tanto online quanto offline. </p>
</div>

<h2 id="Sobre_esse_documento">Sobre esse documento</h2>

<p>Esse tutorial utiliza a API assíncrona do IndexedDB. Se você não está familiarizado com IndexedDB, você pode ler <a href="/pt-BR/docs/IndexedDB/Basic_Concepts_Behind_IndexedDB">Conceitos básicos sobre IndexedDB</a>.</p>

<p>Para a documentação de referência, veja o artigo sobre <a href="/pt-BR/docs/IndexedDB" title="https://developer.mozilla.org/en/IndexedDB">API IndexedDB</a>, pois nele contém os tipos de objetos utilizados no IndexedDB,  como também métodos da API, tanto síncrona como assíncrona. </p>

<h2 id="pattern" name="pattern">Padrão básico</h2>

<p>O IndexedDB encoraja o uso do seguinte padrão:</p>

<ol>
 <li>Abrir um banco de dados.</li>
 <li>Criar um ObjectStore ao atualizar o banco. </li>
 <li>Iniciar uma transação e e faz um request para fazer alguma operação no banco, como adicionar ou obter dados.</li>
 <li>
  <div>Esperar a operação ser completada ouvindo algum evento DOM.</div>
 </li>
 <li>
  <div>Fazer algo com o resultado da query (que pode ser obtida pelo objeto request).</div>
 </li>
</ol>

<p>OK, então, agora com esses conceitos em mente, nós podemos fazer coisas mais concretas.</p>

<h2 id="open" name="open">Criando e estruturando o banco</h2>

<p>Pelo fato  da especificação ainda estar evoluindo, as implementações do IndexedDB tem prefixos de navegadores. Os navegadores podem ter implementações diferentes da API IndexedDB até a especificação ser consolidada. Mas uma vez que tudo chegar a um consenso, os navegadores tirarão seus prefixos. Atualmente, algumas implementações removeram o prefixo: Internet Explorer 10, Firefox 16, Chrome 24. Quando eles usam prefixo, navegadores baseados no Gecko usam o prefixo <code>moz</code>, enquanto os navegadores baseados no WebKit usam o prefixo <code>webkit</code>.</p>

<h3 id="Usando_uma_versão_experimental_do_IndexedDB">Usando uma versão experimental do IndexedDB</h3>

<p>Se você quiser testar seu código em navegadores que usam prefixo, você pode usar o código abaixo:  </p>

<pre class="brush: js">// Na linha abaixo, você deve incluir os prefixos do navegador que você vai testar.
window.indexedDB = window.indexedDB || window.mozIndexedDB || window.webkitIndexedDB || window.msIndexedDB;
// Não use "var indexedDB = ..." se você não está numa function.
// Posteriormente, você pode precisar de referências de algum objeto window.IDB*:
window.IDBTransaction = window.IDBTransaction || window.webkitIDBTransaction || window.msIDBTransaction;
window.IDBKeyRange = window.IDBKeyRange || window.webkitIDBKeyRange || window.msIDBKeyRange;
// (Mozilla nunca usou prefixo nesses objetos, então não precisamos window.mozIDB*)</pre>

<p>Tome cuidado, implementações prefixadas podem estar com bugs ou implementando especificações antigas. Portanto, não é recomendado usar em produção. É preferível não usar IndexedDB em navegadores antigos:</p>

<pre class="brush: js">if (!window.indexedDB) {
    window.alert("Seu navegador não suporta uma versão estável do IndexedDB. Alguns recursos não estarão disponíveis.");
}
</pre>

<h3 id="Abrindo_um_banco">Abrindo um banco</h3>

<p>Começamos todo o processo assim:</p>

<pre class="brush: js">// Abrindo o banco de dados
var request = window.indexedDB.open("DBteste", 3);
</pre>

<p>Abrir um banco é como qualquer outra operação — Você tem que "requerer (request)".</p>

<p>A requisição de abertura não abre o banco ou inicia a transação. A chamada da função <code>open()</code> retorna um objeto <a href="/en-US/docs/IndexedDB/IDBOpenDBRequest" title="/en-US/docs/IndexedDB/IDBOpenDBRequest"><code>IDBOpenDBRequest</code></a> com o resultado (success) ou um erro que você terá que tratar. Muitas outras funções assíncronas no IndexedDB fazem a mesma coisa - retornam um objeto <a href="/en-US/docs/IndexedDB/IDBRequest" title="/en-US/docs/IndexedDB/IDBRequest"><code style="font-size: 14px; color: rgb(51, 51, 51);">IDBRequest</code></a> com o resultado ou erro. O resultado para a função open é uma instância de <code style="font-size: 14px; color: rgb(51, 51, 51);"><a href="/en-US/docs/IndexedDB/IDBDatabase" title="/en-US/docs/IndexedDB/IDBDatabase">IDBDatabase</a>.</code></p>

<p>O segundo parâmetro para o método open é a versão do banco. A versão do banco determina seu schema — os registros no banco e sua estrutura. Se o banco não existe ainda, ele é criado pela operação <code>open</code>, então o evento<code> onupgradeneeded </code>é chamado e você cria o schema do banco nesse evento. Se o banco existe mas você está fornecendo um novo número de versão, o evento <code>onupgradeneeded </code>é chamado imediatamente, permitindo você tratar a atualização do schema. Para mais informações sobre isso veja <a href="#Updating_the_version_of_the_database">Updating the version of the database</a>.</p>

<div class="warning">
<p>O número de versão é um <code>unsigned long long</code>, o que significa que ele pode ver um inteiro muito grande. Isso também significa que você não pode usar float, pois ele será convertido em um inteiro pequeno e a transação pode não acontecer, ou o evento <code>upgradeneeded</code> pode não ser chamado. Então se você usar 2.4 como versão:</p>

<pre class="brush: js">var request = indexedDB.open("DBteste", 2.4); // não faça isso, pois a versão será convertida para 2.</pre>
</div>

<h4 id="Gerenciando_handlers">Gerenciando handlers</h4>

<p>A primeira coisa que você vai querer fazer em quase todos os requests é tratar os casos de success e error:</p>

<pre class="brush: js">request.onerror = function(event) {
  // Fazer algo com request.errorCode!
};
request.onsuccess = function(event) {
  // Fazer algo com request.result!
};</pre>

<p>Qual das duas funções, <code>onsuccess()</code> ou <code>onerror()</code>,  será chamada? Se tudo correr bem, o evento de sucesso (que é um evento DOM event com propriedade <code>type</code> setada <code>"success"</code>) é chamado com <code>request</code> como seu <code>target</code>. Uma vez chamado, a função <code>onsuccess()</code> no <code>request</code> é chamada com o evento de sucesso em seu contexto. Por outro lado, se acontecer algum problema, um evento de erro (que é um evento DOM com a propriedade <code>type</code> setada para <code>"error"</code>) é chamado no <code>request</code>. Então a função <code><code>onerror()</code></code>  com o evento erro em seu contexto.</p>

<p>A API IndexedDB é feita para minimizar a necessidade de manipular erros, então você não fará muitos eventos de erro (ao menos, se você usar a API!) No  caso de abrir um banco, portanto, existe algumas condições comuns para eventos de erro. O problema mais comum é o usuário não dar permissão para criar o banco. Um dos principais objetivos do IndexedDB é permitir muitos dados serem armazenados para uso offline. (Para aprender mais sobre o quanto cada navegador pode armazenar, veja <a href="/en/IndexedDB#Storage_limits" title="https://developer.mozilla.org/en/IndexedDB#Storage_limits">Storage limits</a>).  </p>

<p>Obviamente, navegadores não querem armazenar dados que poluem seu computador, então o navegador mostra uma mensagem ao usuário na primeira vez que um aplicativo tenta abrir o IndexedDB. O usuário pode escolher permitir ou negar acesso. O IndexedDB também é desabilitado no modo privado dos navegadores (ctrl+shift+N no Chrome e ctrl+shift+P no Firefox). Isso acontece porque o intuito do modo privado é não deixar rastros na navegação.</p>

<p>Agora, assumindo que o usuário aprovou seu request para criar o banco, e você recebeu success; Qual é o próximo passo? O request foi gerado com a chamada de <code>indexedDB.open()</code>, e <code>request.result</code> é uma instância de <code>IDBDatabase</code>, e você definitivamente vai querer armazenar isso para usar depois. Veja abaixo:</p>

<pre class="brush: js">var db;
var request = indexedDB.open("DBteste");
request.onerror = function(event) {
  alert("Você não habilitou minha web app para usar IndexedDB?!");
};
request.onsuccess = function(event) {
  db = request.result;
};
</pre>

<h4 id="Tratando_Erros">Tratando Erros</h4>

<p>Como mencionado acima, o evento de erro é chamado quando o request dá erro. Se você quer evitar manipuladores de erro a cada request, você pode adicionar um único manipulador de erro no objeto db, como abaixo:</p>

<pre class="brush: js">db.onerror = function(event) {
  // Função genérica para tratar os erros de todos os requests desse banco!
  alert("Database error: " + event.target.errorCode);
};
</pre>

<p>Um dos erros mais comuns ao abrir o banco é <code>VER_ERR</code>. Ele indica que a versão do banco existente é maior que a versão que você quer abrir.</p>

<h3 id="Criando_ou_atualizando_a_versão_do_banco">Criando ou atualizando a versão do banco</h3>

<p><a name="Updating_the_version_of_the_database"></a>Quando você cria um novo banco ou aumenta sua versão, o evento <code style="font-size: 14px; color: rgb(51, 51, 51);">onupgradeneeded</code> será chamado. No manipulador deste evento, você deve criar o objectStore necessário para a versão do banco:</p>

<pre class="brush:js;">// Este evento é implementado somente em navegadores mais recentes
request.onupgradeneeded = function(event) {
  var db = event.target.result;

  // cria um objectStore para esse banco
  var objectStore = db.createObjectStore("nome", { keyPath: "minhaChave" });
};</pre>

<p>Neste caso, o banco já terá objectStores de suas versões anteriores, então você não terá que criar esses objectStores de novo. Você somente precisará criar um novo objectStore qualquer, ou deletar objectStores da versão anterior que não serão utilizados. Se você precisa mudar um objectStore existente (mudar o <code>keyPath, por exemplo</code>),  então você precisa deletar o objectStore antigo e criá-lo novamente com as novas opções. (Note que isso irá deletar a informação no objectStore! Se você precisa salvar a informação, você deve ler isso e salvá-lo em algum lugar antes de atualizar o banco.)</p>

<p>Blink/Webkit suporta a versão atual da especificação, nas versões do Chrome 23+ e Opera 17+; IE10+ também suporta. Outros motores e versões antigas não implementam a versão atual da especificação e não suportam a assinatura <code>indexedDB.open(name, version).onupgradeneeded</code> ainda.  Para mais informação sobre como atualizar a versão do banco em Webkit/Blink, veja <a href="/en/IndexedDB/IDBDatabase#setVersion()_.0A.0ADeprecated" title="https://developer.mozilla.org/en/IndexedDB/IDBDatabase#setVersion()_.0A.0ADeprecated">IDBDatabase reference article</a>.</p>

<h3 id="Estruturando_o_banco">Estruturando o banco</h3>

<p>Agora a estrutura do banco. IndexedDB usa "armazens de objetos" em vez de tabelas, e um único banco de dados pode conter qualquer número de "armazem de objetos". Sempre que um valor é armazenado num objectStore, ele é associado a uma chave. Existe várias maneiras diferentes de uma chave ser mostrada, dependendo do que o objectStore usa, um <a href="/en/IndexedDB#gloss_key_path" title="https://developer.mozilla.org/en/IndexedDB#gloss_key_path">key path</a> ou <a href="/en/IndexedDB#gloss_key_generator" title="en/IndexedDB#gloss key generator">key generator</a>.</p>

<p>A tabela abaixo mostra as direfentes chaves suportadas:</p>

<table class="standard-table">
 <thead>
  <tr>
   <th scope="col">Key Path (<code>keyPath</code>)</th>
   <th scope="col">Key Generator (<code>autoIncrement</code>)</th>
   <th scope="col">Description</th>
  </tr>
 </thead>
 <tbody>
  <tr>
   <td>Não</td>
   <td>Não</td>
   <td>Este objectStore pode ter qualquer tipo de valor primitivo como número ou string. Você deve suprir uma chave separada sempre que adicionar um novo valor.</td>
  </tr>
  <tr>
   <td>Sim</td>
   <td>Não</td>
   <td>Este objectStore pode simplesmente armazenar objetos JavaScript. O objeto deve ter uma propriedade com o mesmo nome do key path.</td>
  </tr>
  <tr>
   <td>Não</td>
   <td>Sim</td>
   <td>Este objectStore pode possuir qualquer tipo de valor. A chave é gerada para você, automaticamente, ou você pode suprir uma chave separada, caso utilize uma chave específica.</td>
  </tr>
  <tr>
   <td>Sim</td>
   <td>Sim</td>
   <td>Este objectStore suporta somente objetos JavaScript. Normalmente uma chave é gerada e o valor dela é armazenado no objeto em uma propriedade com o mesmo nome da key path. Portanto, se a propriedade já existe, o valor dela será usado como chave, em vez do valor gerado.</td>
  </tr>
 </tbody>
</table>

<p>Você também pode criar índices em qualquer objectStore. Um indice deixa você olhar os valores armazenados no objectStore usando o valor de uma propriedade do objectStore, em vez de sua chave.</p>

<p>Adicionalmente, indices tem a habilidade de forçar restrições simples nos dados armazenados. Setando uma flag única quando criar o índice, reforça que dois objetos são armazenados contendo o mesmo valor para o key path do índice. Então, por exemplo, se você tem um objeto armazenado que tem um conjunto de pessoas, e você quer ter certeza que ninguém tera o mesmo e-mail, você pode usar um índice com flag única para forçar isso.</p>

<p>Isso soa meio confuso, mas este exemplo pode iluminar o conceito. Primeiro, vamos definir alguns dados a serem usados no exemplo:</p>

<pre class="brush: js">// Isso é o que os dados de nossos clientes será.
const DadosClientes = [
  { ssn: "444-44-4444", nome: "Bill", idade: 35, email: "bill@company.com" },
  { ssn: "555-55-5555", nome: "Donna", idade: 32, email: "donna@home.org" }
];
</pre>

<p>Agora  vamos ver  ccomo criar um IndexedDB para armazenar nossos dados:</p>

<pre class="brush: js">const dbName = "clientes";

var request = indexedDB.open(dbName, 2);

request.onerror = function(event) {
  // Tratar erros.
};
request.onupgradeneeded = function(event) {
  var db = event.target.result;

  // Cria um objectStore para conter a informação sobre nossos clientes. Nós vamos
  // usar "ssn" como key path porque sabemos que é único;
  var objectStore = db.createObjectStore("clientes", { keyPath: "ssn" });

  // Cria um índice para buscar clientes pelo nome. Podemos ter nomes
  // duplicados, então não podemos usar como índice único.
  objectStore.createIndex("nome", "nome", { unique: false });

  // Cria um índice para buscar clientes por email. Queremos ter certeza
  // que não teremos 2 clientes com o mesmo e-mail;
  objectStore.createIndex("email", "email", { unique: true });

  // Usando transação oncomplete para afirmar que a criação do objectStore
  // é terminada antes de adicionar algum dado nele.
  objectStore.transaction.oncomplete = function(event) {
    // Armazenando valores no novo objectStore.
    var clientesObjectStore = db.transaction("clientes", "readwrite").objectStore("clientes");
    for (var i in DadosClientes) {
      clientesObjectStore.add(DadosClientes[i]);
    }
  }
};
</pre>

<p>Como falamos antes, <code>onupgradeneeded</code> é o único lugar onde você pode alterar a estrutura do banco. Nele você pode criar e deletar objectStores e construir ou remover índices.</p>

<div>Armazens de objetos são criados com uma única chamada de <code>createObjectStore()</code>. O método pega o nome do armazem e um objeto parâmetro. Mesmo que o objeto parâmetro seja opcional, ele é muito importante porque ele deixa você definir propriedades importantes e ajustar tipos de dados que você quer criar. No nosso caso, nós obtemos um objectStore chamado "clientes" e definimos um <code>keyPath</code>, que é a propriedade que faz um objeto individual ser único no banco. Essa propriedade, nesse exemplo, é "ssn", que simboliza o cpf (social security number), que é único. O "ssn" deve ser apresentado em cada objeto armazenado no <code>objectStore</code>. </div>

<p>Nós também criamos um índice chamado "nome" ligado à propriedade <code>nome</code>. Assim como o <code>createObjectStore()</code>, o <code>createIndex()</code> tem um parâmetro opcional <code>options</code> que cuida do tipo de índice que você quer criar. Adicionando objetos que não tem a propriedade <code>nome</code> terá sucesso, porém esses objetos não aparecerão no índice "nome".</p>

<p>Nós podemos obter os objetos de clientes armazenados usando os <code>ssn</code> da objectStore diretamente, ou usando os nomes usados no índice. Para aprender como isso é feito, veja a seção <a href="#Using_an_index" title="Using IndexedDB#Using an index">usando um índice</a>.</p>

<h3 id="Usando_um_key_generator">Usando um key generator</h3>

<p>Setando uma flag <code>autoIncrement </code>ao criar o objectStore habilitará o key generator. Por padrão ele não é setado.</p>

<p>Com o key generator, a chave será gerada automaticamente quando você adicionar algum valor no objectStore. O atual número do key generator é sempre setado 1 quando a primeira key generator do objectStore é criada. Basicamente a próxima chave recebe o incremento de 1. O número atual da key generator nunca decresce,  a não ser se alguma operação do banco for revertida, como numa transação abortada, por exemplo. No entanto, deletar um registro ou limpar todos os registros nunca afeta o key generator dos objectStores.</p>

<p>Nós podemos criar outro objectStore com o key generator como abaixo:</p>

<pre class="brush: js">// Abrindo o indexedDB.
var request = indexedDB.open(dbName, 3);

request.onupgradeneeded = function (event) {

    var db = event.target.result;

    // Criando outro objeto chamado "names" com o autoIncrement setado.
    var objStore = db.createObjectStore("names", { autoIncrement : true });

    // Porque "names" tem o the key generator, a chave para o nome é gerada automaticamente.
    // Os registros adicionados serão assim:
    // key : 1 =&gt; value : "Bill"
    // key : 2 =&gt; value : "Donna"
    for (var i in DadosClientes) {
        objStore.add(DadosClientes[i].nome);
    }
}</pre>

<p>Para mais detalhes veja <a href="http://www.w3.org/TR/IndexedDB/#key-generator-concept">"W3C Key Generators"</a>.</p>

<h2 id="Adicionando_obtendo_e_removendo_dados">Adicionando, obtendo e removendo dados</h2>

<p>Antes de fazer qualquer coisa em um novo banco, você precisa iniciar uma transação. Transações estão no objeto database, e você tem que especificar qual objectStore você quer na transaction. Uma vez que você está dentro da transação, você pode acessar os objectStores  com seus dados e fazer os requests. Depois, você precisa decidir se você vai fazer mudanças no banco ou se você simplesmente quer ler esses dados. Transações tem três modos disponíveis: <code>readonly</code>, <code>readwrite</code>, and <code>versionchange</code>.</p>

<p>Para mudar o "schema" ou estrutura do banco — o que envolve criar ou deletar objectStores ou índices — a transação deve ser em modo <code>versionchange</code>. Esta transação é aberta chamando o método {{domxref("IDBFactory.open")}} especificando a  <code>version.</code> (Em navegadores com WebKit que não tem a ultima especificação implementada, o método {{domxref("IDBFactory.open")}} tem apenas um parâmetro, o <code>nome</code> do banco; então você deve chamar {{domxref("IDBVersionChangeRequest.setVersion")}} para estabelecer uma transação <code>versionchange</code>.)</p>

<p>Para ler os registros de um objectStore existente, a transação pode ser tanto<code> readonly</code> quanto <code>readwrite</code>. Para fazer mudanças em um objectStore existente, a transação deve ser em modo <code>readwrite</code>. Você abre essas transações usando {{domxref("IDBDatabase.transaction")}}. O método aceita dois parâmetros: o <code>storeNames</code> (o escopo, definido como um array de objectStores que você quer acessar) e o <code>modo</code> (<code>readonly</code> or <code>readwrite</code>) da transação. O método retorna o objeto detransação contendo o método {{domxref("IDBIndex.objectStore")}}, que você pode usar para acessar seu objectStore. Por padrão, quando nenhum modo é especificado, a transação é aberta no modo <code>readonly</code>.</p>

<p>Você pode deixar o acesso aos dados mais rápido usando o escopo correto e o modo correto de transação. Aqui vai algumas dicas:</p>

<ul>
 <li>Quando definir o escopo, especifique apenas os objectStores que você precisa. Desse jeito você pode rodar multiplas transações sem que uma sobreponha a outra.</li>
 <li>Somente especifique uma transação  <code>readwrite</code> quando necessário. Você pode rodar várias transações <code>readonly</code> com escopos sobreposts, mas você pode ter somente uma transação <code>readwrite</code> por objectStore. Para aprender mais sobre <dfn><a href="/en-US/docs/IndexedDB/Basic_Concepts_Behind_IndexedDB#Database">transactions</a></dfn> veja <a href="/en-US/docs/IndexedDB/Basic_Concepts_Behind_IndexedDB">Basic Concepts</a>.</li>
</ul>

<h3 id="Adicionando_dados_no_banco">Adicionando dados no banco</h3>

<p>Se você acabou de criar o banco, então você provavelmente quer escrever algo nele. Veja abaixo:</p>

<pre class="brush:js;">var transaction = db.transaction(["clientes"], "readwrite");
// Nota: Implementações mais antigas usam uma versão IDBTransaction.READ_WRITE antiga em vez de "readwrite".
// Então, para suportar versões antigas, escreva:
// var transaction = db.transaction(["clientes"], IDBTransaction.READ_WRITE);</pre>

<p>A função <code>transaction()</code> tem dois argumentos (opcionais) e retorna um objeto de transação. O primeiro argumento é uma lista de objectStores que serão trabalhados na transação. Você pode passar um array vazio se você quer uma transação com todos os objectStores, mas não faça isso pois a especificação diz que um array vazio pode gerar um erro InvalidAccessError. Se você não especificar nada no segundo parâmetro, você tem uma transação read-only. Se você quer escrever no banco, use <code>"readwrite"</code>.</p>

<p>Agora que você tem uma transação, você precisa entender seu tempo de uso. Transações são amarradas a um evento. Se você faz uma transação fora de um evento, ela ficará inativa. A única maneira de manter uma transação ativa é fazer um request nela. Quando o request acabar você terá a oportunidade de extender a transação durante o callback. Se você tentar extender uma transação dentro de um evento, então ela tornará inativa. Se existir requests pendentes, a transação continua ativa. O tempo de vida de uma transação é realmente simples mas deve ser usada em um curto espaço de tempo. Outros exemplos poderão ajudá-lo. Se você começar a ver<code> TRANSACTION_INACTIVE_ERR</code> error então você está fazendo algo errado.</p>

<p>Transações podem receber eventos DOM de três tipos diferentes: <code>error</code>, <code>abort</code>, e <code>complete</code>. Nós falamos sobre o <code>error</code>, ou seja, a transação recebe um error sempre que o request gerar erro. Um ponto mais sutil é que o comportamento padrão de um erro é abortar a transação na qual ele estava. A menos que você manipule o erro chamando <code>preventDefault()</code> e fazendo algo depois, a transaçaõ inteira será desfeita. Este design força você a pensar sobre manipulação de erros, mas você pode sempre adicionar um manipulador de todos os erros se a manipulação separada estiver complicada. Se você não tratar o erro ou chamar <code>abort()</code> na transação, então a transação é desfeita (roll back) e o evento <code>abort</code> é chamado. Por outro lado, depois de todo request completado, você tem um evento <code>complete</code>. Se você fazer várias operações no banco, então seguir as operações de transações pode ser um caminho a seguir.</p>

<p>Agora que você tem uma transação, você precisará obter um objectStore dela. Transações somente deixam você obter um objectStore citado na transação. Então você pode adicionar os dados que precisa.</p>

<pre class="brush: js">// Faz algo após a inserção dos dados.
transaction.oncomplete = function(event) {
  alert("Pronto!");
};

transaction.onerror = function(event) {
  // Não esquecer de tratar os erros!
};

var objectStore = transaction.objectStore("clientes");
for (var i in DadosClientes) {
  var request = objectStore.add(DadosClientes[i]);
  request.onsuccess = function(event) {
    // event.target.result == DadosClientes[i].ssn;
  };
}</pre>

<p>O <code>result</code> de um request gerado de uma chamada de <code>add()</code> é a chave do valor  que foi adicionado. Então neste caso, ele deve ser igual ao valor do <code>ssn</code> do objeto que foi adicionado, desde que o objeto use o <code>ssn</code> como key path. Note que a função <code>add()</code> não deixa nenhum objeto ser adicionado com a mesma chave. Se você está tentando modificar um registro existente, você deve usar o <code>put()</code>, como explica a seção  <a href="#updating_an_entry_in_the_database">Updating an entry in the database</a>.</p>

<h3 id="Removendo_dados_do_banco">Removendo dados do banco</h3>

<p>Para remoção o código é parecido:</p>

<pre class="brush: js">var request = db.transaction(["clientes"], "readwrite")
                .objectStore("clientes")
                .delete("444-44-4444");
request.onsuccess = function(event) {
  // Pronto!
};</pre>

<h3 id="Obtendo_dados_do_banco">Obtendo dados do banco</h3>

<p>Agora que o banco tem algumas informações nele, você pode obtê-las de diferentes maneiras. Primeiro, um <code>get()</code> simples. Você precisa informar a chave do valor a ser obtido:</p>

<pre class="brush: js">var transaction = db.transaction(["clientes"]);
var objectStore = transaction.objectStore("clientes");
var request = objectStore.get("444-44-4444");
request.onerror = function(event) {
  // Tratar erro!
};
request.onsuccess = function(event) {
  // Fazer algo com request.result!
  alert("O nome do SSN 444-44-4444 é " + request.result.name);
};</pre>

<p>Veja agora de maneira resumida:</p>

<pre class="brush: js">db.transaction("clientes").objectStore("clientes").get("444-44-4444").onsuccess = function(event) {
  alert("O nome do SSN 444-44-4444 é " + request.result.name);
};</pre>

<p>Viu como funciona? Desde que exista um objectStore, você pode evitar passar uma lista de objectStores que precisa na transação e passar apenas o nome como string. Você também pode ler do banco, apenas, então não precisará de uma transação <code>"readwrite".</code> Chamando <code>transaction()</code> com nenhum modo especificado, você terá uma transação <code>"readonly"</code>. Outra consideração é que você não necessita salvar o request em uma variável. Desde que o evento DOM tenha o target que você precisará para obter a propriedade <code>result</code>.</p>

<div class="note">
<p><strong>Note</strong>: Você pode deixar o acesso aos dados mais rápido limitando o escopo e o modo de transação. Veja algumas dicas:</p>

<ul>
 <li>
  <p>Quando definir o <a href="#scope">escopo</a>, especifique somente os objectStores que vai precisar. Assim você pode rodar multiplas transações sem sopreposições.</p>
 </li>
 <li>
  <p>Utilize uma transação <code>readwrite</code>  somente quando necessário. Você pode rodar várias transações <code>readonly</code> simultâneas, mas apenas uma transação <code>readwrite</code> por objectStore. Para aprender mais sobre isso veja o artigo <a href="/en-US/docs/IndexedDB/Basic_Concepts_Behind_IndexedDB#gloss_transaction"><dfn>transactions</dfn> in the Basic Concepts</a>.</p>
 </li>
</ul>
</div>

<h3 id="Atualizando_um_registro_no_banco">Atualizando um registro no banco</h3>

<p>Agora que obtemos algum dado, atualizá-ls é inserí-los novamente no IndexedDB é bem simples. Vamos atualizar o exemplo anterior:</p>

<pre class="brush: js">var objectStore = db.transaction(["clientes"], "readwrite").objectStore("clientes");
var request = objectStore.get("444-44-4444");
request.onerror = function(event) {
  // Tratar erro
};
request.onsuccess = function(event) {
  // Obter os valores antigos
  var data = request.result;

  // atualizar algum dado
  data.age = 42;

  // Atulizar esse dado no banco
  var requestUpdate = objectStore.put(data);
   requestUpdate.onerror = function(event) {
     // Tratar erro
   };
   requestUpdate.onsuccess = function(event) {
     // Sucesso na atualização \o/
   };
};</pre>

<p>Criamos uma <code>objectStore</code> e obtemos um cliente dele, identificado pelo ssn (<code>444-44-4444</code>). Nós atualizamos o objeto, passando-o como parâmetro de um método put de outro request (<code>requestUpdate</code>) sobrescrevendo o valor antigo.</p>

<div class="note">
<p><strong>Note</strong> que neste caso nós temos que especificar a transação <code>readwrite</code> porque nós queremos escrever no banco, não somente ler os dados dele.</p>
</div>

<h3 id="Usando_um_cursor">Usando um cursor</h3>

<p>Ao usar o método <code>get()</code> você precisa saber a chave do objeto que deseja obter. Se você quer passear entre todos os valores do seu objectStore, então você pode usar um cursor. Veja:</p>

<pre class="brush: js">var objectStore = db.transaction("cliente").objectStore("cliente");

objectStore.openCursor().onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    alert("O nome do SSN " + cursor.key + " é " + cursor.value.name);
    cursor.continue();
  }
  else {
    alert("Não existe mais registros!");
  }
};</pre>

<p><code>A função openCursor()</code> tem vários argumentos. Primeiro, você pode limitar o número de itens obtidos usando uma chave que veremos logo abaixo. Segundo, você pode especificar a direção que deseja iterar. No exemplo acima, nós estamos iterando em todos os objetos em ordem ascendente. O callback de sucesso para cursor é um pouco especial. O objeto cursor já é o <code>result</code> do request (acima nós usamos <code>event.target.result</code>). Então a chave atual e o valor pode ser encontrado na propriedade <code>key</code> e <code>value</code> do objeto cursor. Se você quer manter adiante, então você usa o método <code>continue()</code>. Quando você chegar ao fim dos dados (ou se não existem registros encontrados no <code>openCursor()</code>) você ainda tem um callback de sucesso, mas a propriedade <code>result</code> fica <code>undefined</code>.</p>

<p>Um padrão comum para cursores é obter todos os objetos em um objectStore e adicioná-los a um array como este:</p>

<pre class="brush: js">var clientes = [];

objectStore.openCursor().onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    clientes.push(cursor.value);
    cursor.continue();
  }
  else {
    alert("Todos os clientes: " + clientes);
  }
};</pre>

<div class="note">
<p>Note: Mozilla também implementou o método <code>getAll()</code> para ser usado nesse caso (e <code>getAllKeys()</code>, que está atualmente dentro da preferência do <code>dom.indexedDB.experimental</code> em about:config). Estes métodos não são parte do padrão IndexedDB, então eles podem desaparecer no futuro. Nós adicionamos porque achamos útil. O código abaixo faz o mesmo que o código acima:</p>

<pre class="brush: js">objectStore.getAll().onsuccess = function(event) {
  alert("Todos os clientes: " + event.target.result);
};</pre>

<p>Existe um custo de performance associado com a propriedade <code>value</code> do cursor, porque o objeto é criado de forma lenta. Quando você usa <code>getAll()</code> por exemplo, Gecko deve criar todos os objetos de uma vez. Se você está somente interessado em cada chave, é muito melhor usar o cursor do que usar o <code>getAll()</code>. Se você está tentando obter um array de todos os objetos, então é melhor usar o <code>getAll()</code>.</p>
</div>

<h3 id="Usando_um_índice">Usando um índice</h3>

<p>Armazenar dados de um cliente usando o SSN como chave é lógico pois o SSN identifica o cliente de forma única. Se você precisa obter um cliente pelo seu nome, portanto, você precisará iterar todos os registros no banco e comparar os nomes até achar o que você procura. Buscar dessa maneira é algo lento, então criamos um índice.</p>

<pre class="brush: js">var index = objectStore.index("nome");
index.get("John").onsuccess = function(event) {
  alert("O SSN de John é " + event.target.result.ssn);
};</pre>

<p>O cursor "nome" não é único, então pode existir mais de um registro com o <code>nome</code> igual a <code>"John"</code>. Neste caso você sempre obtem o registro com a chave de menor valor.</p>

<p>Se você precisa acessar todos os registros retornados, você pode usar um cursor. Você pode abrir dois tipos de cursores. Um cursor normal mapeia o índice ao objeto na objectStore. Uma cursor-chave mapeia o a propriedade índice à chave usada para armazenar o objeto. As diferenças são ilustradas abaixo:</p>

<pre class="brush: js">// Usando um cursor normal para obter todos os objetos
index.openCursor().onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    // cursor.key é um nome, como "Bill", e cursor.value é o objeto inteiro.
    alert("Nome: " + cursor.key + ", SSN: " + cursor.value.ssn + ", email: " + cursor.value.email);
    cursor.continue();
  }
};

// Usando um cursor-chave para obter todos os objetos
index.openKeyCursor().onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    // cursor.key é o nome, como "Bill", e cursor.value é o SSN (chave).
    // Não tem como obter o resto do objeto
    alert("Nome: " + cursor.key + ", SSN: " + cursor.value);
    cursor.continue();
  }
};</pre>

<h3 id="Especificando_o_número_e_a_direção_dos_cursores">Especificando o número e a direção dos cursores</h3>

<p>Se você gostaria de limitar o número de valores retornados pelo cursor, você pode usar um objeto <code>IDBKeyRange</code> e passar isso como o primeiro argumento ao <code>openCursor()</code> ou <code>openKeyCursor()</code>. Você pode fazer um key range que permite um único valor, ou valores acima ou abaixo do especificado. O limite pode ser fechado (o key range inclui os valores dados) ou aberto (o key range não inclue os valores dados). Veja como funciona:</p>

<pre class="brush: js">// Somente se for igual "Donna"
var singleKeyRange = IDBKeyRange.only("Donna");

// Combinações menores que "Bill", incluindo "Bill"
var lowerBoundKeyRange = IDBKeyRange.lowerBound("Bill");

// Combinações menores que "Bill", sem incluir "Bill"
var lowerBoundOpenKeyRange = IDBKeyRange.lowerBound("Bill", true);

// Combinações maiores que Donna, não incluindo "Donna"
var upperBoundOpenKeyRange = IDBKeyRange.upperBound("Donna", true);

// Combinações entre "Bill" e "Donna", sem incluir "Donna"
var boundKeyRange = IDBKeyRange.bound("Bill", "Donna", false, true);

// Para usar qualquer um desses key ranges, basta passar como primeiro parâmetro de openCursor()/openKeyCursor()
index.openCursor(boundKeyRange).onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    // Faz algo com o que encontrar
    cursor.continue();
  }
};</pre>

<p>As vezes você pode querer iterar em ordem decrescente, em vez de crescente, alterando o segundo parâmetro de <code>openCursor()</code>:</p>

<pre class="brush: js">objectStore.openCursor(boundKeyRange, "prev").onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    // Prev indica ordem decrescente
    cursor.continue();
  }
};</pre>

<p>Se você apenas quer especificar a ordem sem key range, é só passar null no primeiro parâmetro:</p>

<pre class="brush: js">objectStore.openCursor(null, "prev").onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    // Faça algo com os resultados.
    cursor.continue();
  }
};</pre>

<p>Uma vez que o índice "nome" não é único, pode existir várias entradas onde o <code>nome</code> é o mesmo. Isso não acontece com objectStores porque a chave deve ser sempre única. Se você deseja filtrar valores duplicados numa iteração do cursor, você pode passar <code>nextunique</code> (ou <code>prevunique</code> se quiser decrescer) como parâmetro de direção. Quando <code>nextunique</code> ou <code>prevunique</code> é usado, o registro com menor chave é retornado.</p>

<pre class="brush: js">index.openKeyCursor(null, "nextunique").onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    // Faça algo com os registros.
    cursor.continue();
  }
};</pre>

<p>Veja "<a href="https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor?redirectlocale=en-US&amp;redirectslug=IndexedDB%2FIDBCursor#Constants">IDBCursor Constants</a>" para parâmetros válidos.</p>

<h2 id="Mudança_de_versão_quando_a_web_app_está_aberta_em_outra_aba.">Mudança de versão quando a web app está aberta em outra aba.</h2>

<p>Quando sua web app muda a versão você precisa considerar o que vai acontecer se o usuário está na versão antiga em uma aba, e carrega a versão nova na outra. Quando você chamar o <code>open()</code> com a versão mais nova, um evento <code>onblocked</code>  é chamado até que a aba da versão antiga seja fechada ou recarregada. Veja abaixo:</p>

<pre class="brush: js">var openReq = mozIndexedDB.open("DBteste", 2);

openReq.onblocked = function(event) {
  // Se existe outra aba com a versão antiga
  alert("Existe uma versão antiga da web app aberta em outra aba, feche-a por favor!");
};

openReq.onupgradeneeded = function(event) {
  // Se estiver tudo fechado, então faça as devidas alterações
  db.createObjectStore(/* ... */);
  useDatabase(db);
}

openReq.onsuccess = function(event) {
  var db = event.target.result;
  useDatabase(db);
  return;
}

function useDatabase(db) {
  // Esteja certo de que adicionou um evento para notificar se a página muda a versão
  // Nós devemos fechar o banco. Isso permite à outra página ser atualizada
  // Se você não fizer isso a atualização não acontecerá até fechar as abas.
  db.onversionchange = function(event) {
    db.close();
    alert("Uma nova versão desta web app está pronta. Atualiza, por favor!");
  };

  // Fazer algo com os bancos
}
</pre>

<h2 id="Segurança">Segurança</h2>

<p>IndexedDB usa o princípio de mesma origem, o que significa que o banco só será acessado pelo site que o criou.</p>

<p>É importante notar que o IndexedDB não funciona para conteúdo carregado em um frame de outro site (seja {{ HTMLElement("frame") }} ou {{ HTMLElement("iframe") }}. Esta é uma política de segurança e privacidade análoga ao bloqueio de cookies de terceiros. Para mais detalhes, veja {{ bug(595307) }}.</p>

<h2 id="Alerta_sobre_fechar_o_navegador">Alerta sobre fechar o navegador</h2>

<p>Quando o navegador é fechado, qualquer transação pendente no IndexedDB será abortada (silenciosamente) — ele não vai completar, nem chamar o evento de erro.  Uma vez que o usuário pode sair do navegador, em qualquer momento, isto significa que você não pode confiar em qualquer transação específica para completar ou para saber que ela não foi concluída. Existem várias implicações nesse comportamento.</p>

<p>Primeiro, você deve ter o cuidado de sempre deixar seu banco de dados em um estado consistente, no final de cada transação. Por exemplo, suponha que você está usando IndexedDB para armazenar uma lista de itens que permitem ao usuário editar. Você salvar a lista após a edição, limpando o armazenamento de objetos e, em seguida, escrever a nova lista. Se você limpar o armazenamento de objetos em uma transação e escrever a nova lista em outra transação, há um perigo de que o navegador irá fechar após a limpeza de dados e antes da gravação, deixando-o com um banco de dados vazio. Para evitar isso, você deve combinar tanto a limpeza quanto a gravação em uma única transação.</p>

<p>Em segundo lugar, você nunca deve amarrar as operações de banco de dados ao evento unload. Se o evento unload é acionado pelo fechamento do navegador, todas as transações criadas no unload nunca serão concluídas. Uma abordagem intuitiva para manter algumas informações em sessões do navegador é lê-la a partir do banco de dados quando o navegador (ou uma determinada página) é aberta, atualizá-la assim que o usuário interagir com o navegador, e depois salvá-lo para o banco de dados quando o navegador (ou página) será fechada. No entanto, isso não vai funcionar. As transações de banco de dados será criado no unload, mas como elas são assíncronas, serão abortadas antes que eles possam executar.</p>

<p>De fato, não existe uma maneira de garantir que as transações no IndexedDBserão completadas, mesmo com o fechamento padrão do navegador. Ver {{ bug(870645) }}.</p>

<h2 id="Full_IndexedDB_example" name="Full_IndexedDB_example">Exemplo de IndexedDB</h2>

<h3 id="HTML">HTML</h3>

<pre class="brush: html">&lt;script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.8.3/jquery.min.js"&gt;&lt;/script&gt;

    &lt;h1&gt;IndexedDB Demo: storing blobs, e-publication example&lt;/h1&gt;
    &lt;div class="note"&gt;
      &lt;p&gt;
        Works and tested with:
      &lt;/p&gt;
      &lt;div id="compat"&gt;
      &lt;/div&gt;
    &lt;/div&gt;

    &lt;div id="msg"&gt;
    &lt;/div&gt;

    &lt;form id="register-form"&gt;
      &lt;table&gt;
        &lt;tbody&gt;
          &lt;tr&gt;
            &lt;td&gt;
              &lt;label for="pub-title" class="required"&gt;
                Title:
              &lt;/label&gt;
            &lt;/td&gt;
            &lt;td&gt;
              &lt;input type="text" id="pub-title" name="pub-title" /&gt;
            &lt;/td&gt;
          &lt;/tr&gt;
          &lt;tr&gt;
            &lt;td&gt;
              &lt;label for="pub-biblioid" class="required"&gt;
                Bibliographic ID:&lt;br/&gt;
                &lt;span class="note"&gt;(ISBN, ISSN, etc.)&lt;/span&gt;
              &lt;/label&gt;
            &lt;/td&gt;
            &lt;td&gt;
              &lt;input type="text" id="pub-biblioid" name="pub-biblioid"/&gt;
            &lt;/td&gt;
          &lt;/tr&gt;
          &lt;tr&gt;
            &lt;td&gt;
              &lt;label for="pub-year"&gt;
                Year:
              &lt;/label&gt;
            &lt;/td&gt;
            &lt;td&gt;
              &lt;input type="number" id="pub-year" name="pub-year" /&gt;
            &lt;/td&gt;
          &lt;/tr&gt;
        &lt;/tbody&gt;
        &lt;tbody&gt;
          &lt;tr&gt;
            &lt;td&gt;
              &lt;label for="pub-file"&gt;
                File image:
              &lt;/label&gt;
            &lt;/td&gt;
            &lt;td&gt;
              &lt;input type="file" id="pub-file"/&gt;
            &lt;/td&gt;
          &lt;/tr&gt;
          &lt;tr&gt;
            &lt;td&gt;
              &lt;label for="pub-file-url"&gt;
                Online-file image URL:&lt;br/&gt;
                &lt;span class="note"&gt;(same origin URL)&lt;/span&gt;
              &lt;/label&gt;
            &lt;/td&gt;
            &lt;td&gt;
              &lt;input type="text" id="pub-file-url" name="pub-file-url"/&gt;
            &lt;/td&gt;
          &lt;/tr&gt;
        &lt;/tbody&gt;
      &lt;/table&gt;

      &lt;div class="button-pane"&gt;
        &lt;input type="button" id="add-button" value="Add Publication" /&gt;
        &lt;input type="reset" id="register-form-reset"/&gt;
      &lt;/div&gt;
    &lt;/form&gt;

    &lt;form id="delete-form"&gt;
      &lt;table&gt;
        &lt;tbody&gt;
          &lt;tr&gt;
            &lt;td&gt;
              &lt;label for="pub-biblioid-to-delete"&gt;
                Bibliographic ID:&lt;br/&gt;
                &lt;span class="note"&gt;(ISBN, ISSN, etc.)&lt;/span&gt;
              &lt;/label&gt;
            &lt;/td&gt;
            &lt;td&gt;
              &lt;input type="text" id="pub-biblioid-to-delete"
                     name="pub-biblioid-to-delete" /&gt;
            &lt;/td&gt;
          &lt;/tr&gt;
          &lt;tr&gt;
            &lt;td&gt;
              &lt;label for="key-to-delete"&gt;
                Key:&lt;br/&gt;
                &lt;span class="note"&gt;(for example 1, 2, 3, etc.)&lt;/span&gt;
              &lt;/label&gt;
            &lt;/td&gt;
            &lt;td&gt;
              &lt;input type="text" id="key-to-delete"
                     name="key-to-delete" /&gt;
            &lt;/td&gt;
          &lt;/tr&gt;
        &lt;/tbody&gt;
      &lt;/table&gt;
      &lt;div class="button-pane"&gt;
        &lt;input type="button" id="delete-button" value="Delete Publication" /&gt;
        &lt;input type="button" id="clear-store-button"
               value="Clear the whole store" class="destructive" /&gt;
      &lt;/div&gt;
    &lt;/form&gt;

    &lt;form id="search-form"&gt;
      &lt;div class="button-pane"&gt;
        &lt;input type="button" id="search-list-button"
               value="List database content" /&gt;
      &lt;/div&gt;
    &lt;/form&gt;

    &lt;div&gt;
      &lt;div id="pub-msg"&gt;
      &lt;/div&gt;
      &lt;div id="pub-viewer"&gt;
      &lt;/div&gt;
      &lt;ul id="pub-list"&gt;
      &lt;/ul&gt;
    &lt;/div&gt;
</pre>

<h3 id="CSS_Content">CSS Content</h3>

<pre class="brush: css">body {
  font-size: 0.8em;
  font-family: Sans-Serif;
}

form {
  background-color: #cccccc;
  border-radius: 0.3em;
  display: inline-block;
  margin-bottom: 0.5em;
  padding: 1em;
}

table {
  border-collapse: collapse;
}

input {
  padding: 0.3em;
  border-color: #cccccc;
  border-radius: 0.3em;
}

.required:after {
  content: "*";
  color: red;
}

.button-pane {
  margin-top: 1em;
}

#pub-viewer {
  float: right;
  width: 48%;
  height: 20em;
  border: solid #d092ff 0.1em;
}
#pub-viewer iframe {
  width: 100%;
  height: 100%;
}

#pub-list {
  width: 46%;
  background-color: #eeeeee;
  border-radius: 0.3em;
}
#pub-list li {
  padding-top: 0.5em;
  padding-bottom: 0.5em;
  padding-right: 0.5em;
}

#msg {
  margin-bottom: 1em;
}

.action-success {
  padding: 0.5em;
  color: #00d21e;
  background-color: #eeeeee;
  border-radius: 0.2em;
}

.action-failure {
  padding: 0.5em;
  color: #ff1408;
  background-color: #eeeeee;
  border-radius: 0.2em;
}

.note {
  font-size: smaller;
}

.destructive {
  background-color: orange;
}
.destructive:hover {
  background-color: #ff8000;
}
.destructive:active {
  background-color: red;
}
</pre>

<p> </p>

<h3 id="JavaScript_Content">JavaScript Content</h3>

<pre class="brush: js">(function () {
  var COMPAT_ENVS = [
    ['Firefox', "&gt;= 16.0"],
    ['Google Chrome',
     "&gt;= 24.0 (you may need to get Google Chrome Canary), NO Blob storage support"]
  ];
  var compat = $('#compat');
  compat.empty();
  compat.append('&lt;ul id="compat-list"&gt;&lt;/ul&gt;');
  COMPAT_ENVS.forEach(function(val, idx, array) {
    $('#compat-list').append('&lt;li&gt;' + val[0] + ': ' + val[1] + '&lt;/li&gt;');
  });

  const DB_NAME = 'mdn-demo-indexeddb-epublications';
  const DB_VERSION = 1; // Use a long long for this value (don't use a float)
  const DB_STORE_NAME = 'publications';

  var db;

  // Used to keep track of which view is displayed to avoid uselessly reloading it
  var current_view_pub_key;

  function openDb() {
    console.log("openDb ...");
    var req = indexedDB.open(DB_NAME, DB_VERSION);
    req.onsuccess = function (evt) {
      // Better use "this" than "req" to get the result to avoid problems with
      // garbage collection.
      // db = req.result;
      db = this.result;
      console.log("openDb DONE");
    };
    req.onerror = function (evt) {
      console.error("openDb:", evt.target.errorCode);
    };

    req.onupgradeneeded = function (evt) {
      console.log("openDb.onupgradeneeded");
      var store = evt.currentTarget.result.createObjectStore(
        DB_STORE_NAME, { keyPath: 'id', autoIncrement: true });

      store.createIndex('biblioid', 'biblioid', { unique: true });
      store.createIndex('title', 'title', { unique: false });
      store.createIndex('year', 'year', { unique: false });
    };
  }

  /**
   * @param {string} store_name
   * @param {string} mode either "readonly" or "readwrite"
   */
  function getObjectStore(store_name, mode) {
    var tx = db.transaction(store_name, mode);
    return tx.objectStore(store_name);
  }

  function clearObjectStore(store_name) {
    var store = getObjectStore(DB_STORE_NAME, 'readwrite');
    var req = store.clear();
    req.onsuccess = function(evt) {
      displayActionSuccess("Store cleared");
      displayPubList(store);
    };
    req.onerror = function (evt) {
      console.error("clearObjectStore:", evt.target.errorCode);
      displayActionFailure(this.error);
    };
  }

  function getBlob(key, store, success_callback) {
    var req = store.get(key);
    req.onsuccess = function(evt) {
      var value = evt.target.result;
      if (value)
        success_callback(value.blob);
    };
  }

  /**
   * @param {IDBObjectStore=} store
   */
  function displayPubList(store) {
    console.log("displayPubList");

    if (typeof store == 'undefined')
      store = getObjectStore(DB_STORE_NAME, 'readonly');

    var pub_msg = $('#pub-msg');
    pub_msg.empty();
    var pub_list = $('#pub-list');
    pub_list.empty();
    // Resetting the iframe so that it doesn't display previous content
    newViewerFrame();

    var req;
    req = store.count();
    // Requests are executed in the order in which they were made against the
    // transaction, and their results are returned in the same order.
    // Thus the count text below will be displayed before the actual pub list
    // (not that it is algorithmically important in this case).
    req.onsuccess = function(evt) {
      pub_msg.append('&lt;p&gt;There are &lt;strong&gt;' + evt.target.result +
                     '&lt;/strong&gt; record(s) in the object store.&lt;/p&gt;');
    };
    req.onerror = function(evt) {
      console.error("add error", this.error);
      displayActionFailure(this.error);
    };

    var i = 0;
    req = store.openCursor();
    req.onsuccess = function(evt) {
      var cursor = evt.target.result;

      // If the cursor is pointing at something, ask for the data
      if (cursor) {
        console.log("displayPubList cursor:", cursor);
        req = store.get(cursor.key);
        req.onsuccess = function (evt) {
          var value = evt.target.result;
          var list_item = $('&lt;li&gt;' +
                            '[' + cursor.key + '] ' +
                            '(biblioid: ' + value.biblioid + ') ' +
                            value.title +
                            '&lt;/li&gt;');
          if (value.year != null)
            list_item.append(' - ' + value.year);

          if (value.hasOwnProperty('blob') &amp;&amp;
              typeof value.blob != 'undefined') {
            var link = $('&lt;a href="' + cursor.key + '"&gt;File&lt;/a&gt;');
            link.on('click', function() { return false; });
            link.on('mouseenter', function(evt) {
                      setInViewer(evt.target.getAttribute('href')); });
            list_item.append(' / ');
            list_item.append(link);
          } else {
            list_item.append(" / No attached file");
          }
          pub_list.append(list_item);
        };

        // Move on to the next object in store
        cursor.continue();

        // This counter serves only to create distinct ids
        i++;
      } else {
        console.log("No more entries");
      }
    };
  }

  function newViewerFrame() {
    var viewer = $('#pub-viewer');
    viewer.empty();
    var iframe = $('&lt;iframe /&gt;');
    viewer.append(iframe);
    return iframe;
  }

  function setInViewer(key) {
    console.log("setInViewer:", arguments);
    key = Number(key);
    if (key == current_view_pub_key)
      return;

    current_view_pub_key = key;

    var store = getObjectStore(DB_STORE_NAME, 'readonly');
    getBlob(key, store, function(blob) {
      console.log("setInViewer blob:", blob);
      var iframe = newViewerFrame();

      // It is not possible to set a direct link to the
      // blob to provide a mean to directly download it.
      if (blob.type == 'text/html') {
        var reader = new FileReader();
        reader.onload = (function(evt) {
          var html = evt.target.result;
          iframe.load(function() {
            $(this).contents().find('html').html(html);
          });
        });
        reader.readAsText(blob);
      } else if (blob.type.indexOf('image/') == 0) {
        iframe.load(function() {
          var img_id = 'image-' + key;
          var img = $('&lt;img id="' + img_id + '"/&gt;');
          $(this).contents().find('body').html(img);
          var obj_url = window.URL.createObjectURL(blob);
          $(this).contents().find('#' + img_id).attr('src', obj_url);
          window.URL.revokeObjectURL(obj_url);
        });
      } else if (blob.type == 'application/pdf') {
        $('*').css('cursor', 'wait');
        var obj_url = window.URL.createObjectURL(blob);
        iframe.load(function() {
          $('*').css('cursor', 'auto');
        });
        iframe.attr('src', obj_url);
        window.URL.revokeObjectURL(obj_url);
      } else {
        iframe.load(function() {
          $(this).contents().find('body').html("No view available");
        });
      }

    });
  }

  /**
   * @param {string} biblioid
   * @param {string} title
   * @param {number} year
   * @param {string} url the URL of the image to download and store in the local
   *   IndexedDB database. The resource behind this URL is subjected to the
   *   "Same origin policy", thus for this method to work, the URL must come from
   *   the same origin as the web site/app this code is deployed on.
   */
  function addPublicationFromUrl(biblioid, title, year, url) {
    console.log("addPublicationFromUrl:", arguments);

    var xhr = new XMLHttpRequest();
    xhr.open('GET', url, true);
    // Setting the wanted responseType to "blob"
    // http://www.w3.org/TR/XMLHttpRequest2/#the-response-attribute
    xhr.responseType = 'blob';
    xhr.onload = function (evt) {
                           if (xhr.status == 200) {
                             console.log("Blob retrieved");
                             var blob = xhr.response;
                             console.log("Blob:", blob);
                             addPublication(biblioid, title, year, blob);
                           } else {
                             console.error("addPublicationFromUrl error:",
                                           xhr.responseText, xhr.status);
                           }
                         };
    xhr.send();

    // We can't use jQuery here because as of jQuery 1.8.3 the new "blob"
    // responseType is not handled.
    // http://bugs.jquery.com/ticket/11461
    // http://bugs.jquery.com/ticket/7248
    // $.ajax({
    //   url: url,
    //   type: 'GET',
    //   xhrFields: { responseType: 'blob' },
    //   success: function(data, textStatus, jqXHR) {
    //     console.log("Blob retrieved");
    //     console.log("Blob:", data);
    //     // addPublication(biblioid, title, year, data);
    //   },
    //   error: function(jqXHR, textStatus, errorThrown) {
    //     console.error(errorThrown);
    //     displayActionFailure("Error during blob retrieval");
    //   }
    // });
  }

  /**
   * @param {string} biblioid
   * @param {string} title
   * @param {number} year
   * @param {Blob=} blob
   */
  function addPublication(biblioid, title, year, blob) {
    console.log("addPublication arguments:", arguments);
    var obj = { biblioid: biblioid, title: title, year: year };
    if (typeof blob != 'undefined')
      obj.blob = blob;

    var store = getObjectStore(DB_STORE_NAME, 'readwrite');
    var req;
    try {
      req = store.add(obj);
    } catch (e) {
      if (e.name == 'DataCloneError')
        displayActionFailure("This engine doesn't know how to clone a Blob, " +
                             "use Firefox");
      throw e;
    }
    req.onsuccess = function (evt) {
      console.log("Insertion in DB successful");
      displayActionSuccess();
      displayPubList(store);
    };
    req.onerror = function() {
      console.error("addPublication error", this.error);
      displayActionFailure(this.error);
    };
  }

  /**
   * @param {string} biblioid
   */
  function deletePublicationFromBib(biblioid) {
    console.log("deletePublication:", arguments);
    var store = getObjectStore(DB_STORE_NAME, 'readwrite');
    var req = store.index('biblioid');
    req.get(biblioid).onsuccess = function(evt) {
      if (typeof evt.target.result == 'undefined') {
        displayActionFailure("No matching record found");
        return;
      }
      deletePublication(evt.target.result.id, store);
    };
    req.onerror = function (evt) {
      console.error("deletePublicationFromBib:", evt.target.errorCode);
    };
  }

  /**
   * @param {number} key
   * @param {IDBObjectStore=} store
   */
  function deletePublication(key, store) {
    console.log("deletePublication:", arguments);

    if (typeof store == 'undefined')
      store = getObjectStore(DB_STORE_NAME, 'readwrite');

    // As per spec http://www.w3.org/TR/IndexedDB/#object-store-deletion-operation
    // the result of the Object Store Deletion Operation algorithm is
    // undefined, so it's not possible to know if some records were actually
    // deleted by looking at the request result.
    var req = store.get(key);
    req.onsuccess = function(evt) {
      var record = evt.target.result;
      console.log("record:", record);
      if (typeof record == 'undefined') {
        displayActionFailure("No matching record found");
        return;
      }
      // Warning: The exact same key used for creation needs to be passed for
      // the deletion. If the key was a Number for creation, then it needs to
      // be a Number for deletion.
      req = store.delete(key);
      req.onsuccess = function(evt) {
        console.log("evt:", evt);
        console.log("evt.target:", evt.target);
        console.log("evt.target.result:", evt.target.result);
        console.log("delete successful");
        displayActionSuccess("Deletion successful");
        displayPubList(store);
      };
      req.onerror = function (evt) {
        console.error("deletePublication:", evt.target.errorCode);
      };
    };
    req.onerror = function (evt) {
      console.error("deletePublication:", evt.target.errorCode);
      };
  }

  function displayActionSuccess(msg) {
    msg = typeof msg != 'undefined' ? "Success: " + msg : "Success";
    $('#msg').html('&lt;span class="action-success"&gt;' + msg + '&lt;/span&gt;');
  }
  function displayActionFailure(msg) {
    msg = typeof msg != 'undefined' ? "Failure: " + msg : "Failure";
    $('#msg').html('&lt;span class="action-failure"&gt;' + msg + '&lt;/span&gt;');
  }
  function resetActionStatus() {
    console.log("resetActionStatus ...");
    $('#msg').empty();
    console.log("resetActionStatus DONE");
  }

  function addEventListeners() {
    console.log("addEventListeners");

    $('#register-form-reset').click(function(evt) {
      resetActionStatus();
    });

    $('#add-button').click(function(evt) {
      console.log("add ...");
      var title = $('#pub-title').val();
      var biblioid = $('#pub-biblioid').val();
      if (!title || !biblioid) {
        displayActionFailure("Required field(s) missing");
        return;
      }
      var year = $('#pub-year').val();
      if (year != '') {
        // Better use Number.isInteger if the engine has EcmaScript 6
        if (isNaN(year))  {
          displayActionFailure("Invalid year");
          return;
        }
        year = Number(year);
      } else {
        year = null;
      }

      var file_input = $('#pub-file');
      var selected_file = file_input.get(0).files[0];
      console.log("selected_file:", selected_file);
      // Keeping a reference on how to reset the file input in the UI once we
      // have its value, but instead of doing that we rather use a "reset" type
      // input in the HTML form.
      //file_input.val(null);
      var file_url = $('#pub-file-url').val();
      if (selected_file) {
        addPublication(biblioid, title, year, selected_file);
      } else if (file_url) {
        addPublicationFromUrl(biblioid, title, year, file_url);
      } else {
        addPublication(biblioid, title, year);
      }

    });

    $('#delete-button').click(function(evt) {
      console.log("delete ...");
      var biblioid = $('#pub-biblioid-to-delete').val();
      var key = $('#key-to-delete').val();

      if (biblioid != '') {
        deletePublicationFromBib(biblioid);
      } else if (key != '') {
        // Better use Number.isInteger if the engine has EcmaScript 6
        if (key == '' || isNaN(key))  {
          displayActionFailure("Invalid key");
          return;
        }
        key = Number(key);
        deletePublication(key);
      }
    });

    $('#clear-store-button').click(function(evt) {
      clearObjectStore();
    });

    var search_button = $('#search-list-button');
    search_button.click(function(evt) {
      displayPubList();
    });

  }

  openDb();
  addEventListeners();

})(); // Immediately-Invoked Function Expression (IIFE)
</pre>

<p>{{ LiveSampleLink('Full_IndexedDB_example', "Test the online live demo") }}</p>

<h2 id="Ver_também">Ver também</h2>

<p>Uma leitura adicional para você encontrar mais informações.</p>

<h3 id="Refências">Refências</h3>

<ul>
 <li><a href="/en/IndexedDB" title="https://developer.mozilla.org/en/IndexedDB">IndexedDB API Reference</a></li>
 <li><a class="external" href="http://www.w3.org/TR/IndexedDB/">Indexed Database API Specification</a></li>
 <li><a href="/en-US/docs/IndexedDB/Using_IndexedDB_in_chrome" title="/en-US/docs/IndexedDB/Using_IndexedDB_in_chrome">Using IndexedDB in chrome</a></li>
 <li><a href="/en-US/docs/Web/API/IndexedDB_API/Using_JavaScript_Generators_in_Firefox">Using JavaScript generators in Firefox</a></li>
 <li>IndexedDB <a class="link-https" href="https://mxr.mozilla.org/mozilla-central/find?text=&amp;string=dom%2FindexedDB%2F.*%5C.idl&amp;regexp=1" title="https://mxr.mozilla.org/mozilla-central/find?text=&amp;string=dom/indexedDB/.*\.idl&amp;regexp=1">interface files</a> in the Firefox source code</li>
</ul>

<h3 id="Guias_e_tutoriais">Guias e tutoriais</h3>

<ul>
 <li><a href="http://www.html5rocks.com/en/tutorials/indexeddb/uidatabinding/">Databinding UI Elements with IndexedDB</a></li>
 <li><a class="external" href="http://msdn.microsoft.com/en-us/scriptjunkie/gg679063.aspx">IndexedDB — The Store in Your Browser</a></li>
</ul>