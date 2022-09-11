---
title: flex
slug: Web/CSS/flex
translation_of: Web/CSS/flex
---
<div>{{CSSRef}}</div>

<p>A propriedade flex do CSS, define como um ítem será posicionado para no espaço disponível dentro de seu container.</p>

<div>{{EmbedInteractiveExample("pages/css/flex.html")}}</div>



<h2 id="Propriedades">Propriedades</h2>

<p>Esta propriedade é uma abreviação das seguintes propriedades CSS:</p>

<ul>
 <li>{{cssxref("flex-grow")}}</li>
 <li>{{cssxref("flex-shrink")}}</li>
 <li>{{cssxref("flex-basis")}}</li>
</ul>

<div id="flex">
<pre class="hidden brush: html">&lt;div class="flex-container"&gt;
  &lt;div class="item auto"&gt;auto&lt;/div&gt;
  &lt;div class="item auto"&gt;auto&lt;/div&gt;
  &lt;div class="item auto"&gt;auto&lt;/div&gt;
&lt;/div&gt;

&lt;div class="flex-container"&gt;
  &lt;div class="item auto"&gt;auto&lt;/div&gt;
  &lt;div class="item initial"&gt;initial&lt;/div&gt;
  &lt;div class="item initial"&gt;initial&lt;/div&gt;
&lt;/div&gt;

&lt;div class="flex-container"&gt;
  &lt;div class="item auto"&gt;auto&lt;/div&gt;
  &lt;div class="item auto"&gt;auto&lt;/div&gt;
  &lt;div class="item none"&gt;none&lt;/div&gt;
&lt;/div&gt;

&lt;div class="flex-container"&gt;
  &lt;div class="item initial"&gt;initial&lt;/div&gt;
  &lt;div class="item none"&gt;none&lt;/div&gt;
  &lt;div class="item none"&gt;none&lt;/div&gt;
&lt;/div&gt;

&lt;div class="flex-container"&gt;
  &lt;div class="item four"&gt;4&lt;/div&gt;
  &lt;div class="item two"&gt;2&lt;/div&gt;
  &lt;div class="item one"&gt;1&lt;/div&gt;
&lt;/div&gt;
</pre>

<pre class="hidden brush: css">* {
  box-sizing: border-box;
}

.flex-container {
  background-color: #F4F7F8;
  resize: horizontal;
  overflow: hidden;
  display: flex;
  margin: 1em;
}

.item {
  margin: 1em;
  padding: 0.5em;
  width: 110px;
  min-width: 0;
  background-color: #1B5385;
  color: white;
  font-family: monospace;
  font-size: 13px;
}

.initial {
  flex: initial;
}

.auto {
  flex: auto;
}

.none {
  flex: none;
}

.four {
  flex: 4;
}

.two {
  flex: 2;
}

.one {
  flex: 1;
}
</pre>

<p>{{EmbedLiveSample("flex", 1200, 370, "", "", "example-outcome-frame")}}</p>

<p>By default flex items don't shrink below their minimum content size. To change this, set the item's {{cssxref("min-width")}} or {{cssxref("min-height")}}.</p>
</div>

<h2 id="Syntax" name="Syntax">Sintaxe</h2>

<pre class="brush:css">/* Propriedades principais */
flex: auto;
flex: initial;
flex: none;

/* Valor único, sem unidade: flex-grow */
flex: 2;

/* Valor único, unidade width/height: flex-basis */
flex: 10em;
flex: 30%;
flex: min-content;

/* Dois valores: flex-grow | flex-basis */
flex: 1 30px;

/* Dois valores: flex-grow | flex-shrink */
flex: 2 2;

/* Três valores: flex-grow | flex-shrink | flex-basis */
flex: 2 2 10%;

/* Valores globais */
flex: inherit;
flex: initial;
flex: unset;
</pre>

<p>The <code>flex</code> property may be specified using one, two, or three values.</p>

<ul>
 <li><strong>One-value syntax</strong>: the value must be one of:

  <ul>
   <li>a <code>&lt;number&gt;</code>: In this case it is interpreted as <code>flex: &lt;number&gt; 1 0</code>; the <code><a href="#&lt;'flex-shrink'>">&lt;flex-shrink&gt;</a></code> value is assumed to be 1 and the <code><a href="#&lt;'flex-basis'>">&lt;flex-basis&gt;</a></code> value is assumed to be <code>0</code>.</li>
   <li>one of the keywords: <code><a href="#none">none</a></code>, <code><a href="#auto">auto</a></code>, or <code>initial</code>.</li>
  </ul>
 </li>
 <li><strong>Two-value syntax</strong>: the first value must be a {{cssxref("&lt;number&gt;")}} and it is interpreted as <code><a href="#&lt;'flex-grow'>">&lt;flex-grow&gt;</a></code>. The second value must be one of:
  <ul>
   <li>a {{cssxref("&lt;number&gt;")}}: then it is interpreted as <code><a href="#&lt;'flex-shrink'>">&lt;flex-shrink&gt;</a></code>.</li>
   <li>a valid value for {{cssxref("width")}}: then it is interpreted as <code><a href="#&lt;'flex-basis'>">&lt;flex-basis&gt;</a></code>.</li>
  </ul>
 </li>
 <li><strong>Three-value syntax:</strong> the values must be in the following order:
  <ol>
   <li>a {{cssxref("&lt;number&gt;")}} for <code><a href="#&lt;'flex-grow'>">&lt;flex-grow&gt;</a></code>.</li>
   <li>a {{cssxref("&lt;number&gt;")}} for <code><a href="#&lt;'flex-grow'>">&lt;flex-shrink&gt;</a></code>.</li>
   <li>a valid value for {{cssxref("width")}} for <code><a href="#&lt;'flex-basis'>">&lt;flex-basis&gt;</a></code>.</li>
  </ol>
 </li>
</ul>

<h3 id="Values" name="Values">Values</h3>

<dl>
 <dt><code>initial</code></dt>
 <dd>The item is sized according to its <code>width</code> and <code>height</code> properties. It shrinks to its minimum size to fit the container, but does not grow to absorb any extra free space in the flex container. This is equivalent to setting "<code>flex: 0 1 auto</code>".</dd>
 <dt><a id="auto" name="auto"><code>auto</code></a></dt>
 <dd>The item is sized according to its <code>width</code> and <code>height</code> properties, but grows to absorb any extra free space in the flex container, and shrinks to its minimum size to fit the container. This is equivalent to setting "<code>flex: 1 1 auto</code>".</dd>
 <dt><a id="none" name="none"><code>none</code></a></dt>
 <dd>The item is sized according to its <code>width</code> and <code>height</code> properties. It is fully inflexible: it neither shrinks nor grows in relation to the flex container. This is equivalent to setting "<code>flex: 0 0 auto</code>".</dd>
 <dt><a id="&lt;'flex-grow'>" name="&lt;'flex-grow'>"><code>&lt;'flex-grow'&gt;</code></a></dt>
 <dd>Defines the {{cssxref("flex-grow")}} of the flex item. Negative values are considered invalid. Defaults to <code>1</code> when omitted.</dd>
 <dt><a id="&lt;'flex-shrink'>" name="&lt;'flex-shrink'>"><code id="&lt;'flex-shrink'>">&lt;'flex-shrink'&gt;</code></a></dt>
 <dd>Defines the {{cssxref("flex-shrink")}} of the flex item. Negative values are considered invalid. Defaults to <code>1</code> when omitted.</dd>
 <dt><a id="&lt;'flex-basis'>" name="&lt;'flex-basis'>"><code id="&lt;'flex-basis'>">&lt;'flex-basis'&gt;</code></a></dt>
 <dd>Defines the {{cssxref("flex-basis")}} of the flex item. A preferred size of <code>0</code> must have a unit to avoid being interpreted as a flexibility. Defaults to <code>0</code> when omitted.</dd>
</dl>

<h3 id="Formal_syntax">Formal syntax</h3>

{{csssyntax}}

<h2 id="Example">Example</h2>

<pre class="brush: css">#flex-container {
  display: flex;
  flex-direction: row;
}

#flex-container &gt; .flex-item {
  flex: auto;
}

#flex-container &gt; .raw-item {
  width: 5rem;
}
</pre>

<pre class="brush: html">&lt;div id="flex-container"&gt;
  &lt;div class="flex-item" id="flex"&gt;Flex box (click to toggle raw box)&lt;/div&gt;
  &lt;div class="raw-item" id="raw"&gt;Raw box&lt;/div&gt;
&lt;/div&gt;
</pre>

<div class="hidden">
<pre class="brush: js">var flex = document.getElementById("flex");
var raw = document.getElementById("raw");
flex.addEventListener("click", function() {
  raw.style.display = raw.style.display == "none" ? "block" : "none";
});
</pre>

<pre class="brush: css">#flex-container {
  width: 100%;
  font-family: Consolas, Arial, sans-serif;
}

#flex-container &gt; div {
  border: 1px solid #f00;
  padding: 1rem;
}

#flex-container &gt; .raw-item {
  border: 1px solid #000;
}
</pre>
</div>

<h3 id="Result">Result</h3>

<p>{{EmbedLiveSample('Example','100%','60')}}</p>

<h2 id="Specifications" name="Specifications">Specifications</h2>

<table class="standard-table">
 <thead>
  <tr>
   <th scope="col">Specification</th>
   <th scope="col">Status</th>
   <th scope="col">Comment</th>
  </tr>
 </thead>
 <tbody>
  <tr>
   <td>{{SpecName('CSS3 Flexbox', '#flex-property', 'flex')}}</td>
   <td>{{Spec2('CSS3 Flexbox')}}</td>
   <td>Initial definition</td>
  </tr>
 </tbody>
</table>

<p>{{cssinfo}}</p>

<h2 id="Browser_compatibility">Compatibilidade com navegadores</h2>

<p>{{Compat("css.properties.flex")}}</p>

<h2 id="See_also" name="See_also">See also</h2>

<ul>
 <li>CSS Flexbox Guide: <em><a href="/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Basic_Concepts_of_Flexbox">Basic Concepts of Flexbox</a></em></li>
 <li>CSS Flexbox Guide: <em><a href="/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Controlling_Ratios_of_Flex_Items_Along_the_Main_Ax">Controlling Ratios of flex items along the main axis</a></em></li>
</ul>