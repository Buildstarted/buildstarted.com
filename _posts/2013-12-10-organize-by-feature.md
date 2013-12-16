---
layout: post
title: Organize By Feature
---

Taking inspiration from <a href='http://thefringeninja.com/'>João P. Bragança</a> in configuring <a href='http://nancyfx.org/'>Nancy</a> to <a href='http://thefringeninja.com/blog/481/organizing-per-feature-in-nancy'>organize per feature</a> I've taken the concept and applied it to MVC.

The idea behind it is to group related classes/files together by feature rather than function.

By default MVC creates a `Controllers`, `Models`, and `Views` folder. This is fine for tiny projects with a couple controllers. It gets unyielding when your website grows.

![Organize By Function](/images/mvc-default.png)

All we need to do is update the Razor View Engine and tell it where our files now reside.

    private void InitializeFeatureEngineExtensions()
    {
        var razor = ViewEngines.Engines.OfType<RazorViewEngine>().Single();
        razor.ViewLocationFormats = new[] {"~/Features/{1}/Views/{0}.cshmtl"}.Concat(razor.ViewLocationFormats).ToArray();
		razor.PartialViewLocationFormats = new[] {"~/Features/{1}/Views/{0}.cshtml"}.Concat(razor.PartialViewLocationFormats).ToArray();
    }
	
That's all there is to it. You can add more paths if you like. I've chosen to keep the default paths intact.

Now we can modify our project structure like so:

![Organize By Feature](/images/mvc-feature.png)

One thing to note is that the tooling for Visual Studio requires the standard Views location. I'm not sure how (if it's even possible) to modify this yet. Add View and Goto View currently do not work.

Thanks to João P. Bragança aka The Fringe Ninja for the inspiration.

Clarification

One of the problems with MVC Areas is that you're still stuck with their folder structure. The point of this post was to highlight a little know ability to change the locations of your views from standard to anything you'd like.

-Ben Dornis