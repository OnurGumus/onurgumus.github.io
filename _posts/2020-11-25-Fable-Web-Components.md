

<table class="pre"><tr><td class="lines"><pre class="fssnip"><span class="l">1: </span>
<span class="l">2: </span>
<span class="l">3: </span>
<span class="l">4: </span>
</pre></td>
<td class="snippet"><pre class="fssnip highlighted"><code lang="fsharp">    <span class="c">(** # *F# literate* in action *)</span>
    <span class="k">let</span> <span onmouseout="hideTip(event, '31', 1)" onmouseover="showTip(event, '31', 1)" class="fn">convert</span> <span onmouseout="hideTip(event, '32', 2)" onmouseover="showTip(event, '32', 2)" class="id">i</span> <span class="o">=</span> <span onmouseout="hideTip(event, '32', 3)" onmouseover="showTip(event, '32', 3)" class="fn">i</span><span class="pn">.</span><span onmouseout="hideTip(event, '33', 4)" onmouseover="showTip(event, '33', 4)" class="id">ToString</span><span class="pn">(</span><span class="pn">)</span>
    <span onmouseout="hideTip(event, '34', 5)" onmouseover="showTip(event, '34', 5)" class="fn">printfn</span> <span class="s">&quot;</span><span class="pf">%s</span><span class="s">&quot;</span> <span class="pn">(</span><span onmouseout="hideTip(event, '31', 6)" onmouseover="showTip(event, '31', 6)" class="fn">convert</span> <span class="n">5</span><span class="pn">)</span>
    
</code></pre></td>
</tr>
</table>
<div class="tip" id="31">val convert : i:&#39;a -&gt; string</div>
<div class="tip" id="32">val i : &#39;a</div>
<div class="tip" id="33">System.Object.ToString() : string</div>
<div class="tip" id="34">val printfn : format:Printf.TextWriterFormat&lt;&#39;T&gt; -&gt; &#39;T</div>

<h1>code execution</h1>
<table class="pre"><tr><td class="lines"><pre class="fssnip"><span class="l">1: </span>
<span class="l">2: </span>
</pre></td>
<td class="snippet"><pre class="fssnip highlighted"><code lang="fsharp"><span class="k">let</span> <span onmouseout="hideTip(event, '11', 1)" onmouseover="showTip(event, '11', 1)" class="fn">square</span> <span onmouseout="hideTip(event, '12', 2)" onmouseover="showTip(event, '12', 2)" class="id">x</span> <span class="o">=</span> <span onmouseout="hideTip(event, '12', 3)" onmouseover="showTip(event, '12', 3)" class="id">x</span> <span class="o">*</span> <span onmouseout="hideTip(event, '12', 4)" onmouseover="showTip(event, '12', 4)" class="id">x</span>
<span class="k">let</span> <span onmouseout="hideTip(event, '13', 5)" onmouseover="showTip(event, '13', 5)" class="id">v</span> <span class="o">=</span> <span onmouseout="hideTip(event, '11', 6)" onmouseover="showTip(event, '11', 6)" class="fn">square</span> <span class="n">3</span>
</code></pre></td>
</tr>
</table>
<p>the value is:</p>
<table class="pre"><tr><td><pre><code>9</code></pre></td></tr></table>
<div class="tip" id="11">val square : x:int -&gt; int</div>
<div class="tip" id="12">val x : int</div>
<div class="tip" id="13">val v : int</div>

<h1>printing</h1>
<table class="pre"><tr><td class="lines"><pre class="fssnip"><span class="l">1: </span>
</pre></td>
<td class="snippet"><pre class="fssnip highlighted"><code lang="fsharp"><span class="k">let</span> <span onmouseout="hideTip(event, '21', 1)" onmouseover="showTip(event, '21', 1)" class="fn">square</span> <span onmouseout="hideTip(event, '22', 2)" onmouseover="showTip(event, '22', 2)" class="id">x</span> <span class="o">=</span> <span onmouseout="hideTip(event, '22', 3)" onmouseover="showTip(event, '22', 3)" class="id">x</span> <span class="o">*</span> <span onmouseout="hideTip(event, '22', 4)" onmouseover="showTip(event, '22', 4)" class="id">x</span>
</code></pre></td>
</tr>
</table>
<table class="pre"><tr><td class="lines"><pre class="fssnip"><span class="l">1: </span>
</pre></td>
<td class="snippet"><pre class="fssnip highlighted"><code lang="fsharp"><span onmouseout="hideTip(event, '23', 5)" onmouseover="showTip(event, '23', 5)" class="fn">printfn</span> <span class="s">&quot;result: </span><span class="pf">%d</span><span class="s">&quot;</span> <span class="pn">(</span><span onmouseout="hideTip(event, '21', 6)" onmouseover="showTip(event, '21', 6)" class="fn">square</span> <span class="n">3</span><span class="pn">)</span>
</code></pre></td>
</tr>
</table>
<p>the value is:</p>
<table class="pre"><tr><td><pre><code>result: 9</code></pre></td></tr></table>
<div class="tip" id="21">val square : x:int -&gt; int</div>
<div class="tip" id="22">val x : int</div>
<div class="tip" id="23">val printfn : format:Printf.TextWriterFormat&lt;&#39;T&gt; -&gt; &#39;T</div>

<script src="assets/tips.js"></script><link href="assets/style.css" rel="stylesheet" type="text/css" />
