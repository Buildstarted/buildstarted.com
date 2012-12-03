---
layout: default
title: Metadatatypes and why theyre useful
---

<h2>MetadataType</h2>

<p>You have a linq dbml file created and you wish to add some attributes to the properties. However, if you update your dbml file for any reason all your changes will disappear.</p>

<p>You'll have 3 separate classes after you're done with one Linq object</p>

<h3>Linq Object</h3>

<pre><code>public partial class YourLinqObject {
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public int Age { get; set; }
}
</code></pre>

<h3>Your attribute class holder</h3>

<pre><code>[MetadataType(typeof(YourLinqObjectMetadata))]
public partial class YourLinqObject {

}
</code></pre>

<h3>Your metadata class</h3>

<pre><code>public class YourLinqObjectMetadata {
    [MaximumLength(50)]
    public string FirstName { get; set; }

    [MaximumLength(50)]
    public string LastName { get; set; }

    [Range(13, 100)]
    public int Age { get; set; }
}
</code></pre>

<p>Whenever you update your dbml it won't affect your partial class and you'll keep your attributes. It does cause a bit of code duplication, however with relation to the properties but it's effective none-the-less.</p>

<h3>- Ben</h3>