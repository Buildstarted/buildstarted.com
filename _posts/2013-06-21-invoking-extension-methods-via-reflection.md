---
layout: post
title: Invoking extension methods via reflection
---

As part of a small project I'm working on I created a dynamic wrapper around a class that was almost exclusively used with extension methods. You can't just do the following.

    _someClass.GetType().GetMethod("ExtensionMethod").Invoke(_someClass, null);
	
Extension methods are just some compiler trickery. They don't exist on the class but separately in a static class elsewhere. To do it properly you'll need to invoke the method on the static class.

	typeof(ExtensionMethodClass)
        .GetMethod("SomeMethod")
        .Invoke(null, new [] { _someClass });

We're required to pass an instance of `_someClass` since that is the first parameter on all extension methods.

This has the minor downside of knowing the extention method class that contains the method we're looking for. That's not always readily apparent with someone else's library and there could be any number of available classes that extend your object. 

**-Ben Dornis**