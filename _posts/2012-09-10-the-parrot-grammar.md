<h1>Creating an element</h1>

<p>Creating an html element is really simple - just type a word. </p>

<pre><code>div
a
ul
some-random-name
</code></pre>

<p>These will create an html element (using the default html renderer) with the word as the tag.</p>

<pre><code><div></div>
<a></a>
<ul></ul>
<some-random-name></some-random-name>
</code></pre>

<h1>Adding an id or class to your new element</h1>

<p>There are two ways to add an id or class declaration to your elements. We'll focus on the simple way in this example.</p>

<pre><code>div#sample-id
</code></pre>

<p>This will simply create an html element with the id tag set to "sample-id".</p>

<pre><code><div id="sample-id"></div>
</code></pre>

<p>The same method is used for classes but with a . instead.</p>

<pre><code>div.sample-class
<div class="sample-class"></div>
</code></pre>

<p>You may combine these two in one declaration. An element may only have <em>one</em> id tag but can have as many class declarations you'd like.</p>

<pre><code>div#sample-id.sample-class.other.info
<div class="sample-class other info" id="sample-id"></div>
</code></pre>

<h1>Adding other attributes</h1>

<p>Ids and classes aren't the only attributes you'll want to add. Adding additional attributes is simple. wrap a simple key/value pair with brackets. Multiple attributes can be added with a space in between. You can also add the id/class attributes here as well but it's not recommended.</p>

<pre><code>div[name="value"]
div[name="firstname" data-val-id="something"]

<div name="value"></div>
<div name="firstname" data-val-id="something"></div>
</code></pre>

<h1>Adding children</h1>

<p>There are two ways to add children. To add multiple children you'll usually want to wrap them in a parent using curly braces. To add a single child you can append it using the <code style="display:inline;">></code> syntax.</p>

<pre><code>ul {
   li
   li
   li
   li
}

div > a > span

<ul>
    <li></li>
    <li></li>
    <li></li>
    <li></li>
</ul>

<div><a><span></span></a></div>
</code></pre>

<h1>Adding siblings</h1>

<p>Sometimes you don't need or want to wrap multiple children in curly braces. You can add siblings to an element by using the <code style="display:inline;">+</code> selector syntax.</p>

<pre><code>ul > li + li + li + li

<ul>
    <li></li>
    <li></li>
    <li></li>
    <li></li>
</ul>
</code></pre>

<h1>Model parameters</h1>

<p>To bind an element to a model you just add it to the element declaration surrounded by parenthesis <code style="display:inline;">()</code>.</p>

<pre><code>div(User)
</code></pre>

<p>This element is now bound to the User property of your model. This affects children properties as well and allows for simplified nesting.</p>

<pre><code>div(User) > span(FirstName) + span(LastName)
</code></pre>

<h1>String literals</h1>

<p>There are several ways to output a string literal.</p>

<pre><code>span > | String that reads until newline character

"String wrapped in quotes"

@"String which doesn't
  care about newlines"

'String wrapped in single quotes'

@'Multiline string wrapped
  in single quotes'
</code></pre>

<h1>Variable outputs</h1>

<p>There are two shortcuts to output a variable. If you pass in a variable to a block element and do not provide any children the variable with be added to the dom as with <code style="display:inline;">ToString()</code> applied and added as the first child to that element. You can also encode output using <code style="display:inline;">:</code> or raw output using <code style="display:inline;">=</code>. (PageTitle = "Parrot")</p>

<pre><code>title(PageTitle)
title > :PageTitle
title > =PageTitle

<title>Parrot</title>
</code></pre>

<h1>Shortcuts</h1>

<p>You can shortcut divs by leaving div out when using #sample-id or .sample-class. </p>

<pre><code>#sample-id
<div id="sample-id"></div>
</code></pre>

<p>Some elements can override the default tag name such as a ul or ol. The default for these elements (as children) are li.</p>

<pre><code>ul > #sample-id
<ul><li id="sample-id"></li></ul>
</code></pre>

<p>You can shortcut common attributes such as <code style="display:inline;">type</code> as well. For instance when adding an input element you can specify it's type by adding a <code style="display:inline;">:</code> followed by type.</p>

<pre><code>input:text
input:radio

<input type="text" />
<input type="radio" />
</code></pre>

<h1>Parser</h1>

<p>This is a very brief overview of the grammar for parrot. I don't anticipate any near-future changes. I am currently working on an alternative parser for the language to avoid a reference to GoldParser. I hope to have it released in the next couple weeks.</p>

<p><strong>-Ben Dornis</strong></p>