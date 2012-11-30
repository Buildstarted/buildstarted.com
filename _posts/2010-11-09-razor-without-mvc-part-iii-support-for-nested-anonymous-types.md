---
layout: default
title: Razor without mvc part iii support for nested anonymous types
---

<p>One of the problems with my previous post was you couldn't have an object definition like:</p>

<pre><code>var nestedType = new {
    Name = "Ben",
    Accounts = new {
        Twitter = "BuildStarted",
        StackOverflow = "BuildStarted"
    }
};
</code></pre>

<p>I've updated the RazorDynamicObject to compensate. </p>

<pre><code>    /// <summary>
    /// Dynamic object that we'll utilize to return anonymous type parameters
    /// </summary>
    class RazorDynamicObject : DynamicObject {
        internal object Model { get; set; }
        public override bool TryGetMember(GetMemberBinder binder, out object result) {
            PropertyInfo propInfo = Model.GetType().GetProperty(binder.Name);

            if (propInfo == null) {
                throw new InvalidOperationException(binder.Name);
            }

            object returnObject = propInfo.GetValue(Model, null);

            Type modelType = returnObject.GetType();
            if (modelType != null
                && !modelType.IsPublic
                && modelType.BaseType == typeof(Object) 
                && modelType.DeclaringType == null) {
                    result = new RazorDynamicObject() { Model = returnObject };
            } else {
                result = returnObject;
            }

            return true;
        }
    }
</code></pre>

<p>Now, we're checking to see if the object we're pulling out of our model is another anonymous type and returning a new RazorDynamicObject to handle subsequent anonymous type calls.</p>

<p>I probably could have used the ExpandoObject but this works just fine.</p>