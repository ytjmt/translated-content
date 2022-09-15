---
title: WindowOrWorkerGlobalScope.setInterval()
slug: Web/API/setInterval
translation_of: Web/API/WindowOrWorkerGlobalScope/setInterval
original_slug: Web/API/WindowOrWorkerGlobalScope/setInterval
---
<div>{{APIRef("HTML DOM")}}</div>

<div> </div>

<p>O método <strong><code>setInterval() </code></strong>oferecido das interfaces {{domxref("Window")}} e {{domxref("Worker")}}, repetem chamadas de funções ou executam trechos de código, com um tempo de espera fixo entre cada chamada. Isso retorna um ID único para o intervalo, podendo remove-lo mais tarde apenas o chamando {{domxref("WindowOrWorkerGlobalScope.clearInterval", "clearInterval()")}}. Este metodo é definido pelo mixin {{domxref("WindowOrWorkerGlobalScope")}}.</p>

<h2 id="Sintaxe.">Sintaxe.</h2>

<pre class="syntaxbox"><em>var intervalID</em> = scope.setInterval(<em>func</em>, <em>delay</em>[, <em>param1</em>, <em>param2</em>, ...]);
<em>var intervalID</em> = scope.setInterval(<em>code</em>, <em>delay</em>);
</pre>

<h3 id="Parâmetros">Parâmetros</h3>

<dl>
 <dt><code>func</code></dt>
 <dd>Uma {{jsxref("function")}} para ser executada a cada <code>delay</code> em milisegundos.  Não é passado nenhum parâmetro para a função, e não retorna nenhum valor esperado.</dd>
 <dt><code>code</code></dt>
 <dd>Uma sintaxe opcional permite você incuir uma string ao invés de uma função, no qual é compilado e executada a cada <code>delay</code> em milisegundos. Esta sintaxe <em>não é recomendada </em>pelos mesmos motivos que envolvem riscos de segurança de {{jsxref("eval", "eval()")}}.</dd>
 <dt><code>delay</code></dt>
 <dd>O tempo, em milisegundos (milésimos de segundo), o temporizador deve atrasar entre cada execução de uma especifica função ou código. Se esse parâmetro for menos que 10, um valor de 10 é usado. Note que o atraso pode vir a ser mais longo; veja {{SectionOnPage("/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout", "Reasons for delays longer than specified")}} para exemplos.</dd>
 <dt><code>param1, ..., paramN</code> {{optional_inline}}</dt>
 <dd>Parâmetros adicionais que são passados através da função especificada pela <em>func</em> quando o temporizador expirar.</dd>
</dl>

<div class="note">
<p><strong>Note</strong>: Passing additional parameters to <code>setInterval()</code> in the first syntax does not work in Internet Explorer 9 and earlier. If you want to enable this functionality on that browser, you must use a polyfill (see the <a href="#Callback_arguments">Callback arguments</a> section).</p>
</div>

<h3 id="Return_value">Return value</h3>

<p>O <code>intervalID</code> retornado é um número, non-zero valor que identifica o temporizador criado pela chamada do <code>setInterval()</code>; este valor pode ser passado para {{domxref("WindowOrWorkerGlobalScope.clearInterval()")}} afim de cancelar o timeout.</p>

<p>Isso pode ser útil, estar ciente que o <code>setInterval()</code> e {{domxref("WindowOrWorkerGlobalScope.setTimeout", "setTimeout()")}} compartilham o mesmo grupo de IDs, e que o <code>clearInterval()</code> e {{domxref("WindowOrWorkerGlobalScope.clearTimeout", "clearTimeout()")}} podem tecnicamente serem usados em conjunto. Para deixar claro, contudo, você deve sempre tentar evitar combina-los, afim de evitar confusão na manutenção do seu código.</p>

<div class="note"><strong>Note</strong>: The <code>delay</code> parameter is converted to a signed 32-bit integer. This effectively limits <code>delay</code> to 2147483647 ms, since it's specified as a signed integer in the IDL.</div>

<h2 id="Exemplos">Exemplos</h2>

<h3 id="Exemplo_1_Sintaxe_básica">Exemplo 1: Sintaxe básica</h3>

<p>O seguinte exemplo mostra a sintaxe básica do <code>setInterval()</code></p>

<pre class="brush:js">var intervalID = window.setInterval(myCallback, 500);

function myCallback() {
  // Your code here
}
</pre>

<h3 id="Exemplo_2_Alternando_duas_cores">Exemplo 2: Alternando duas cores</h3>

<p>O seguinte exemplo chama a função <code>flashtext()</code> uma vez por segundo até o botão de parar ser pressionado.</p>

<pre class="brush:html">&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
  &lt;meta charset="UTF-8" /&gt;
  &lt;title&gt;setInterval/clearInterval example&lt;/title&gt;

  &lt;script&gt;
    var nIntervId;

    function changeColor() {
      nIntervId = setInterval(flashText, 1000);
    }

    function flashText() {
      var oElem = document.getElementById('my_box');
      oElem.style.color = oElem.style.color == 'red' ? 'blue' : 'red';
      // oElem.style.color == 'red' ? 'blue' : 'red' is a ternary operator.
    }

    function stopTextColor() {
      clearInterval(nIntervId);
    }
  &lt;/script&gt;
&lt;/head&gt;

&lt;body onload="changeColor();"&gt;
  &lt;div id="my_box"&gt;
    &lt;p&gt;Hello World&lt;/p&gt;
  &lt;/div&gt;

  &lt;button onclick="stopTextColor();"&gt;Stop&lt;/button&gt;
&lt;/body&gt;
&lt;/html&gt;
</pre>

<h3 id="Exemplo_3_Simulação_de_máquina_de_escrever">Exemplo 3: Simulação de máquina de escrever</h3>

<p>O seguinte exemplo simula uma máquina de escrever primeiro limpando e digitando lentamente o conteúdo para  <code><a href="https://developer.mozilla.org/en-US/docs/DOM/NodeList">NodeList</a></code> que corresponde a um grupo especificado de seletores.</p>

<pre class="brush:html">&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;meta charset="UTF-8" /&gt;
&lt;title&gt;JavaScript Typewriter - MDN Example&lt;/title&gt;
&lt;script&gt;
  function Typewriter (sSelector, nRate) {

  function clean () {
    clearInterval(nIntervId);
    bTyping = false;
    bStart = true;
    oCurrent = null;
    aSheets.length = nIdx = 0;
  }

  function scroll (oSheet, nPos, bEraseAndStop) {
    if (!oSheet.hasOwnProperty('parts') || aMap.length &lt; nPos) { return true; }

    var oRel, bExit = false;

    if (aMap.length === nPos) { aMap.push(0); }

    while (aMap[nPos] &lt; oSheet.parts.length) {
      oRel = oSheet.parts[aMap[nPos]];

      scroll(oRel, nPos + 1, bEraseAndStop) ? aMap[nPos]++ : bExit = true;

      if (bEraseAndStop &amp;&amp; (oRel.ref.nodeType - 1 | 1) === 3 &amp;&amp; oRel.ref.nodeValue) {
        bExit = true;
        oCurrent = oRel.ref;
        sPart = oCurrent.nodeValue;
        oCurrent.nodeValue = '';
      }

      oSheet.ref.appendChild(oRel.ref);
      if (bExit) { return false; }
    }

    aMap.length--;
    return true;
  }

  function typewrite () {
    if (sPart.length === 0 &amp;&amp; scroll(aSheets[nIdx], 0, true) &amp;&amp; nIdx++ === aSheets.length - 1) { clean(); return; }

    oCurrent.nodeValue += sPart.charAt(0);
    sPart = sPart.slice(1);
  }

  function Sheet (oNode) {
    this.ref = oNode;
    if (!oNode.hasChildNodes()) { return; }
    this.parts = Array.prototype.slice.call(oNode.childNodes);

    for (var nChild = 0; nChild &lt; this.parts.length; nChild++) {
      oNode.removeChild(this.parts[nChild]);
      this.parts[nChild] = new Sheet(this.parts[nChild]);
    }
  }

  var
    nIntervId, oCurrent = null, bTyping = false, bStart = true,
    nIdx = 0, sPart = "", aSheets = [], aMap = [];

  this.rate = nRate || 100;

  this.play = function () {
    if (bTyping) { return; }
    if (bStart) {
      var aItems = document.querySelectorAll(sSelector);

      if (aItems.length === 0) { return; }
      for (var nItem = 0; nItem &lt; aItems.length; nItem++) {
        aSheets.push(new Sheet(aItems[nItem]));
        /* Uncomment the following line if you have previously hidden your elements via CSS: */
        // aItems[nItem].style.visibility = "visible";
      }

      bStart = false;
    }

    nIntervId = setInterval(typewrite, this.rate);
    bTyping = true;
  };

  this.pause = function () {
    clearInterval(nIntervId);
    bTyping = false;
  };

  this.terminate = function () {
    oCurrent.nodeValue += sPart;
    sPart = "";
    for (nIdx; nIdx &lt; aSheets.length; scroll(aSheets[nIdx++], 0, false));
    clean();
  };
}

/* usage: */
var oTWExample1 = new Typewriter(/* elements: */ '#article, h1, #info, #copyleft', /* frame rate (optional): */ 15);

/* default frame rate is 100: */
var oTWExample2 = new Typewriter('#controls');

/* you can also change the frame rate value modifying the "rate" property; for example: */
// oTWExample2.rate = 150;

onload = function () {
  oTWExample1.play();
  oTWExample2.play();
};
&lt;/script&gt;
&lt;style type="text/css"&gt;
span.intLink, a, a:visited {
  cursor: pointer;
  color: #000000;
  text-decoration: underline;
}

#info {
  width: 180px;
  height: 150px;
  float: right;
  background-color: #eeeeff;
  padding: 4px;
  overflow: auto;
  font-size: 12px;
  margin: 4px;
  border-radius: 5px;
  /* visibility: hidden; */
}
&lt;/style&gt;
&lt;/head&gt;

&lt;body&gt;

&lt;p id="copyleft" style="font-style: italic; font-size: 12px; text-align: center;"&gt;CopyLeft 2012 by &lt;a href="https://developer.mozilla.org/" target="_blank"&gt;Mozilla Developer Network&lt;/a&gt;&lt;/p&gt;
&lt;p id="controls" style="text-align: center;"&gt;[&amp;nbsp;&lt;span class="intLink" onclick="oTWExample1.play();"&gt;Play&lt;/span&gt; | &lt;span class="intLink" onclick="oTWExample1.pause();"&gt;Pause&lt;/span&gt; | &lt;span class="intLink" onclick="oTWExample1.terminate();"&gt;Terminate&lt;/span&gt;&amp;nbsp;]&lt;/p&gt;
&lt;div id="info"&gt;
Vivamus blandit massa ut metus mattis in fringilla lectus imperdiet. Proin ac ante a felis ornare vehicula. Fusce pellentesque lacus vitae eros convallis ut mollis magna pellentesque. Pellentesque placerat enim at lacus ultricies vitae facilisis nisi fringilla. In tincidunt tincidunt tincidunt.
&lt;/div&gt;
&lt;h1&gt;JavaScript Typewriter&lt;/h1&gt;

&lt;div id="article"&gt;
&lt;p&gt;Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nullam ultrices dolor ac dolor imperdiet ullamcorper. Suspendisse quam libero, luctus auctor mollis sed, malesuada condimentum magna. Quisque in ante tellus, in placerat est. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Donec a mi magna, quis mattis dolor. Etiam sit amet ligula quis urna auctor imperdiet nec faucibus ante. Mauris vel consectetur dolor. Nunc eget elit eget velit pulvinar fringilla consectetur aliquam purus. Curabitur convallis, justo posuere porta egestas, velit erat ornare tortor, non viverra justo diam eget arcu. Phasellus adipiscing fermentum nibh ac commodo. Nam turpis nunc, suscipit a hendrerit vitae, volutpat non ipsum.&lt;/p&gt;
&lt;form&gt;
&lt;p&gt;Phasellus ac nisl lorem: &lt;input type="text" /&gt;&lt;br /&gt;
&lt;textarea style="width: 400px; height: 200px;"&gt;Nullam commodo suscipit lacus non aliquet. Phasellus ac nisl lorem, sed facilisis ligula. Nam cursus lobortis placerat. Sed dui nisi, elementum eu sodales ac, placerat sit amet mauris. Pellentesque dapibus tellus ut ipsum aliquam eu auctor dui vehicula. Quisque ultrices laoreet erat, at ultrices tortor sodales non. Sed venenatis luctus magna, ultricies ultricies nunc fringilla eget. Praesent scelerisque urna vitae nibh tristique varius consequat neque luctus. Integer ornare, erat a porta tempus, velit justo fermentum elit, a fermentum metus nisi eu ipsum. Vivamus eget augue vel dui viverra adipiscing congue ut massa. Praesent vitae eros erat, pulvinar laoreet magna. Maecenas vestibulum mollis nunc in posuere. Pellentesque sit amet metus a turpis lobortis tempor eu vel tortor. Cras sodales eleifend interdum.&lt;/textarea&gt;&lt;/p&gt;
&lt;p&gt;&lt;input type="submit" value="Send" /&gt;
&lt;/form&gt;
&lt;p&gt;Duis lobortis sapien quis nisl luctus porttitor. In tempor semper libero, eu tincidunt dolor eleifend sit amet. Ut nec velit in dolor tincidunt rhoncus non non diam. Morbi auctor ornare orci, non euismod felis gravida nec. Curabitur elementum nisi a eros rutrum nec blandit diam placerat. Aenean tincidunt risus ut nisi consectetur cursus. Ut vitae quam elit. Donec dignissim est in quam tempor consequat. Aliquam aliquam diam non felis convallis suscipit. Nulla facilisi. Donec lacus risus, dignissim et fringilla et, egestas vel eros. Duis malesuada accumsan dui, at fringilla mauris bibStartum quis. Cras adipiscing ultricies fermentum. Praesent bibStartum condimentum feugiat.&lt;/p&gt;
&lt;p&gt;Nam faucibus, ligula eu fringilla pulvinar, lectus tellus iaculis nunc, vitae scelerisque metus leo non metus. Proin mattis lobortis lobortis. Quisque accumsan faucibus erat, vel varius tortor ultricies ac. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed nec libero nunc. Nullam tortor nunc, elementum a consectetur et, ultrices eu orci. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque a nisl eu sem vehicula egestas.&lt;/p&gt;
&lt;/div&gt;
&lt;/body&gt;
&lt;/html&gt;</pre>

<p><a href="/files/3997/typewriter.html">View this demo in action</a>. See also: <a href="/en-US/docs/DOM/window.clearInterval"><code>clearInterval()</code></a>.</p>

<h2 id="Argumentos_callback">Argumentos callback</h2>

<p>Como já foi discutido, Internet Explorer 9 e versões anteriores não suportam passar argumentos para a função callback em ambos <code>setTimeout()</code> ou <code>setInterval()</code>. O seguinte código <strong>IE-specific</strong> demonstra um método para superar esta limitação. Para usar, apenas adicione o seguinte código no topo do seu script.</p>

<pre class="brush:js">/*\
|*|
|*|  IE-specific polyfill that enables the passage of arbitrary arguments to the
|*|  callback functions of javascript timers (HTML5 standard syntax).
|*|
|*|  https://developer.mozilla.org/en-US/docs/Web/API/window.setInterval
|*|  https://developer.mozilla.org/User:fusionchess
|*|
|*|  Syntax:
|*|  var timeoutID = window.setTimeout(func, delay[, param1, param2, ...]);
|*|  var timeoutID = window.setTimeout(code, delay);
|*|  var intervalID = window.setInterval(func, delay[, param1, param2, ...]);
|*|  var intervalID = window.setInterval(code, delay);
|*|
\*/

if (document.all &amp;&amp; !window.setTimeout.isPolyfill) {
  var __nativeST__ = window.setTimeout;
  window.setTimeout = function (vCallback, nDelay /*, argumentToPass1, argumentToPass2, etc. */) {
    var aArgs = Array.prototype.slice.call(arguments, 2);
    return __nativeST__(vCallback instanceof Function ? function () {
      vCallback.apply(null, aArgs);
    } : vCallback, nDelay);
  };
  window.setTimeout.isPolyfill = true;
}

if (document.all &amp;&amp; !window.setInterval.isPolyfill) {
  var __nativeSI__ = window.setInterval;
  window.setInterval = function (vCallback, nDelay /*, argumentToPass1, argumentToPass2, etc. */) {
    var aArgs = Array.prototype.slice.call(arguments, 2);
    return __nativeSI__(vCallback instanceof Function ? function () {
      vCallback.apply(null, aArgs);
    } : vCallback, nDelay);
  };
  window.setInterval.isPolyfill = true;
}
</pre>

<p>Outra possibilidade é uso uma função anônima para chama o callback, apesar de que esta solução seja um pouco mais pesada. Exemplo:</p>

<pre class="brush:js">var intervalID = setInterval(function() { myFunc('one', 'two', 'three'); }, 1000);</pre>

<p>Outra possibilidade é usar o <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind">function's bind</a>. Exemplo:</p>

<pre class="brush:js">var intervalID = setInterval(function(arg1) {}.bind(undefined, 10), 1000);</pre>

<h3>Abas inativas</h3>

<p>Iniciado no Gecko 5.0 {{geckoRelease("5.0")}}, intervalos são fixados para disparar não mais do que uma vez por segundo em abas inativas.</p>

<h2 id="O_problema_do_this">O problema do "<a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this"><code>this</code></a>"</h2>

<p>Quando você passa um método para <code>setInterval()</code> ou qualquer outra função, ela é chamada com o valor do <code><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this">this</a></code> errado. Este problema é explicado em detalhes em <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this#As_an_object_method">JavaScript reference</a>.</p>

<h3 id="Explicação">Explicação</h3>

<p>O código executado pelo <code>setInterval()</code> roda em um contexto de execução separado da função que foi chamada. Como uma consequência, o <code><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this">this</a></code> da função chamada, é setado como o objeto <code>window</code> (ou <code>global</code>), esse não é o mesmo valor do <code>this</code> para a função chamada em setTimeout. veja o seguinte exemplo (que usa <code>setTimeout()</code> ao invés de <code>setInterval()</code> - o problema segue para ambos os temporizadores)</p>

<pre class="brush:js">myArray = ['zero', 'one', 'two'];

myArray.myMethod = function (sProperty) {
    alert(arguments.length &gt; 0 ? this[sProperty] : this);
};

myArray.myMethod(); // prints "zero,one,two"
myArray.myMethod(1); // prints "one"
setTimeout(myArray.myMethod, 1000); // prints "[object Window]" after 1 second
setTimeout(myArray.myMethod, 1500, "1"); // prints "undefined" after 1,5 seconds
// passing the 'this' object with .call won't work
// because this will change the value of this inside setTimeout itself
// while we want to change the value of this inside myArray.myMethod
// in fact, it will be an error because setTimeout code expects this to be the window object:
setTimeout.call(myArray, myArray.myMethod, 2000); // error: "NS_ERROR_XPC_BAD_OP_ON_WN_PROTO: Illegal operation on WrappedNative prototype object"
setTimeout.call(myArray, myArray.myMethod, 2500, 2); // same error
</pre>

<p> </p>

<p>Como você pode ver, não há maneiras de passar o objeto <code>this</code> para a função callback.</p>

<p> </p>

<h3 id="Uma_possível_solução">Uma possível solução</h3>

<p>Um possível caminho para resolver o problema do <code>this</code>, é sobreescrever as duas funções globais nativas <code>setTimeout()</code> ou <code>setInterval()</code> com duas <em>non-native</em> que permitem sua invocação através do método <code><a href="https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Function/call">Function.prototype.call</a></code>. O seguinte exemplo mostra a possível substituição.</p>

<pre class="brush:js">// Enable the passage of the 'this' object through the JavaScript timers

var __nativeST__ = window.setTimeout, __nativeSI__ = window.setInterval;

window.setTimeout = function (vCallback, nDelay /*, argumentToPass1, argumentToPass2, etc. */) {
  var oThis = this, aArgs = Array.prototype.slice.call(arguments, 2);
  return __nativeST__(vCallback instanceof Function ? function () {
    vCallback.apply(oThis, aArgs);
  } : vCallback, nDelay);
};

window.setInterval = function (vCallback, nDelay /*, argumentToPass1, argumentToPass2, etc. */) {
  var oThis = this, aArgs = Array.prototype.slice.call(arguments, 2);
  return __nativeSI__(vCallback instanceof Function ? function () {
    vCallback.apply(oThis, aArgs);
  } : vCallback, nDelay);
};</pre>

<div class="note">These two replacements also enable the HTML5 standard passage of arbitrary arguments to the callback functions of timers in IE. So they can be used as <em>non-standard-compliant</em> polyfills also. See the <a href="#Callback_arguments">callback arguments paragraph</a> for a <em>standard-compliant</em> polyfill.</div>

<p>Teste da nova implementação:</p>

<pre class="brush:js">myArray = ['zero', 'one', 'two'];

myArray.myMethod = function (sProperty) {
    alert(arguments.length &gt; 0 ? this[sProperty] : this);
};

setTimeout(alert, 1500, 'Hello world!'); // the standard use of setTimeout and setInterval is preserved, but...
setTimeout.call(myArray, myArray.myMethod, 2000); // prints "zero,one,two" after 2 seconds
setTimeout.call(myArray, myArray.myMethod, 2500, 2); // prints "two" after 2,5 seconds
</pre>

<p>Outra, mais complexa, solução para o problema do <code>this</code> é <a href="https://developer.mozilla.org/pt-BR/docs/Web/API/WindowOrWorkerGlobalScope/setInterval$edit#A_little_framework">the following framework</a>.</p>

<div class="note">JavaScript 1.8.5 introduces the <code><a href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind">Function.prototype.bind()</a></code> method, which lets you specify the value that should be used as <code>this</code> for all calls to a given function. This lets you easily bypass problems where it's unclear what this will be, depending on the context from which your function was called. Also, ES2015 supports <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions">arrow functions</a>, with lexical this allowing us to write setInterval( () =&gt; this.myMethod) if we're inside myArray method.</div>

<h2 id="MiniDaemon_-_A_framework_for_managing_timers">MiniDaemon - A framework for managing timers</h2>

<p>In pages requiring many timers, it can often be difficult to keep track of all of the running timer events. One approach to solving this problem is to store information about the state of a timer in an object. Following is a minimal example of such an abstraction. The constructor architecture explicitly avoids the use of closures. It also offers an alternative way to pass the <a href="/en-US/docs/Web/JavaScript/Reference/Operators/this"><code>this</code></a> object to the callback function (see <a href="#The_.22this.22_problem">The "this" problem</a> for details). The following code is also <a href="https://github.com/madmurphy/minidaemon.js">available on GitHub</a>.</p>

<div class="note">For a more complex but still modular version of it (<code><em>Daemon</em></code>) see <a href="/en-US/Add-ons/Code_snippets/Timers/Daemons">JavaScript Daemons Management</a>. This more complex version is nothing but a big and scalable collection of methods for the <code><em>Daemon</em></code> constructor. However, the <code><em>Daemon</em></code> constructor itself is nothing but a clone of <code><em>MiniDaemon</em></code> with an added support for <em>init</em> and <em>onstart</em> functions declarable during the instantiation of the <code><em>daemon</em></code>. <strong>So the <code><em>MiniDaemon</em></code> framework remains the recommended way for simple animations</strong>, because <code><em>Daemon</em></code> without its collection of methods is essentially a clone of it.</div>

<h3 id="minidaemon.js">minidaemon.js</h3>

<pre class="brush:js">/*\
|*|
|*|  :: MiniDaemon ::
|*|
|*|  Revision #2 - September 26, 2014
|*|
|*|  https://developer.mozilla.org/en-US/docs/Web/API/window.setInterval
|*|  https://developer.mozilla.org/User:fusionchess
|*|  https://github.com/madmurphy/minidaemon.js
|*|
|*|  This framework is released under the GNU Lesser General Public License, version 3 or later.
|*|  http://www.gnu.org/licenses/lgpl-3.0.html
|*|
\*/

function MiniDaemon (oOwner, fTask, nRate, nLen) {
  if (!(this &amp;&amp; this instanceof MiniDaemon)) { return; }
  if (arguments.length &lt; 2) { throw new TypeError('MiniDaemon - not enough arguments'); }
  if (oOwner) { this.owner = oOwner; }
  this.task = fTask;
  if (isFinite(nRate) &amp;&amp; nRate &gt; 0) { this.rate = Math.floor(nRate); }
  if (nLen &gt; 0) { this.length = Math.floor(nLen); }
}

MiniDaemon.prototype.owner = null;
MiniDaemon.prototype.task = null;
MiniDaemon.prototype.rate = 100;
MiniDaemon.prototype.length = Infinity;

  /* These properties should be read-only */

MiniDaemon.prototype.SESSION = -1;
MiniDaemon.prototype.INDEX = 0;
MiniDaemon.prototype.PAUSED = true;
MiniDaemon.prototype.BACKW = true;

  /* Global methods */

MiniDaemon.forceCall = function (oDmn) {
  oDmn.INDEX += oDmn.BACKW ? -1 : 1;
  if (oDmn.task.call(oDmn.owner, oDmn.INDEX, oDmn.length, oDmn.BACKW) === false || oDmn.isAtEnd()) { oDmn.pause(); return false; }
  return true;
};

  /* Instances methods */

MiniDaemon.prototype.isAtEnd = function () {
  return this.BACKW ? isFinite(this.length) &amp;&amp; this.INDEX &lt; 1 : this.INDEX + 1 &gt; this.length;
};

MiniDaemon.prototype.synchronize = function () {
  if (this.PAUSED) { return; }
  clearInterval(this.SESSION);
  this.SESSION = setInterval(MiniDaemon.forceCall, this.rate, this);
};

MiniDaemon.prototype.pause = function () {
  clearInterval(this.SESSION);
  this.PAUSED = true;
};

MiniDaemon.prototype.start = function (bReverse) {
  var bBackw = Boolean(bReverse);
  if (this.BACKW === bBackw &amp;&amp; (this.isAtEnd() || !this.PAUSED)) { return; }
  this.BACKW = bBackw;
  this.PAUSED = false;
  this.synchronize();
};
</pre>

<div class="note">MiniDaemon passes arguments to the callback function. If you want to work on it with browsers that natively do not support this feature, use one of the methods proposed above.</div>

<h3 id="Syntax">Syntax</h3>

<p><code>var myDaemon = new MiniDaemon(<em>thisObject</em>, <em>callback</em>[</code><code>, <em>rate</em></code><code>[, <em>length</em>]]);</code></p>

<h3 id="Description">Description</h3>

<p>Returns a JavaScript <a href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object"><code>Object</code></a> containing all information needed by an animation (like the <a href="/en-US/docs/Web/JavaScript/Reference/Operators/this"><code>this</code></a> object, the callback function, the length, the frame-rate).</p>

<h4 id="Parameters">Parameters</h4>

<dl>
 <dt><code>thisObject</code></dt>
 <dd>The <a href="/en-US/docs/Web/JavaScript/Reference/Operators/this"><code>this</code></a> object on which the <em>callback</em> function is called. It can be an <a href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object"><code>object</code></a> or <code>null</code>.</dd>
 <dt><code>callback</code></dt>
 <dd>The function that is repeatedly invoked . <strong>It is called with three parameters</strong>: <em>index</em> (the iterative index of each invocation), <em>length</em> (the number of total invocations assigned to the <em>daemon</em> - finite or <a href="/en-US/docs/JavaScript/Reference/Global_Objects/Infinity"><code>Infinity</code></a>) and <em>backwards</em> (a boolean expressing whether the <em>index</em> is increasing or decreasing). It is something like <em>callback</em>.call(<em>thisObject</em>, <em>index</em>, <em>length</em>, <em>backwards</em>). <strong>If the callback function returns a <code>false</code> value the <em>daemon</em> is paused</strong>.</dd>
 <dt><code>rate (optional)</code></dt>
 <dd>The time lapse (in number of milliseconds) between each invocation. The default value is 100.</dd>
 <dt><code>length (optional)</code></dt>
 <dd>The total number of invocations. It can be a positive integer or <a href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/Infinity"><code>Infinity</code></a>. The default value is <code>Infinity</code>.</dd>
</dl>

<h4 id="MiniDaemon_instances_properties"><code>MiniDaemon</code> instances properties</h4>

<dl>
 <dt><code>myDaemon.owner</code></dt>
 <dd>The <a href="/en-US/docs/Web/JavaScript/Reference/Operators/this"><code>this</code></a> object on which is executed the daemon (read/write). It can be an <a href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object"><code>object</code></a> or <code>null</code>.</dd>
 <dt><code>myDaemon.task</code></dt>
 <dd>The function that is repeatedly invoked (read/write). It is called with three arguments: <em>index</em> (the iterative index of each invocation), <em>length</em> (the number of total invocations assigned to the daemon - finite or <a href="/en-US/docs/JavaScript/Reference/Global_Objects/Infinity"><code>Infinity</code></a>) and backwards (a boolean expressing whether the <em>index</em> is decreasing or not) – see above. If the <code>myDaemon.task</code> function returns a <code>false</code> value the <em>daemon</em> is paused.</dd>
 <dt><code>myDaemon.rate</code></dt>
 <dd>The time lapse (in number of milliseconds) between each invocation (read/write).</dd>
 <dt><code>myDaemon.length</code></dt>
 <dd>The total number of invocations. It can be a positive integer or <a href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/Infinity"><code>Infinity</code></a> (read/write).</dd>
</dl>

<h4 id="MiniDaemon_instances_methods"><code>MiniDaemon</code> instances methods</h4>

<dl>
 <dt><code>myDaemon.isAtEnd()</code></dt>
 <dd>Returns a boolean expressing whether the <em>daemon</em> is at the start/end position or not.</dd>
 <dt><code>myDaemon.synchronize()</code></dt>
 <dd>Synchronize the timer of a started daemon with the time of its invocation.</dd>
 <dt><code>myDaemon.pause()</code></dt>
 <dd>Pauses the daemon.</dd>
 <dt><code>myDaemon.start([<em>reverse</em>])</code></dt>
 <dd>Starts the daemon forward (<em>index</em> of each invocation increasing) or backwards (<em>index</em> decreasing).</dd>
</dl>

<h4 id="MiniDaemon_global_object_methods"><code>MiniDaemon</code> global object methods</h4>

<dl>
 <dt><code>MiniDaemon.forceCall(<em>minidaemon</em>)</code></dt>
 <dd>Forces a single callback to the <code><em>minidaemon</em>.task</code> function regardless of the fact that the end has been reached or not. In any case the internal <code>INDEX</code> property is increased/decreased (depending on the actual direction of the process).</dd>
</dl>

<h3 id="Example_usage">Example usage</h3>

<p>Your HTML page:</p>

<pre class="brush:html">&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
  &lt;meta charset="UTF-8" /&gt;
  &lt;title&gt;MiniDaemin Example - MDN&lt;/title&gt;
  &lt;script type="text/javascript" src="minidaemon.js"&gt;&lt;/script&gt;
  &lt;style type="text/css"&gt;
    #sample_div {
      visibility: hidden;
    }
  &lt;/style&gt;
&lt;/head&gt;

&lt;body&gt;
  &lt;p&gt;
    &lt;input type="button" onclick="fadeInOut.start(false /* optional */);" value="fade in" /&gt;
    &lt;input type="button" onclick="fadeInOut.start(true);" value="fade out"&gt;
    &lt;input type="button" onclick="fadeInOut.pause();" value="pause" /&gt;
  &lt;/p&gt;

  &lt;div id="sample_div"&gt;Some text here&lt;/div&gt;

  &lt;script type="text/javascript"&gt;
    function opacity (nIndex, nLength, bBackwards) {
      this.style.opacity = nIndex / nLength;
      if (bBackwards ? nIndex === 0 : nIndex === 1) {
        this.style.visibility = bBackwards ? 'hidden' : 'visible';
      }
    }

    var fadeInOut = new MiniDaemon(document.getElementById('sample_div'), opacity, 300, 8);
  &lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;</pre>

<p><a href="/files/3995/minidaemon_example.html" title="MiniDaemon Example">View this example in action</a></p>

<h2 id="Notes">Notes</h2>

<p>The <code>setInterval()</code> function is commonly used to set a delay for functions that are executed again and again, such as animations.</p>

<p>You can cancel the interval using {{domxref("WindowOrWorkerGlobalScope.clearInterval()")}}.</p>

<p>If you wish to have your function called <em>once</em> after the specified delay, use {{domxref("WindowOrWorkerGlobalScope.setTimeout()")}}.</p>

<h3 id="Ensure_that_execution_duration_is_shorter_than_interval_frequency">Ensure that execution duration is shorter than interval frequency</h3>

<p>If there is a possibility that your logic could take longer to execute than the interval time, it is recommended that you recursively call a named function using {{domxref("WindowOrWorkerGlobalScope.setTimeout")}}. For example, if using <code>setInterval</code> to poll a remote server every 5 seconds, network latency, an unresponsive server, and a host of other issues could prevent the request from completing in its allotted time. As such, you may find yourself with queued up XHR requests that won't necessarily return in order.</p>

<p>In these cases, a recursive <code>setTimeout()</code> pattern is preferred:</p>

<pre class="brush:js">(function loop(){
   setTimeout(function() {
      // Your logic here

      loop();
  }, delay);
})();
</pre>

<p>In the above snippet, a named function <code>loop()</code> is declared and is immediately executed. <code>loop()</code> is recursively called inside <code>setTimeout()</code> after the logic has completed executing. While this pattern does not guarantee execution on a fixed interval, it does guarantee that the previous interval has completed before recursing.</p>

<h3 id="Throttling_of_intervals">Throttling of intervals</h3>

<p><code>setInterval()</code> is subject to the same throttling restrictions in Firefox as {{domxref("WindowOrWorkerGlobalScope.setTimeout","setTimeout()")}}; see <a href="/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout#Reasons_for_delays_longer_than_specified">Reasons for delays longer than specified</a>.</p>

<h2 id="Specifications">Specifications</h2>

<table class="standard-table">
 <tbody>
  <tr>
   <th>Specification</th>
   <th>Status</th>
   <th>Comment</th>
  </tr>
  <tr>
   <td>{{SpecName('HTML WHATWG', 'webappapis.html#dom-setinterval', 'WindowOrWorkerGlobalScope.setInterval()')}}</td>
   <td>{{Spec2("HTML WHATWG")}}</td>
   <td>Method moved to the <code>WindowOrWorkerGlobalScope</code> mixin in the latest spec.</td>
  </tr>
  <tr>
   <td>{{SpecName("HTML WHATWG", "webappapis.html#dom-setinterval", "WindowTimers.setInterval()")}}</td>
   <td>{{Spec2("HTML WHATWG")}}</td>
   <td>Initial definition (DOM Level 0)</td>
  </tr>
 </tbody>
</table>

<h2 id="Browser_compatibility">Compatibilidade com navegadores</h2>

<div>


<p>{{Compat("api.WindowOrWorkerGlobalScope.setInterval")}}</p>
</div>

<h2 id="See_also">See also</h2>

<ul>
 <li><a href="/en-US/Add-ons/Code_snippets/Timers">JavaScript timers</a></li>
 <li>{{domxref("WindowOrWorkerGlobalScope.setTimeout")}}</li>
 <li>{{domxref("WindowOrWorkerGlobalScope.clearTimeout")}}</li>
 <li>{{domxref("WindowOrWorkerGlobalScope.clearInterval")}}</li>
 <li>{{domxref("window.requestAnimationFrame")}}</li>
 <li><a href="/en-US/Add-ons/Code_snippets/Timers/Daemons"><em>Daemons</em> management</a></li>
</ul>