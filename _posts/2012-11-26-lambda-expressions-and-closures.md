---
layout: post
title: Lambda expressions and closures
---

{{ page.title }}
----------------

<p>I've been dealing a lot with anonymous methods lately I started getting interested in how they work, especially when dealing with closures. I was curious how these variables worked.</p>

<pre><code>for(int i = 0; i < 10; i++) {
    Task.Factory.StartNew(() => Console.WriteLine(i));
}
</code></pre>

<p>One might think that this would output <code>0, 1, 2, 3, 4...9</code> however as the anonymous methods might not be run at the time they're defined and the loop may finish faster and set <code>i</code> to <code>10</code> before the anonymous methods are even executed (depending on the speed of your system). The output on my system results in 10 lines of <code>10</code>. The reason for this is how c# generates the code for this. I've simplified it for brevity.</p>

<pre><code>//original code
class Program
{
    static void Main()
    {
        for (int i = 0; i < 10; i++)
        {
            Task.Factory.StartNew(() => Console.WriteLine(i));
        }
    }
}

//after compilation
//this code has been simplified
//for readability
class Program
{
    static void Main()
    {
        c__DisplayClass2 cDisplayClass2 = new c__DisplayClass2();
        for (cDisplayClass2.i = 0; cDisplayClass2.i < 10; cDisplayClass2.i++)
        {
            Task.Factory.StartNew((Action)(cDisplayClass2.b__0));
        }
    }
}

[CompilerGenerated]
private sealed class c__DisplayClass2
{
    public int i;

    public void b__0()
    {
        Console.WriteLine(this.i);
    }
}
</code></pre>

<p>Wow. The lambda expression sure does hide a lot of code. Let's see what's going on here. Notice a new class was created and it has a public property <code>i</code>. This replaces the local variable in the <code>for/loop</code>. This is how we can access the closure within the anonymous method. It no longer exists in the context of a local variable of the function but as a global field of the generated class. As the methods don't necessarily execute when called you must make sure to make a local copy of that variable. If not the member variable might change before execution.</p>

<p>What if you're accessing a member field of the calling class?</p>

<pre><code>//original
class Program
{
    private string test = "test";

    static void Main()
    {
        var p = new Program();
        p.Run();
    }

    private void Run()
    {
        Task.Factory.StartNew(() => Console.WriteLine(test));
    }
}


//after compilation
//this code has been simplified
//for readability
class Program
{
    private string test = "test";

    private static void Main(string[] args)
    {
      new Program().Run();
    }

    private void Run()
    {
      Task.Factory.StartNew((Action)(b__0));
    }

    [CompilerGenerated]
    private void b__0()
    {
      Console.WriteLine(this.test);
    }
}
</code></pre>

<p>As we can see, this time, the <code>CompilerGenerated</code> method resides in the same class. This is required because it needs to access the same field of the calling class. However, if you need both, access to a local variable and one to a member variable it creates a second class with a references to the caller.</p>

<pre><code>//after compilation
//this code has been simplified
//for readability
class Program
{
    private string test = "test1";

    private void Run()
    {
      c__DisplayClass1 cDisplayClass1 = new c__DisplayClass1();
      cDisplayClass1.__this = this;
      // ISSUE: method pointer
      Task.Factory.StartNew((Action)(b__0));
    }

    [CompilerGenerated]
    private sealed class c__DisplayClass1
    {
      public string test1;
      public Program __this;

      public void b__0()
      {
        Console.WriteLine(this.__this.test + this.test1);
      }
    }
}
</code></pre>

<p>Knowing how the compiler acts on your code is important. Hopefully this will give you some insight as to how some of the magic with anonymous methods/functions happens. </p>