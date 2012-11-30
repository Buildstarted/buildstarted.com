---
layout: default
title: Entity Framework and lazy loading
---

<h2>Entity Framework and virtual properties</h2>

<p>Some of you may have come across a situation where you've created your model and found that one of the properties is null when you were expecting a value.</p>

<p>Here's a simple setup that would produce the error:</p>

<pre><code>public class Person {
    public int PersonID { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public PersonDetail Details { get; set; }
}

public class PersonDetail {
    public int PersonDetailID { get; set; }
    public string UserName { get; set; }
    public string Password { get; set; }
    public DateTime DOB { get; set; }
    public string About { get; set; }
}


Person owner = db.People.Single(p => p.PersonID == 1);
output(owner.Details);
</code></pre>

<p>owner.Details would be null here due to LazyLoading. This allows us to have an object with many different objects that are only loaded when needed rather than loaded when a parent object is loaded. This is especially helpful if you have something like a <strong>Comments</strong> property as part of the user. Without LazyLoading the comments collection would be loaded whenever you wanted to grab just the First Name. A downside to LazyLoading is that each navigation performed requires a separate query to the data source. (Unless that object has already be loaded)</p>

<p>To allow for LazyLoading you need to mark related object properties as virtual.</p>

<pre><code>public class Person {
    public int PersonID { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public virtual PersonDetails Details { get; set; }
}
</code></pre>

<p>In addition you must supply a navigation property in your context:</p>

<pre><code>public class Database : DbContext {
    public DbSet<Person> People { get; set; }
    public DbSet<PersonDetail> PersonDetails { get; set; }
}
</code></pre>

<p>Doing the above will allow entity framework to generate proxy classes that allow for lazy loading. Other requirements for these proxy classes to be generated:</p>

<ol>
<li>A class must be <strong>public</strong>.</li>
<li>A class cannot be marked as <strong>sealed</strong> (or <strong>NotInheritable</strong>).</li>
<li>A class cannot be marked as <strong>abstract</strong> (or <strong>MustInherit</strong>).</li>
<li>A class must have a parameterless constructor that is <strong>public</strong> or <strong>protected</strong>.</li>
<li>A class must not implement <strong>IEntityWithChangeTracker</strong> or <strong>IEntityWithRelationships</strong> interfaces because the EF generated proxies already implement them.</li>
<li>The ObjectContext property <strong>ProxyCreationEnabled</strong> must be set to true. (default)</li>
</ol>

<h3>- Ben</h3>