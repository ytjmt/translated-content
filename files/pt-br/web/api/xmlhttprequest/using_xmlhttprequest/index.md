---
title: Usando XMLHttpRequest
slug: Web/API/XMLHttpRequest/Using_XMLHttpRequest
translation_of: Web/API/XMLHttpRequest/Using_XMLHttpRequest
original_slug: Web/API/XMLHttpRequest/Usando_XMLHttpRequest
---
<p><a href="/en-US/docs/DOM/XMLHttpRequest" title="XMLHttpRequest"><code>XMLHttpRequest</code></a> torna o envio de requisições HTTP muito fácil.  Basta criar uma instância do objeto, abrir uma url e enviar uma requisição. O <a href="/en-US/docs/HTTP/HTTP_response_codes" title="HTTP response codes">status</a> <a href="/en-US/docs/HTTP/HTTP_response_codes" title="HTTP response codes">HTTP </a>do resultado assim como o seu conteúdo estarão disponíveis quando a transação for completada. Esta página descreve alguns casos comuns de uso desse poderoso objeto JavaScript.</p>

<pre class="brush: js">function reqListener () {
  console.log(this.responseText);
};

var oReq = new XMLHttpRequest();
oReq.onload = reqListener;
oReq.open("get", "yourFile.txt", true);
oReq.send();</pre>

<h2 id="Tipos_de_Requisições">Tipos de Requisições</h2>

<p>Uma requisição feita via XMLHttpRequest pode buscar dados de duas maneiras, sícrona e assíncrona. O tipo de requisição é dado pelo argumento <code>async</code> que é opcional (terceiro argumento) e é definido no método XMLHttpRequest <a href="/en-US/docs/DOM/XMLHttpRequest#open()" style="line-height: 1.5;" title="DOM/XMLHttpRequest#open()">open()</a>. Se esse argumento for <code>true</code> ou não especificado, o <code>XMLHttpRequest</code> será processado de maneira assíncrona, caso contrário o processamento será síncrono. Uma discussão detalhada e demonstrações desses dois tipos podem ser encontradas na página <a href="/en-US/docs/DOM/XMLHttpRequest/Synchronous_and_Asynchronous_Requests" style="line-height: 1.5;" title="Synchronous and Asynchronous Requests">requisições síncronas e assíncronas</a>. No geral a melhor prática é a das solicitações assíncronas.</p>

<h2 id="Manipulando_Respostas">Manipulando Respostas</h2>

<p>Existem vários tipos de <a href="http://www.w3.org/TR/XMLHttpRequest2/#response">atributos de resposta</a> definidos pela especificação da W3C para o  XMLHttpRequest.  Eles informam ao cliente que efetuou a requisição XMLHttpRequest informações importantes sobre o status da resposta. Em alguns casos onde se lida com tipos de resposa de não-texto, os tipos de resposta podem envolver alguma manipulação e/ou análise conforme descrito nas seções seguintes.</p>

<h3 id="Analisando_e_manipulando_a_propriedade_responseXML">Analisando e manipulando a propriedade <code>responseXML</code></h3>

<p>Se você utiliza o <code>XMLHttpRequest </code>para obter o conteúdo de um documento XML remoto, a propriedade <code>responseXML</code> será um objeto DOM que contém um documento XML, o que pode dificultar a manipulação e análise.</p>

<p>As cinco formas mais utilizadas para análisar e manipular um arquivo XML são:</p>

<ol>
 <li>Usando <a href="/en-US/docs/XPath" title="XPath">XPath</a> para análisar parte deles.</li>
 <li>Usando <a href="/en-US/docs/JXON" title="JXON">JXON</a> para converter em um Objeto JavaScript.</li>
 <li>Manualmente <a href="/en-US/docs/Parsing_and_serializing_XML" title="Parsing_and_serializing_XML">Parsing and serializing XML</a> para strings ou objetos.</li>
 <li>Usando <a href="/en-US/docs/XMLSerializer" title="XMLSerializer">XMLSerializer</a> para serializar <strong>árvores do DOM para strings ou para arquivos</strong>.</li>
 <li><a href="/en-US/docs/JavaScript/Reference/Global_Objects/RegExp" title="https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/regexp">RegExp </a>pode ser usado se você souber de antemão qual é o conteúdo do XML. Você pode remover quebras de linhas, usando a RegExp para procurar as quebras de linha. No entanto, este é o "último método", caso o código do XML sofra alterações, o método se torna falho.</li>
</ol>

<h3 id="Analisando_e_manipulando_uma_propriedade_responseText_contendo_um_documento_HTML">Analisando e manipulando uma propriedade <code>responseText</code> contendo um documento HTML</h3>

<div class="note"><strong>Nota:</strong> A especificação W3C do <a href="http://dvcs.w3.org/hg/xhr/raw-file/tip/Overview.html">XMLHttpRequest</a> permite analisar HTML através da propriedade <code>XMLHttpRequest.responseXML</code> . Leia o artigo sobre <a href="https://developer.mozilla.org/en-US/docs/HTML_in_XMLHttpRequest" title="HTML_in_XMLHttpRequest">HTML in XMLHttpRequest</a> para maiores detalhes.</div>

<p>Se você usa o <code>XMLHttpRequest</code> para recuperar o conteúdo de uma página HTML remota, a propriedade <code>responseText</code> será uma string contendo um a "sopa" de todos as tags HTML, o que pode ser difícil de manipular e analizar. Existem três formas básicas para analizar esta sopa de string HTML:</p>

<ol>
 <li>Use a propriedade  <code>XMLHttpRequest.responseXML</code>.</li>
 <li>Introduza o conteúdo dentro do corpo de um <a href="https://developer.mozilla.org/en-US/docs/Web/API/DocumentFragment">document fragment</a> Através de <code>fragment.body.innerHTML</code> e percorra o fragmento do DOM.</li>
 <li><a href="https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/RegExp" title="https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/regexp">RegExp </a>pode se usada se você sempre conhece o conteúdo HTML <code>responseText </code>de que tem em mãos. Você pode quere remover quebras de linha, se você usar RegExp para varrer no que diz respeito a quebra de linhas. Contudo, este método é um "último recurso" uma vez que se o código HTML mudar um pouco, o método provavelmente irá falhar.</li>
</ol>

<h2 id="Manipulação_de_dados_binários">Manipulação de dados binários</h2>

<p>Apesar de <code>XMLHttpRequest</code> ser mais comumente usado para enviar e receber dados textual, ele pode ser utilizado para enviar e receber conteúdo binário. Existem vários métodos bem testados para forçar a resposta de um XMLHttpRequest para o envio de dados binário. Eles envolvem a utilização do método  .overrideMimeType()  sobre o objeto  XMLHttpRequest e é uma solução viável.</p>

<pre class="brush:js">var oReq = new XMLHttpRequest();
oReq.open("GET", url, true);
// recupera dados não processados como uma string binária
oReq.overrideMimeType("text/plain; charset=x-user-defined");
/* ... */
</pre>

<p>A especificação XMLHttpRequest Level 2  adiciona novo <a href="http://www.w3.org/TR/XMLHttpRequest2/#the-responsetype-attribute">responseType attributes</a> que tornam o envio e recebimento de dados muito mais fácil.</p>

<pre class="brush:js">var oReq = new XMLHttpRequest();

oReq.open("GET", url, true);
oReq.responseType = "arraybuffer";
oReq.onload = function(e) {
  var arraybuffer = oReq.response; // não é responseText
  /* ... */
}
oReq.send();
</pre>

<p>Para mais exemplos confira a página <a href="/en-US/docs/DOM/XMLHttpRequest/Sending_and_Receiving_Binary_Data" title="DOM/XMLHttpRequest/Sending_and_Receiving_Binary_Data">Sending and Receiving Binary Data</a>.</p>

<h2 id="Monitorando_o_progresso">Monitorando o progresso</h2>

<p><code>XMLHttpRequest</code> fornece a capacidade de ouvir vários eventos que podem ocorrer enquanto o pedido está sendo processado. Isso inclui notificações periódicas de progresso, notificações de erro e assim por diante.</p>

<p>Suporte para evento de progresso DOM monitorando a conexão <code>XMLHttpRequest</code> transfers siga a Web API <a href="http://dev.w3.org/2006/webapi/progress/Progress.html">specification for progress events</a>: estes eventos implementam a interface {{domxref("ProgressEvent")}} .</p>

<pre class="brush:js">var oReq = new XMLHttpRequest();

oReq.addEventListener("progress", updateProgress, false);
oReq.addEventListener("load", transferComplete, false);
oReq.addEventListener("error", transferFailed, false);
oReq.addEventListener("abort", transferCanceled, false);

oReq.open();

// ...A transferência foi cancelada pelo usuário

// progresso de transferências do servidor para o cliente (downloads)
function updateProgress (oEvent) {
  if (oEvent.lengthComputable) {
    var percentComplete = oEvent.loaded / oEvent.total;
    // ...
  } else {
    // Não é possível calcular informações de progresso uma vez que a dimensão total é desconhecida
  }
}

function transferComplete(evt) {
  alert("A transferência foi concluída.");
}

function transferFailed(evt) {
  alert("Um erro ocorreu durante a transferência do arquivo.");
}

function transferCanceled(evt) {
  alert("A transferência foi cancelada pelo usuário.");
}</pre>

<p>Lines 3-6 adiciona receptores de eventos (event listeners) para os vários que são enviados ao executar uma transferência de dados usando <code>XMLHttpRequest</code>.</p>

<div class="note"><strong>Nota:</strong> Você precisa adicionar os receptores de eventos (event listeners) antes de chamar <code>open()</code> sobre a requisição.  Caso contrário, os eventos de prograsso não dispararão..</div>

<p>O manipulador de evento  de prograsso, especificado pela função <code>updateProgress()</code> neste exemplo, recebe o número total de bytes para transferir, bem como o número de bytes transferidos até o momento em total de eventos e campos  carregados . No entanto, se o campo lengthComputable é false, o comprimento total não é conhecido e será zero..</p>

<p>Eventos de progresso existem para ambos as transferências de download e upload. The download events are fired on the <code>XMLHttpRequest</code> object itself, as shown in the above sample. The upload events are fired on the <code>XMLHttpRequest.upload</code> object, as shown below:</p>

<pre class="brush:js">var oReq = new XMLHttpRequest();

oReq.upload.addEventListener("progress", updateProgress, false);
oReq.upload.addEventListener("load", transferComplete, false);
oReq.upload.addEventListener("error", transferFailed, false);
oReq.upload.addEventListener("abort", transferCanceled, false);

oReq.open();
</pre>

<div class="note"><strong>Nota:</strong> eventos de progresso não estão disponíveis para o arquivo<code>:</code> protocol.</div>

<div class="note"><strong>Nota</strong>: Atualmente, existem bugs em aberto para o evento de progresso que continua fetando a versão 25 do Firefox sobre <a href="https://bugzilla.mozilla.org/show_bug.cgi?id=908375">OS X</a> e <a href="https://bugzilla.mozilla.org/show_bug.cgi?id=786953">Linux</a>.</div>

<div class="note">
<p><strong>Nota:</strong> Iniciando no {{Gecko("9.0")}}, eventos de progresso agora podem ser invocados a entrar para cada pedaço de dados recebidos, incluindo o último bloco, nos casos em que o último pacote é recebido e a conexão fechada antes do evento progresso ser disparado. Neste caso, o evento de progresso é automaticamente acionado quando o evento load ocorre para esse pacote. Isso permite que você agora acompanhe de forma confiável apenas observando o evento de progresso</p>
</div>

<div class="note">
<p><strong>Nota:</strong> A partir do {{Gecko("12.0")}}, se o seu evento de progresso e chamado com um <code>responseType</code> de "moz-blob", o valor da resposta será um {{domxref("Blob")}} contendo os dados recebidos até agorar.</p>
</div>

<p>POde-se também detectar todas as três condições de fim de carga (<code>abort</code>, <code>load</code>, or <code>error</code>) usando o evento <code>loadend</code>:</p>

<pre class="brush:js">req.addEventListener("loadend", loadEnd, false);

function loadEnd(e) {
  alert("A transferência terminou (embora não sabemos se ele conseguiu ou não).");
}
</pre>

<p>Note que não há nenhuma maneira de ter certeza a partir da informação recebida pelo evento loadend sobre qual condição causou a operação de encerrar; no entanto, você pode usar isso para lidar com tarefas que precisam ser realizadas em todos os cenários de fim-de-transferência.</p>

<h2 id="Submitting_forms_and_uploading_files">Submitting forms and uploading files</h2>

<p>Instances of <code>XMLHttpRequest</code> can be used to submit forms in two ways:</p>

<ul>
 <li>using nothing but <em>pure</em> AJAX,</li>
 <li>using the <a href="/en-US/docs/DOM/XMLHttpRequest/FormData" title="DOM/XMLHttpRequest/FormData"><code>FormData</code></a> API.</li>
</ul>

<p>The <strong>second way</strong> (using the <a href="/en-US/docs/DOM/XMLHttpRequest/FormData" title="DOM/XMLHttpRequest/FormData"><code>FormData</code></a> API) is the simplest and the fastest, but has the disadvantage that <strong>the data thus collected can not be <a href="/en-US/docs/JavaScript/Reference/Global_Objects/JSON/stringify" title="/en-US/docs/JavaScript/Reference/Global_Objects/JSON/stringify">stringified</a></strong>: they are in every way <em>a blob</em>. It is the best solution for simple cases.<br>
 The <strong>first way</strong> (<em>pure</em> AJAX) is instead the most complex, but in compensation is also the most flexible and powerful: it lends itself to wider uses and <strong>the data thus collected can be <a href="/en-US/docs/JavaScript/Reference/Global_Objects/JSON/stringify" title="/en-US/docs/JavaScript/Reference/Global_Objects/JSON/stringify">stringified</a></strong><strong> and reused for other purposes</strong> such as, for example, populating the <em>status object</em> during a <a href="/en-US/docs/DOM/Manipulating_the_browser_history" title="/en-US/docs/DOM/Manipulating_the_browser_history">manipulation of the browser history</a>, or other.</p>

<h3 id="Using_nothing_but_pure_AJAX">Using nothing but <em>pure</em> AJAX</h3>

<p>Submitting forms without the <a href="/en-US/docs/DOM/XMLHttpRequest/FormData" title="DOM/XMLHttpRequest/FormData"><code>FormData</code></a> API does not require other APIs, except that, only <strong>if you want to upload one or more files</strong>, the <a href="/en-US/docs/DOM/FileReader" title="/en-US/docs/DOM/FileReader"><code>FileReader</code></a> API.</p>

<h4 id="A_brief_introduction_to_the_submit_methods">A brief introduction to the submit methods</h4>

<p>An html {{ HTMLElement("form") }} can be sent in four ways:</p>

<ul>
 <li>using the <code>POST</code> method and setting the <code>enctype</code> attribute to <code>application/x-www-form-urlencoded</code> (default);</li>
 <li>using the <code>POST</code> method and setting the <code>enctype</code> attribute to <code>text/plain</code>;</li>
 <li>using the <code>POST</code> method and setting the <code>enctype</code> attribute to <code>multipart/form-data</code>;</li>
 <li>using the <code>GET</code> method (in this case the <code>enctype</code> attribute will be ignored).</li>
</ul>

<p>Now, consider to submit a form containing only two fields, named <code>foo</code> and <code>baz</code>. If you are using the <code>POST</code> method, the server will receive a string similar to one of the following three ones depending on the encoding type you are using:</p>

<ul>
 <li>
  <p>Method: <code>POST</code>; Encoding type: <code>application/x-www-form-urlencoded</code> (default):</p>

  <pre>Content-Type: application/x-www-form-urlencoded

foo=bar&amp;baz=The+first+line.&amp;#37;0D%0AThe+second+line.%0D%0A</pre>
 </li>
 <li>
  <p>Method: <code>POST</code>; Encoding type: <code>text/plain</code>:</p>

  <pre>Content-Type: text/plain

foo=bar
baz=The first line.
The second line.</pre>
 </li>
 <li>
  <p>Method: <code>POST</code>; Encoding type: <code>multipart/form-data</code>:</p>

  <pre style="height: 100px; overflow: auto;">Content-Type: multipart/form-data; boundary=---------------------------314911788813839

-----------------------------314911788813839
Content-Disposition: form-data; name="foo"

bar
-----------------------------314911788813839
Content-Disposition: form-data; name="baz"

The first line.
The second line.

-----------------------------314911788813839--</pre>
 </li>
</ul>

<p>Instead, if you are using the <code>GET</code> method, a string like the following will be simply added to the URL:</p>

<pre>?foo=bar&amp;baz=The%20first%20line.%0AThe%20second%20line.</pre>

<h4 id="A_little_vanilla_framework">A little <em>vanilla</em> framework</h4>

<p>All these things are done automatically by the web browser whenever you submit a {{ HTMLElement("form") }}. But if you want to do the same things using JavaScript you have to instruct the interpreter about <em>all</em> things. So, how to send forms in <em>pure</em> AJAX is too complex to be explained in detail here. For this reason we posted here <strong>a complete (but still didactic) framework</strong>, which is able to use all the four ways of <em>submit</em> and, also, to <strong>upload files</strong>:</p>

<div style="height: 400px; margin-bottom: 12px; overflow: auto;">
<pre class="brush: html">&lt;!doctype html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;meta http-equiv="Content-Type" content="text/html; charset=UTF-8" /&gt;
&lt;title&gt;Sending forms with pure AJAX &amp;ndash; MDN&lt;/title&gt;
&lt;script type="text/javascript"&gt;

"use strict";

/*\
|*|
|*|  :: XMLHttpRequest.prototype.sendAsBinary() Polifyll ::
|*|
|*|  https://developer.mozilla.org/en-US/docs/DOM/XMLHttpRequest#sendAsBinary()
\*/

if (!XMLHttpRequest.prototype.sendAsBinary) {
  XMLHttpRequest.prototype.sendAsBinary = function (sData) {
    var nBytes = sData.length, ui8Data = new Uint8Array(nBytes);
    for (var nIdx = 0; nIdx &lt; nBytes; nIdx++) {
      ui8Data[nIdx] = sData.charCodeAt(nIdx) &amp; 0xff;
    }
    /* send as ArrayBufferView...: */
    this.send(ui8Data);
    /* ...or as ArrayBuffer (legacy)...: this.send(ui8Data.buffer); */
  };
}

/*\
|*|
|*|  :: AJAX Form Submit Framework ::
|*|
|*|  https://developer.mozilla.org/en-US/docs/DOM/XMLHttpRequest/Using_XMLHttpRequest
|*|
|*|  This framework is released under the GNU Public License, version 3 or later.
|*|  http://www.gnu.org/licenses/gpl-3.0-standalone.html
|*|
|*|  Syntax:
|*|
|*|   AJAXSubmit(HTMLFormElement);
\*/

var AJAXSubmit = (function () {

  function ajaxSuccess () {
    /* console.log("AJAXSubmit - Success!"); */
    alert(this.responseText);
    /* you can get the serialized data through the "submittedData" custom property: */
    /* alert(JSON.stringify(this.submittedData)); */
  }

  function submitData (oData) {
    /* the AJAX request... */
    var oAjaxReq = new XMLHttpRequest();
    oAjaxReq.submittedData = oData;
    oAjaxReq.onload = ajaxSuccess;
    if (oData.technique === 0) {
      /* method is GET */
      oAjaxReq.open("get", oData.receiver.replace(/(?:\?.*)?$/, oData.segments.length &gt; 0 ? "?" + oData.segments.join("&amp;") : ""), true);
      oAjaxReq.send(null);
    } else {
      /* method is POST */
      oAjaxReq.open("post", oData.receiver, true);
      if (oData.technique === 3) {
        /* enctype is multipart/form-data */
        var sBoundary = "---------------------------" + Date.now().toString(16);
        oAjaxReq.setRequestHeader("Content-Type", "multipart\/form-data; boundary=" + sBoundary);
        oAjaxReq.sendAsBinary("--" + sBoundary + "\r\n" + oData.segments.join("--" + sBoundary + "\r\n") + "--" + sBoundary + "--\r\n");
      } else {
        /* enctype is application/x-www-form-urlencoded or text/plain */
        oAjaxReq.setRequestHeader("Content-Type", oData.contentType);
        oAjaxReq.send(oData.segments.join(oData.technique === 2 ? "\r\n" : "&amp;"));
      }
    }
  }

  function processStatus (oData) {
    if (oData.status &gt; 0) { return; }
    /* the form is now totally serialized! do something before sending it to the server... */
    /* doSomething(oData); */
    /* console.log("AJAXSubmit - The form is now serialized. Submitting..."); */
    submitData (oData);
  }

  function pushSegment (oFREvt) {
    this.owner.segments[this.segmentIdx] += oFREvt.target.result + "\r\n";
    this.owner.status--;
    processStatus(this.owner);
  }

  function plainEscape (sText) {
    /* how should I treat a text/plain form encoding? what characters are not allowed? this is what I suppose...: */
    /* "4\3\7 - Einstein said E=mc2" ----&gt; "4\\3\\7\ -\ Einstein\ said\ E\=mc2" */
    return sText.replace(/[\s\=\\]/g, "\\$&amp;");
  }

  function SubmitRequest (oTarget) {
    var nFile, sFieldType, oField, oSegmReq, oFile, bIsPost = oTarget.method.toLowerCase() === "post";
    /* console.log("AJAXSubmit - Serializing form..."); */
    this.contentType = bIsPost &amp;&amp; oTarget.enctype ? oTarget.enctype : "application\/x-www-form-urlencoded";
    this.technique = bIsPost ? this.contentType === "multipart\/form-data" ? 3 : this.contentType === "text\/plain" ? 2 : 1 : 0;
    this.receiver = oTarget.action;
    this.status = 0;
    this.segments = [];
    var fFilter = this.technique === 2 ? plainEscape : escape;
    for (var nItem = 0; nItem &lt; oTarget.elements.length; nItem++) {
      oField = oTarget.elements[nItem];
      if (!oField.hasAttribute("name")) { continue; }
      sFieldType = oField.nodeName.toUpperCase() === "INPUT" ? oField.getAttribute("type").toUpperCase() : "TEXT";
      if (sFieldType === "FILE" &amp;&amp; oField.files.length &gt; 0) {
        if (this.technique === 3) {
          /* enctype is multipart/form-data */
          for (nFile = 0; nFile &lt; oField.files.length; nFile++) {
            oFile = oField.files[nFile];
            oSegmReq = new FileReader();
            /* (custom properties:) */
            oSegmReq.segmentIdx = this.segments.length;
            oSegmReq.owner = this;
            /* (end of custom properties) */
            oSegmReq.onload = pushSegment;
            this.segments.push("Content-Disposition: form-data; name=\"" + oField.name + "\"; filename=\""+ oFile.name + "\"\r\nContent-Type: " + oFile.type + "\r\n\r\n");
            this.status++;
            oSegmReq.readAsBinaryString(oFile);
          }
        } else {
          /* enctype is application/x-www-form-urlencoded or text/plain or method is GET: files will not be sent! */
          for (nFile = 0; nFile &lt; oField.files.length; this.segments.push(fFilter(oField.name) + "=" + fFilter(oField.files[nFile++].name)));
        }
      } else if ((sFieldType !== "RADIO" &amp;&amp; sFieldType !== "CHECKBOX") || oField.checked) {
        /* field type is not FILE or is FILE but is empty */
        this.segments.push(
          this.technique === 3 ? /* enctype is multipart/form-data */
            "Content-Disposition: form-data; name=\"" + oField.name + "\"\r\n\r\n" + oField.value + "\r\n"
          : /* enctype is application/x-www-form-urlencoded or text/plain or method is GET */
            fFilter(oField.name) + "=" + fFilter(oField.value)
        );
      }
    }
    processStatus(this);
  }

  return function (oFormElement) {
    if (!oFormElement.action) { return; }
    new SubmitRequest(oFormElement);
  };

})();

&lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;

&lt;h1&gt;Sending forms with pure AJAX&lt;/h1&gt;

&lt;h2&gt;Using the GET method&lt;/h2&gt;

&lt;form action="register.php" method="get" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Registration example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;h2&gt;Using the POST method&lt;/h2&gt;
&lt;h3&gt;Enctype: application/x-www-form-urlencoded (default)&lt;/h3&gt;

&lt;form action="register.php" method="post" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Registration example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;h3&gt;Enctype: text/plain&lt;/h3&gt;

&lt;form action="register.php" method="post" enctype="text/plain" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Registration example&lt;/legend&gt;
    &lt;p&gt;
      Your name: &lt;input type="text" name="user" /&gt;
    &lt;/p&gt;
    &lt;p&gt;
      Your message:&lt;br /&gt;
      &lt;textarea name="message" cols="40" rows="8"&gt;&lt;/textarea&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;h3&gt;Enctype: multipart/form-data&lt;/h3&gt;

&lt;form action="register.php" method="post" enctype="multipart/form-data" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Upload example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;&lt;br /&gt;
      Sex:
      &lt;input id="sex_male" type="radio" name="sex" value="male" /&gt; &lt;label for="sex_male"&gt;Male&lt;/label&gt;
      &lt;input id="sex_female" type="radio" name="sex" value="female" /&gt; &lt;label for="sex_female"&gt;Female&lt;/label&gt;&lt;br /&gt;
      Password: &lt;input type="password" name="secret" /&gt;&lt;br /&gt;
      What do you prefer:
      &lt;select name="image_type"&gt;
        &lt;option&gt;Books&lt;/option&gt;
        &lt;option&gt;Cinema&lt;/option&gt;
        &lt;option&gt;TV&lt;/option&gt;
      &lt;/select&gt;
    &lt;/p&gt;
    &lt;p&gt;
      Post your photos:
      &lt;input type="file" multiple name="photos[]"&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input id="vehicle_bike" type="checkbox" name="vehicle[]" value="Bike" /&gt; &lt;label for="vehicle_bike"&gt;I have a bike&lt;/label&gt;&lt;br /&gt;
      &lt;input id="vehicle_car" type="checkbox" name="vehicle[]" value="Car" /&gt; &lt;label for="vehicle_car"&gt;I have a car&lt;/label&gt;
    &lt;/p&gt;
    &lt;p&gt;
      Describe yourself:&lt;br /&gt;
      &lt;textarea name="description" cols="50" rows="8"&gt;&lt;/textarea&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;/body&gt;
&lt;/html&gt;</pre>
</div>

<p>To test it, please, create a page named <strong>register.php</strong> (which is the <code>action</code> attribute of these sample forms) and just put the following <em>minimalistic </em>content:</p>

<pre class="brush: php">&lt;?php

  /* register.php */

  header("Content-type: text/plain");

  echo ":: data received via GET ::\n\n";
  print_r($_GET);

  echo "\n\n:: Data received via POST ::\n\n";
  print_r($_POST);

  echo "\n\n:: Data received as \"raw\" (text/plain encoding) ::\n\n";
  if (isset($HTTP_RAW_POST_DATA)) { echo $HTTP_RAW_POST_DATA; }

  echo "\n\n:: Files received ::\n\n";
  print_r($_FILES);

?&gt;</pre>

<p>The syntax of this script is the following:</p>

<pre class="syntaxbox">AJAXSubmit(myForm);</pre>

<div class="note"><strong>Note:</strong> This little <em>vanilla</em> framework <strong>uses the <a href="/en-US/docs/DOM/FileReader" title="/en-US/docs/DOM/FileReader"><code>FileReader</code></a> API</strong>, which is <em>a recent technique</em> (but only when there are files to upload, the <code>method</code> of the {{ HTMLElement("form") }} is <code>POST</code> and the <code>enctype</code> attribute is setted to <code>multipart/form-data</code>). For this reason, <strong>the <em>pure-AJAX</em> upload is to be considered an experimental technique</strong>. Instead, if you don't want to upload files, this framework will not use any recent API.<br>
Note also that <strong>the best way to send binary content is using <a href="/en-US/docs/JavaScript/Typed_arrays/ArrayBuffer" title="/en-US/docs/JavaScript/Typed_arrays/ArrayBuffer">ArrayBuffers</a> or <a href="/en-US/docs/DOM/Blob" title="/en-US/docs/DOM/Blob">Blobs</a> in conjuncton with the <a href="/en-US/docs/DOM/XMLHttpRequest#send%28%29" title="/en-US/docs/DOM/XMLHttpRequest#send()"><code>send()</code></a> method and, possibly, with the <a href="/en-US/docs/DOM/FileReader#readAsArrayBuffer()" title="/en-US/docs/DOM/FileReader#readAsArrayBuffer()"><code>readAsArrayBuffer()</code></a> method of the <a href="/en-US/docs/DOM/FileReader" title="/en-US/docs/DOM/FileReader"><code>FileReader</code></a> API</strong>. But, since the aim of this little script is to work with a <em><a href="/en-US/docs/JavaScript/Reference/Global_Objects/JSON/stringify" title="/en-US/docs/JavaScript/Reference/Global_Objects/JSON/stringify">stringifiable</a></em> raw data, we used the <a href="/en-US/docs/DOM/XMLHttpRequest#sendAsBinary%28%29" title="/en-US/docs/DOM/XMLHttpRequest#sendAsBinary()"><code>sendAsBinary()</code></a> method in conjunction with the <a href="/en-US/docs/DOM/FileReader#readAsBinaryString%28%29" title="/en-US/docs/DOM/FileReader#readAsBinaryString()"><code>readAsBinaryString()</code></a> method of the <a href="/en-US/docs/DOM/FileReader" title="/en-US/docs/DOM/FileReader"><code>FileReader</code></a> API. So, this is <strong>the best solution when working with a relatively few data which must be <a href="/en-US/docs/JavaScript/Reference/Global_Objects/JSON/stringify" title="/en-US/docs/JavaScript/Reference/Global_Objects/JSON/stringify">stringified</a> in order to be reused later</strong>. Anyhow, since working with strings instead of <a href="/en-US/docs/JavaScript/Typed_arrays" title="/en-US/docs/JavaScript/Typed_arrays">typed arrays</a> implies a greater waste of resources, this script makes sense only when you are dealing with <em>small</em> files (like images, documents, mp3, etc.). Otherwise, if you don't want to stringify the submitted or uploaded data, in addition to <a href="/en-US/docs/JavaScript/Typed_arrays" title="/en-US/docs/JavaScript/Typed_arrays">typed arrays</a>, consider also the use of <strong>the <a href="/en-US/docs/DOM/XMLHttpRequest/FormData" title="DOM/XMLHttpRequest/FormData"><code>FormData</code></a> API</strong>.</div>

<h3 id="Using_FormData_objects">Using FormData objects</h3>

<p>The <a href="/en-US/docs/DOM/XMLHttpRequest/FormData" title="DOM/XMLHttpRequest/FormData"><code>FormData</code></a> constructor lets you compile a set of key/value pairs to send using <code>XMLHttpRequest</code>. Its primarily intended for use in sending form data, but can be used independently from forms in order to transmit keyed data. The transmitted data is in the same format that the form's <code>submit()</code> method would use to send the data if the form's encoding type were set to "multipart/form-data". FormData objects can be utilized in a number of ways with an XMLHttpRequest. For examples and explanations of how one can utilize FormData with XMLHttpRequests see the <a href="/en-US/docs/DOM/XMLHttpRequest/FormData/Using_FormData_Objects" title="Using FormData Objects">Using FormData Objects</a> page. For didactic purpose only we post here <strong>a <em>translation</em> of <a href="#A_little_vanilla_framework" title="#A_little_vanilla_framework">the previous example</a> transformed so as to make use of the <code>FormData</code> API</strong>. Note the brevity of the code:</p>

<div style="height: 400px; margin-bottom: 12px; overflow: auto;">
<pre class="brush: html">&lt;!doctype html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;meta http-equiv="Content-Type" content="text/html; charset=UTF-8" /&gt;
&lt;title&gt;Sending forms with FormData &amp;ndash; MDN&lt;/title&gt;
&lt;script type="text/javascript"&gt;
"use strict";

function ajaxSuccess () {
  alert(this.responseText);
}

function AJAXSubmit (oFormElement) {
  if (!oFormElement.action) { return; }
  var oReq = new XMLHttpRequest();
  oReq.onload = ajaxSuccess;
  if (oFormElement.method.toLowerCase() === "post") {
    oReq.open("post", oFormElement.action, true);
    oReq.send(new FormData(oFormElement));
  } else {
    var oField, sFieldType, nFile, sSearch = "";
    for (var nItem = 0; nItem &lt; oFormElement.elements.length; nItem++) {
      oField = oFormElement.elements[nItem];
      if (!oField.hasAttribute("name")) { continue; }
      sFieldType = oField.nodeName.toUpperCase() === "INPUT" ? oField.getAttribute("type").toUpperCase() : "TEXT";
      if (sFieldType === "FILE") {
        for (nFile = 0; nFile &lt; oField.files.length; sSearch += "&amp;" + escape(oField.name) + "=" + escape(oField.files[nFile++].name));
      } else if ((sFieldType !== "RADIO" &amp;&amp; sFieldType !== "CHECKBOX") || oField.checked) {
        sSearch += "&amp;" + escape(oField.name) + "=" + escape(oField.value);
      }
    }
    oReq.open("get", oFormElement.action.replace(/(?:\?.*)?$/, sSearch.replace(/^&amp;/, "?")), true);
    oReq.send(null);
  }
}
&lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;

&lt;h1&gt;Sending forms with FormData&lt;/h1&gt;

&lt;h2&gt;Using the GET method&lt;/h2&gt;

&lt;form action="register.php" method="get" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Registration example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;h2&gt;Using the POST method&lt;/h2&gt;
&lt;h3&gt;Enctype: application/x-www-form-urlencoded (default)&lt;/h3&gt;

&lt;form action="register.php" method="post" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Registration example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;h3&gt;Enctype: text/plain&lt;/h3&gt;

&lt;p&gt;The text/plain encoding is not supported by the FormData API.&lt;/p&gt;

&lt;h3&gt;Enctype: multipart/form-data&lt;/h3&gt;

&lt;form action="register.php" method="post" enctype="multipart/form-data" onsubmit="AJAXSubmit(this); return false;"&gt;
  &lt;fieldset&gt;
    &lt;legend&gt;Upload example&lt;/legend&gt;
    &lt;p&gt;
      First name: &lt;input type="text" name="firstname" /&gt;&lt;br /&gt;
      Last name: &lt;input type="text" name="lastname" /&gt;&lt;br /&gt;
      Sex:
      &lt;input id="sex_male" type="radio" name="sex" value="male" /&gt; &lt;label for="sex_male"&gt;Male&lt;/label&gt;
      &lt;input id="sex_female" type="radio" name="sex" value="female" /&gt; &lt;label for="sex_female"&gt;Female&lt;/label&gt;&lt;br /&gt;
      Password: &lt;input type="password" name="secret" /&gt;&lt;br /&gt;
      What do you prefer:
      &lt;select name="image_type"&gt;
        &lt;option&gt;Books&lt;/option&gt;
        &lt;option&gt;Cinema&lt;/option&gt;
        &lt;option&gt;TV&lt;/option&gt;
      &lt;/select&gt;
    &lt;/p&gt;
    &lt;p&gt;
      Post your photos:
      &lt;input type="file" multiple name="photos[]"&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input id="vehicle_bike" type="checkbox" name="vehicle[]" value="Bike" /&gt; &lt;label for="vehicle_bike"&gt;I have a bike&lt;/label&gt;&lt;br /&gt;
      &lt;input id="vehicle_car" type="checkbox" name="vehicle[]" value="Car" /&gt; &lt;label for="vehicle_car"&gt;I have a car&lt;/label&gt;
    &lt;/p&gt;
    &lt;p&gt;
      Describe yourself:&lt;br /&gt;
      &lt;textarea name="description" cols="50" rows="8"&gt;&lt;/textarea&gt;
    &lt;/p&gt;
    &lt;p&gt;
      &lt;input type="submit" value="Submit" /&gt;
    &lt;/p&gt;
  &lt;/fieldset&gt;
&lt;/form&gt;

&lt;/body&gt;
&lt;/html&gt;</pre>
</div>

<div class="note"><strong>Note:</strong> As we said, <strong><code>FormData</code> objects are not <a href="/en-US/docs/JavaScript/Reference/Global_Objects/JSON/stringify" title="/en-US/docs/JavaScript/Reference/Global_Objects/JSON/stringify">stringifiable</a> objects</strong>. If you want to stringify a submitted data, use <a href="#A_little_vanilla_framework" title="#A_little_vanilla_framework">the previous <em>pure</em>-AJAX example</a>. Note also that, although in this example there are some <code>file</code> {{ HTMLElement("input") }} fields, <strong>when you submit a form through the <code>FormData</code> API you do not need to use the <a href="/en-US/docs/DOM/FileReader" title="/en-US/docs/DOM/FileReader"><code>FileReader</code></a> API also</strong>: files are automatically loaded and uploaded.</div>

<h2 id="Cross-site_XMLHttpRequest">Cross-site XMLHttpRequest</h2>

<p>Modern browsers support cross-site requests by implementing the web applications working group's <a href="/en-US/docs/HTTP_access_control" title="HTTP access control">Access Control for Cross-Site Requests</a> standard.  As long as the server is configured to allow requests from your web application's origin, <code>XMLHttpRequest</code> will work.  Otherwise, an <code>INVALID_ACCESS_ERR</code> exception is thrown.</p>

<h2 id="Bypassing_the_cache">Bypassing the cache</h2>

<p>A, cross-browser compatible approach to bypassing the cache is to append a timestamp to the URL, being sure to include a "?" or "&amp;" as appropriate.  For example:</p>

<pre>http://foo.com/bar.html -&gt; http://foo.com/bar.html?12345
http://foo.com/bar.html?foobar=baz -&gt; http://foo.com/bar.html?foobar=baz&amp;12345
</pre>

<p>Since the local cache is indexed by URL, this causes every request to be unique, thereby bypassing the cache.</p>

<p>You can automatically adjust URLs using the following code:</p>

<pre class="brush:js">var oReq = new XMLHttpRequest();

oReq.open("GET", url + ((/\?/).test(url) ? "&amp;" : "?") + (new Date()).getTime(), true);
oReq.send(null);</pre>

<h2 id="Security">Security</h2>

<p>{{fx_minversion_note(3, "Versions of Firefox prior to Firefox 3 allowed you to set the preference <code>capability.policy..XMLHttpRequest.open</code> to <code>allAccess</code> to give specific sites cross-site access.  This is no longer supported.")}}</p>

<p>{{fx_minversion_note(5, "Versions of Firefox prior to Firefox 5 could use <code>netscape.security.PrivilegeManager.enablePrivilege(\"UniversalBrowserRead\");</code> to request cross-site access. This is no longer supported, even though it produces no warning and permission dialog is still presented.")}}</p>

<p>The recommended way to enable cross-site scripting is to use the <code>Access-Control-Allow-Origin </code> HTTP header in the response to the XMLHttpRequest.</p>

<h3 id="XMLHttpRequests_being_stopped">XMLHttpRequests being stopped</h3>

<p>If you end up with an XMLHttpRequest having <code>status=0</code> and <code>statusText=null</code>, it means that the request was not allowed to be performed. It was <code><a href="http://www.w3.org/TR/XMLHttpRequest/#dom-xmlhttprequest-unsent">UNSENT</a></code>. A likely cause for this is when the <a href="http://www.w3.org/TR/XMLHttpRequest/#xmlhttprequest-origin" style="outline: 1px dotted; outline-offset: 0pt;"><code>XMLHttpRequest</code> origin</a> (at the creation of the XMLHttpRequest) has changed when the XMLHttpRequest is then <code>open()</code>. This case can happen for example when one has an XMLHttpRequest that gets fired on an onunload event for a window: the XMLHttpRequest gets in fact created when the window to be closed is still there, and then the request is sent (ie <code>open()</code>) when this window has lost its focus and potentially different window has gained focus. The way to avoid this problem is to set a listener on the new window "activate" event that gets set when the old window has its "unload" event fired.</p>

<h2 id="Using_XMLHttpRequest_from_JavaScript_modules_XPCOM_components">Using XMLHttpRequest from JavaScript modules / XPCOM components</h2>

<p>Instantiating <code>XMLHttpRequest</code> from a <a href="/en-US/docs/JavaScript_code_modules/Using" title="https://developer.mozilla.org/en/JavaScript_code_modules/Using_JavaScript_code_modules">JavaScript module</a> or an XPCOM component works a little differently; it can't be instantiated using the <code>XMLHttpRequest()</code> constructor. The constructor is not defined inside components and the code results in an error. The best way to work around this is to use the XPCOM component constructor.</p>

<pre class="brush:js">const XMLHttpRequest = Components.Constructor("@mozilla.org/xmlextras/xmlhttprequest;1");
var oReq = XMLHttpRequest();</pre>

<p>Unfortunately in versions of Gecko prior to Gecko 16 there is a bug which can cause requests created this way to be cancelled for no reason.  If you need your code to work on Gecko 15 or earlier, you can get the XMLHttpRequest constructor from the hidden DOM window like so.</p>

<pre class="brush:js">const { XMLHttpRequest } = Components.classes["@mozilla.org/appshell/appShellService;1"]
                                     .getService(Components.interfaces.nsIAppShellService)
                                     .hiddenDOMWindow;
var oReq = XMLHttpRequest();
</pre>

<h2 id="See_also">See also</h2>

<ol>
 <li><a href="/en-US/docs/AJAX/Getting_Started" title="AJAX/Getting_Started">MDC AJAX introduction</a></li>
 <li><a href="/en-US/docs/HTTP_access_control" title="HTTP access control">HTTP access control</a></li>
 <li><a href="/en-US/docs/How_to_check_the_security_state_of_an_XMLHTTPRequest_over_SSL" title="How to check the security state of an XMLHTTPRequest over SSL">How to check the security state of an XMLHTTPRequest over SSL</a></li>
 <li><a href="http://www.peej.co.uk/articles/rich-user-experience.html">XMLHttpRequest - REST and the Rich User Experience</a></li>
 <li><a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/xmlsdk/html/xmobjxmlhttprequest.asp">Microsoft documentation</a></li>
 <li><a href="http://developer.apple.com/internet/webcontent/xmlhttpreq.html">Apple developers' reference</a></li>
 <li><a href="http://jibbering.com/2002/4/httprequest.html">"Using the XMLHttpRequest Object" (jibbering.com)</a></li>
 <li><a href="http://www.w3.org/TR/XMLHttpRequest/">The XMLHttpRequest Object: W3C Specification</a></li>
 <li><a href="http://dev.w3.org/2006/webapi/progress/Progress.html">Web Progress Events specification</a></li>
</ol>