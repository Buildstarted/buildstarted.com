---
layout: post
title: Parrot for AspNet
---

Install the nuget package using the following command.
<div class="nuget-badge"><p><code>PM&gt; Install-Package Parrot.AspNet</code></p></div>

How is Parrot different from Razor?
===================================

Views are meant to be dumb. They shouldn't contain any (or hardly any) code and just output the view with data from the model. Razor's focus was to give a cleaner separation of code and html, Parrot strives to remove code from your views.

Here is a sample razor template converted to parrot.

    //Razor sample
    <h3>Welcome, @Model.Name</h3>
    
    <ul id="cart">
    	@foreach(var item in Model.ShoppingCart) {
    		<li class="item">@item.Name - @item.Price</li>
    	}
    </ul>

    //Parrot sample
    h3 | Welcome, @Name
    
    ul#cart(ShoppingCart) > li.item | @Name - @Price

It's just a basic sample but you can already see some major differences. First note that the Model keyword isn't necessary. By default the page is scoped to the Model property of the page.
You're not limited to that though. If your view has other properties you want to access you can easily do so by specifying the property name instead @Request.Url (or something similar). If there's a conflict the Model wins. (i think i need to change this or provide a way to force)

There's no need for a foreach in Parrot as well. Tags are smart enough to know if the property applied to them is an array or IEnumerable to loop over the items and apply them to the children. 
Notice there's no variable name declaration for the child element. By default the element is scoped to the iterator item.

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

Parrot was designed for extensibility. You can easily add new element renderers. 

Take for example the `if` renderer. If a renderer does not exist for a specific element it will default to rendering it as html. In this case it would render `<if></if>`.

We have our `if` renderer and now it will only render if the parameter passed evaluates to `true`.

    if(SomeModelValue) |It's true!
	
The above will render only the text `It's true!`. This particular renderer renders only children. Other renderers can do other things.

A lot of renderers already exist for common types that behave differently than standard html `<openbracket></closebracket>`. Take the `ul/ol` tag for example. By default it would only render once including it's children once.
I've created a renderer specifically for those two as they're lists. If you pass in an `IEnumerable` parameter it will loop over the items and render the children each time.

I'll be working on the documentation on how to create these.


Final Thoughts
==============

Parrot is a very new product. There are going to be some rough edges to be smoothed out. Help is **very** welcome. Visit [This is parrot](http://thisisparrot.com/) for more info.

**- Ben Dornis**