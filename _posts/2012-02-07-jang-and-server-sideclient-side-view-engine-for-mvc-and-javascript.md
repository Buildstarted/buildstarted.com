---
layout: default
title: Jang and server sideclient side view engine for mvc and javascript
---

<div style="background-color: #202020; border: 4px solid #C0C0C0;border-radius: 5px 5px 5px 5px;box-shadow: 2px 2px 3px #6E6E6E;color: #E2E2E2;display: block;font: 1.5em 'andale mono','lucida console',monospace; overflow: auto; padding:15px; margin-bottom: 1.5em;"><p style="margin-bottom:0;">PM&gt; Install-Package Jang</p></div>

Jang was born out of a desire to have a single template that works on both server and client side using any view engine necessary. 
For instance an ajax site that polls for a new comment and inserts it into the page without a post back you'll have to maintain two views
one for the server-side generation of the initial page and one for the client-side rendering with an ajax call. 

Jang makes it easier to have the best of both worlds. With a single call you can render the same view on the server, while at the same time make a call
on the client and render the same view with new data.

<h2>Example</h2>
Here is a sample view that generates a list of users from a model.
<pre class="prettyprint"><code>
    //userlist.jazz
    < ul>
        @for(var i in model.Model) {
            @jang.renderView("user", model.Model[i])
        }
    < /ul>
    //user.jazz (this is a child view)
    < li>@model.Model.Name< /li>
</code></pre>
    
<h2>Server-side Views</h2>
Rendering a view on the server side is almost no different from how you render child views in Razor.
<pre class="prettyprint">
    @Html.RenderTemplate(viewName, Model)
</pre>

This can be done almost anywhere you currently call child views.
    
<h2>Client-side Views</h2>
There are two ways to render a client-side view.

Direct call
<pre class="prettyprint">
    jang.renderView("user", model)
</pre>
    
This will return the html of the rendered view which you can then use to insert into the dom.

Making an ajax call with jang.ajax will automatically render the view for you and return the resulting html.
<pre class="prettyprint">
    jang.ajax({
        url: '/home/user',
        success: function (result) {
            $("ul#users").append($(result));
        }
    });
</pre>

On the server side you need to return a proper response for the view to be rendered. A "JangResult" object has been provided to make this easy.
<pre class="prettyprint">
    public ActionResult User()
    {
        return new JangResult(new User {Name = "this is a new user "});
    }
</pre>

The JangResult automatically determines the view name based on the action or you can specify a different one yourself.

The goal was to make this seamless and invisible to the consumer.

<h2>View Engines</h2>
Jang was designed to support additional view engines. Support exists for jsRender, Underscore, Mustache and Jazz. (Jazz is currently still in alpha)

The above sample can be translated:
<h3>jsRender</h3>
<pre class="prettyprint"><code>
    //userlist.jsrender
	< ul>
		{{#each Model}}
			< li>{{=Name}}< /li>
		{{/each}}
	< /ul>
</code></pre>

<h3>Underscore</h3>
<pre class="prettyprint"><code>
    //userlist.underscore
    < ul>
	<% for(var index = 0; index < Model.length; index++) {%>
		<%= jang.renderView("user", Model[index]) %>
	<%}%>
    < /ul>
    //user.underscore (this is a child view)
    < li><%= Model.Name %>< /li>
</code></pre>

<h3>Mustache</h3>
<pre class="prettyprint"><code>
    //userlist.mustache
	< ul>
		{{#Model}}
			< li>{{Name}}< /li>
		{{/Model}}
	< /ul>
</code></pre>

The source for this project is hosted on Github at <a href="https://github.com/Buildstarted/Jang" title="https://github.com/Buildstarted/Jang">https://github.com/Buildstarted/Jang</a> please fork it and add support for more view engines :)

I'll have a future post detailing how it all works.

- <strong>Ben Dornis</strong>