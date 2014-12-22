---
layout: post
title: Create an instance of a class without calling a constructor.
---

When creating database models I tend to want required fields to be in the constructor.

    public User(string name, string email)
    {
        /* Initialize the class here */
    }

This is useful for a couple reasons. 1) You have required fields that all `User`s should possess. 2) If `NOT NULL` is in your database schema and you don't want to accidentally throw an exception if you forget to populate certain properties on your model. 

However, with ORMs the waters get a bit muddied. This doesn't even have to come from an ORM it could be any serializer such as XmlSerializer. In general, they won't deserialize into a class that doesn't have an empty default constructor. Most of the time they use `Activator.CreateInstance(Type)` to instantiate a new object. So you do what you have to do you add an empty and useless constructor. To inform others not to use it you could possibly add an attribute `[Obsolete]` or maybe even `///<summary>` telling users of your class not to use the default constructor. Hacks.

### What can you do?
There's a seemingly little known static class that gives us a lot of nice little methods. One of which is `FormatterServices.GetUninitializedObject`.

    var user = (User)FormatterServices.GetUnitializedObject(typeof(User));

This will create an instance of our User class ***without*** calling a constructor. Very useful for models that don't have any initialization. Although the object you get returned does not have any of the properties initialized that's OK. This will automatically be populated by the ORM.

This doesn't really help the consumers of an ORM or serializer as the library must already implement this. However it's definitely something to think about if you're designing a simple ORM or any type of custom serializer. Check out the MSDN for [GetUnitializedObject](http://msdn.microsoft.com/en-us/library/system.runtime.serialization.formatterservices.getuninitializedobject.aspx) and see all the methods available on [FormatterServices](http://msdn.microsoft.com/en-us/library/System.Runtime.Serialization.FormatterServices.aspx)

- **Ben Dornis**