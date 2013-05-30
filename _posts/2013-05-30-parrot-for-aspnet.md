---
layout: post
title: Parrot for AspNet
---
Install the nuget package using the following command.
<div class="nuget-badge"><p><code>PM&gt; Install-Package Parrot.AspNet</code></p></div>

See my introductory article on Parrot [here](http://buildstarted.com/2012/08/17/why-you-should-care-about-parrot-a-new-asp-net-mvc-view-engine/).

How is Parrot different from Razor?
===================================
Views are meant to be dumb. They shouldn't contain any (or hardly any) code and just output the view with data from the model. Razor's focus was to give a cleaner separation of code and html, Parrot strives to remove code from your views.

Here is a sample razor template converted to parrot.

###Razor sample
    <h3>Welcome, @Model.Name</h3>
    
    <ul id="cart">
      @foreach(var item in Model.ShoppingCart) {
    		<li class="item">@item.Name - @item.Price</li>
    	}
    </ul>

###Parrot sample
    h3 | Welcome, @Name
    
    ul#cart(ShoppingCart) > li.item | @Name - @Price

It's just a basic sample but you can already see some major differences. First note that the Model keyword isn't necessary. By default the page is scoped to the Model property of the page.
You're not limited to that though. If your view has other objects you want to access, such as HttpContext.Request or ViewContext you can easily do so by specifying the property name instead, @Request.Url (or something similar). If there's a conflict the Model wins. (I think I need to change this or provide a way to force)

There's no need for a foreach in Parrot as well. Tags are smart enough to know if the property applied to them is an array or IEnumerable to loop over the items and apply them to the children. 
Notice there's no variable name declaration for the child tag. By default the tag is scoped to the iterator item.

Layouts and Partials
====================

Layouts are slightly different in Parrot. In Razor you can define a layout file by setting a variable in either the page or in _ViewStart.cshtml. In Parrot you just wrap your content. The content tag in your layout page
Tells Parrot where to output. 'filename' should be a path to the layout file.

    layout('filename') {
    	h1 |We're in a layout!
    }

    html > body > content
	
Partials are just as simple

	html > body > partial('filename')

Extensibility
=============

Parrot was designed for extensibility. You can easily add new tag renderers. 

If a tag is specified in code but no renderers exist for that particular tag then it will default to a standard html renderer and output `<tag>...contents...</tag>`.

Suppose we needed an `if` tag that would output it's children if the object passed in is true. Something like this.

    if(SomeModelValue) |It's true!

It wouldn't make sense to output `<if>It's true!</if>` so the custom renderer not only outputs if the value is true but also only outputs the children.	

A lot of renderers exist for common tags that behave differently than standard html `<tag></tag>`. The `ul/ol` tags, for example. I've created a renderer specifically for those two as they're lists. If you pass in an `IEnumerable` object as a parameter it will loop over the items and render the children each time.

I'll be working on the documentation on how to create these as well as documenting the features of some of the custom ones in a future blog post.

###Sample If renderer
<script src="https://gist.github.com/Buildstarted/5676078.js"></script>

Final Thoughts
==============

Parrot is a very new project. There are going to be some rough edges to be smoothed out. Help is **very** welcome. Visit [This is parrot](http://thisisparrot.com/) for more info on the Nancy, Javascript and Asp.Net libraries.

**- Ben Dornis**