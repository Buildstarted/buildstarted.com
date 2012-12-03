---
layout: default
title: Razor templating on codeplex
---

<p>Good news everyone! The Razor Templating project <a href='http://fidelitydesign.net'>Matthew Abbott</a> &amp; I have been working on is available on <a href='http://razorengine.codeplex.com/'>Codeplex.com</a>! </p>

<h3>Project Description</h3>

<p>A templating engine built upon Microsoft's Razor parsing technology. The Razor Templating Engine allows you to use Razor syntax to build robust templates. Currently we have integrated the vanilla Html + Code support, but we hope to support other markup languages in future.</p>

<p>Using the templating engine couldn't be easier, using a simple synax, we can do the following:</p>

<pre><code>string template = "Hello @Model.Name! Welcome to Razor!"
string result = Razor.Parse(template, new { Name = "World" });
</code></pre>

<p>The templating engine supports strict and anonymous types, as well as customized base templates, for instance:</p>

<pre><code>Razor.SetBaseTemplate(typeof(HtmlTemplateBase<>));

string template = 
@"<html>
    <head>
      <title>Hello @Model.Name</title>
    </head>
    <body>
      Email: @Html.TextBoxFor(m => m.Email)
    </body>
  </html>";

var model = new PageModel { Name = "World", Email = "someone@somewhere.com" };
string result = Razor.Parse(template, model);
</code></pre>

<p>I hope everyone enjoys this!</p>

<h3>- Ben</h3>