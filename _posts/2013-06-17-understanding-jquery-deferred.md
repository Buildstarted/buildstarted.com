---
layout: post
title: Understanding jQuery's Deferred
---

Understanding jQuery's Deferred
===============================

I was talking with a coworker earlier today trying to wrap my head around how part of jQuery's Deferred worked. I didn't understand how part of Deferred worked.

Here's a sample call

    function something() {
    	var deferred = $.Deferred();
    	
    	if (someCall) {
    		deferred.resolve();
    	}
    	
    	return deferred;
    }

    something()
    	.done(function() {
    	})
    	.fail(function() {
    	})
    	.always(function() {
    	});
	
What was confusing me was how `.done` would be called if resolve was executed before the deferred was returned. There was some sort of weird disconnect going on in my brain. I was thinking `someCall` is async so it's possible that `.resolve` won't be called before the method returns. But that just causes race conditions that I knew weren't what I wanted to get into. My coworker then set me straight that when `.done` is executed it checks the state of the deferred and then calls the callback if necessary. This made perfect sense. I don't know why that didn't occur to me.

To cement this in my brain I decided to port Deferred to c#. It helps me a lot to be able to write something.

Here's the result.

<script src="https://gist.github.com/Buildstarted/7618990f33011bba4dbc.js"></script>

I had decided not to make Deferred itself generic because each of the `.Done`, `.Always`, `.Fail` may take many different parameters. I wanted to give some leeway in those calls.

**- Ben Dornis**