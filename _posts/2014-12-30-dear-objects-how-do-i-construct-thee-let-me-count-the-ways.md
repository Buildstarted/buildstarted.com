---
layout: post
title: Dear Objects - How do I construct thee? Let me count the ways.
---

## Typical construction

The standard way to create a new instance of an object is to use the `new` keyword.

    var instance = new CoolObject();
	var instance = new CoolObject(someParameter);

This is great when you know the object you're constructing.


## Reflection

You can also use reflection to create a new instance of an object. This is a little more involved.

    //Here's a sample constructor
    public CoolObject() { }
	
    Type[] arrayOfTypeParameters = null;
    var constructorInfo = typeof(CoolObject).GetConstructor(BindingFlags.Instance, null, arrayOfTypeParameters, null);

If we want a specific constructor with parameters we can make a call like the following

    public CoolObject(bool isCool) { }
	
    Type[] arrayOfTypeParameters = new [] { typeof(bool) };
    var constructorInfo = typeof(CoolObject).GetConstructor(BindingFlags.Instance, null, arrayOfTypeParameters, null);
	
With the [constructorInfo](http://msdn.microsoft.com/en-us/library/system.reflection.constructorinfo%28v=vs.110%29.aspx) we can now invoke the constructor to return our object.

    var instance = (CoolObject)constructorInfo.Invoke(null);

Or with a parameter

    var instance = (CoolObject)constructorInfo.Invoke(new [] { false });

With this method we can invoke `private`, `protected` and `public` constructors.


## Activator.CreateInstance/Assembly.CreateInstance

These are both technically shortcuts to the *Reflection* method above. [Assembly.CreateInstance](http://msdn.microsoft.com/en-us/library/system.reflection.assembly.createinstance%28v=vs.110%29.aspx) calls [Activator.CreateInstance](http://msdn.microsoft.com/en-us/library/system.activator.createinstance%28v=vs.110%29.aspx) which calls `ConstructorInfo.Invoke`. You can, however, use a string for the type you wish to construct.

    var instance = (CoolObject)Activator.CreateInstance("MyAwesomeProgram.CoolObject");
    var instance = (CoolObject)Assembly.GetExecutingAssembly().CreateInstance("MyAwesomeProgram.CoolObject");
	
One caveat with the `Assembly` method is you must know which assembly contains the type you wish to instantiate. You can do a couple things here.

    //this retrieves the assembly that contains your type
    var assembly = Assembly.LoadFile(@"c:\my.dll");
    var type = assembly.GetType("MyAwesomeProgram.CoolObject");
    var instance = Activator.CreateInstance(type);

    //or
    var instance = assembly.CreateInstance("MyAwesomeProject.CoolObject");

You can also pass type info directly or even use generics.

    var instance = Activator.CreateInstance(typeof(CoolObject));
    var instance = Activator.CreateInstance<CoolObject>();

The latter call is especially useful when dealing with generics.


## new T()

Sometimes all you have is a generic `T` and you want to create a new instance of `T` that has a default constructor. We need to declare that `T` can be constructed. We can do this with the `new()` constraint on `T`.

    public CoolObject() { }
	
	public T MakeObject<T>() where T : new() 
    {
        return new T();
    }

There's a way to return the default value of `T`. However this returns the default value for an object which in most cases is `null`. Value types such as `Int32` will return their default value, which in this case is `0`.


## FormatterServices.GetUninitializedObject

I covered this in my previous [blog post](http://buildstarted.com/2014/12/22/create-an-instance-of-a-class-without-calling-a-constructor/). However here's the short version.

    var instance = (CoolObject)FormatterServices.GetUninitializedObject(typeof(CoolObject));

This particular one is strange though. It ***does not*** call any constructors. It does not initialize the object nor any of it's fields/properties except to their default values.


## Final

Hopefully this helps you. If I missed any please leave a comment! 

###-Ben Dornis