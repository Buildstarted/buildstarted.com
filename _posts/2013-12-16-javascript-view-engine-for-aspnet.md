---
layout: post
title: Javascript View Engine for Asp.Net
---

I was chatting with a friend the other day and was remarking about [Parrot](http://thisisparrot.com) when I made an offhand comment that I should build a **view engine** that can execute Javascript.

I hate when that happens. I started thinking that there are so many great view engines out there written in Javascript that instead of writing a new one I should just use what's already out there. A few hours later I found myself writing such a view engine.

First I had to find a nice vm for javascript that I could execute within .Net. I found some promising ones after a lot of searching, [v8sharp](http://v8sharp.codeplex.com/) and [Javascript.Net](https://github.com/chillitom/Javascript.Net). I first played around with js.net but ran into some issues and since this was just a test I wasn't interested in figuring out what's wrong and went with v8sharp instead. Dev went well and I got the basics of the engine working. Eventually I settled on [ClearScript](http://clearscript.codeplex.com/). It had the most feature complete implementation that had excellent host side capabilities and is in active development. I could execute a [Mustache](https://github.com/janl/mustache.js) template without any issues.

After I got the core library working I started thinking about ways to enhance it. It needed support for the plethora of templating engines already out there available for javascript. Rather than write a separate .Net class for each engine I just had the view engine itself search for specific types of javascript files in the project that it could then load. This has the advantage of being easily upgraded with new engines by just adding new .js files to the project or updating existing .js files.

The view engine itself is fairly basic and isn't much different from any other view engine. The main difference is how the views are rendered. We build up our Javascript file run it through ClearScript and return the results. It's fairly simple and was a quickly put together project.

To get started one just needs to add the view engine to the list of available view engines to an MVC project.

    protected void Application_Start()
    {
        AreaRegistration.RegisterAllAreas();

        WebApiConfig.Register(GlobalConfiguration.Configuration);
        FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
        RouteConfig.RegisterRoutes(RouteTable.Routes);

        ViewEngines.Engines.Add(new JavascriptViewEngine());
        //ViewEngines.Engines.Add(new JavascriptViewEngine(AlternateEngineDefinitionLocation, new [] { "~/AlternateViewLocationDirectories" }));
    }

Next you'll need to tell the JavascriptViewEngine the location of your engines script. The default path is `~/scripts/Javascript.ViewEngines.js`.

Register your templating engine with the engine like the following.

    JsViewEngines.add({
        name: "Mustache",
        extensions: ["mustache"],
        template: new Template(function (template, model) {
            return Mustache.render(template, model);
        }),
        requires: ["mustache.js"]
    });

We have a little bit of meta data here. Most are fairly obvious. Extensions is an array of view filename extensions to look for. `includes` is an array of javascript files required to execute this engine against templates. `template` is the actual javascript rendering call. It's different for each engine. You must wrap the template function with the `new Template` constructor. This will tell the view engine how to execute the view.

One "gotcha" is that these are run **on the server**. As such you don't have the ability to manipulate the dom or output to the console or any other things you can do on a client/browser templating engine.

You can view a live sample at http://jsve.buildstarted.com/. It has several engines already added. The templates are really simple for now.

You can check out the code on my github repo at [https://github.com/Buildstarted/Javascript.ViewEngines](https://github.com/Buildstarted/Javascript.ViewEngines). I'll be creating a [Nuget](https://nuget.org) package soon as there are some issues between x86 and x64.

-Ben Dornis