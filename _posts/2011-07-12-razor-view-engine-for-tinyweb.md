---
layout: default
title: Razor view engine for tinyweb
---

<p>If you haven't heard of Tinyweb I suggest you check out out at Martin Rue's series of blog posts. (<a href='http://invalidcast.com/2011/05/tinyweb-series-1-getting-started'>Tinyweb series</a>)</p>

<p>When I first tried Tinyweb I thought it was a great example of the <a href='http://en.wikipedia.org/wiki/Single_responsibility_principle'>Single Responsibility Principle</a>. I found it was missing the razor view engine and I wanted to try and port <a href='http://razor-engine.com/'>http://razor-engine.com/</a> to this project.</p>

<p>This wasn't easy. The RazorEngine that <a href='http://fidelitydesign.net'>Matt</a> and I (mostly Matt) developed initially was too...large...for this particular port and didn't have all the features I wanted to bring to this project. Layout pages, for instance. I tried to add layout pages to RazorEngine but had way too much trouble. I decided that I needed to rewrite the engine from scratch and focus entirely on writing one for this particular project.</p>

<h2>TemplateBase&lt;T&gt;</h2>

<p>The first thing I needed was a new template base. The new features I've added is a <strong>Func&lt;string&gt; RenderBody</strong> method so that Layout pages can render the children, a really basic <strong>HtmlHelper</strong> to make a few of the base calls and provide a platform for adding new extension methods based on <strong>HtmlHelper</strong>, and last an instance based <strong>StringBuilder</strong> to create the <strong>View</strong>. (The RazorEngine used a <strong>ThreadStatic</strong> builder)</p>

<pre style='overflow:scroll;'><code>public abstract class TemplateBase<T> {
    //Methods that were not included are inherent to Razor such as Write, WriteLiteral and Execute
    public string Layout { get; set; }

    public Func<string> RenderBody { get; set; }
    public HtmlHelper<T> Html { get; set; }
    public string Path { get; internal set; }
    public StringBuilder Builder { get; private set; }
    public string Result { get { return Builder.ToString(); } }
    public T Model { get; set; }

    protected TemplateBase() {
        Builder = new StringBuilder();
        Html = new HtmlHelper<T>(this);
    }

}
</code></pre>

<h2>RazorCompiler</h2>

<p>This razor compiler is tiny - and it only supports c#. (I'll be adding VB eventually) There are two static overloaded <strong>Render</strong> methods that accept a <strong>model</strong>, <strong>templatename</strong> and <strong>masterpath</strong> and they both return the view as a string. Templates are compiled like normal and cached for future requests. The new feature here is Layout templates. If view has a Layout define or a master page name was passed in we execute the MasterView and pass in the current instance view.</p>

<pre style='overflow:scroll;'><code>//used to render the master
private static string RenderMasterView<T>(T model, string templatePath, TemplateBase<T> instance) {
    var masterPath = Helpers.ResolveTemplatePath(instance.Layout, new string[] { System.IO.Path.GetDirectoryName(templatePath) });

    var masterInstance = GetCompiledTemplate<object>(model, masterPath);
    //RenderBody is a func that we can overwrite
    //makes it nice and easy.
    masterInstance.RenderBody = () => {
        return instance.Result;
    };

    masterInstance.Execute();

    return masterInstance.Result;
}
</code></pre>

<h2>RazorResult</h2>

<p>The <strong>RazorResult</strong> class just returns an <strong>IResult</strong> (expected by TinyWeb) which contains a single method <code>ProcessResult</code>.</p>

<pre style='overflow:scroll;'><code>public virtual void ProcessResult(IRequestContext request, IResponseContext response) {
    response.ContentType = "text/html";
    response.Write(RazorCompiler.Render(_templateName, _master));
}
</code></pre>

<p>To call the RazorResult class we have a View class which contains contains a couple static methods that return <strong>RazorResult</strong>. And in your handler you just call: </p>

<pre><code>public class HelloWorldHandler {
    public IResult Get() {
        HelloWorldModel model = new HelloWorldModel() { Name = "Ben" };
        return View.Razor<HelloWorldModel>(model, "HelloWorld");
    }
}
</code></pre>

<p>It's really that simple.</p>

<p>The full source code is available on <a href='https://github.com/Buildstarted/Tinyweb'>Github</a>. Please do what you will with it.</p>

<p>*Special thanks goes out to <a href='http://samsaffron.com/'>Sam Saffron</a> for helping me whittle down the compiler to what it currently is.</p>

<h3>-Ben Dornis</h3>