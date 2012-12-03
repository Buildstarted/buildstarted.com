---
layout: default
title: Update settings table with extension methods
---

So after working with the <a href='http://buildstarted.com/2010/08/09/creating-a-settings-table-that-can-handle-almost-any-type-of-value/'>Settings table</a> from my previous post a bit, it's clear to me that creating some extension methods would make working with settings easier. To do this though we have to modify the Model a bit first and rename "Value" to lowercase "value". The summary xml documentation is there to inform the user there's an Extension method

<pre><code>public class Setting {
    public int SettingID { get; set; }

    public string Name { get; set; }

    /// <summary>
    /// Use the extension methods instead of this value; ex: Value&lt;T&gt;
    /// <summary>
    public byte[] value { get; set; }
    public string Type { get; set; }
}
</code></pre>

<br />

###Creating the Extension Method

<pre><code>    [Extension]
    public static T Value<T>(this Setting setting) {
        if (setting == null) return default(T);

        if (setting.Type != typeof(T).FullName)
            throw new InvalidCastException(
                string.Format("Unable to cast: {0} to {1}",
                    typeof(T).FullName,
                    setting.Type));

        return (T)(new BinaryFormatter()
                            .Deserialize(new System.IO.MemoryStream(setting.value)));
    }

    [Extension]
    public static void Value<T>(this Setting setting, T value) {
        if (setting == null) throw new ArgumentNullException("setting");

        System.IO.MemoryStream ms = new System.IO.MemoryStream();
        new BinaryFormatter().Serialize(ms, value);

        if (setting.Type != typeof(T).FullName)
            throw new InvalidCastException(
                string.Format("Unable to cast: {0} to {1}",
                    typeof(T).FullName,
                    setting.Type));

        setting.value = ms.ToArray();
    }
</code></pre>

Doing this also has the added benefit of making the Generic methods in the DataContext cleaner

<pre><code>    public T Setting<T>(string Name) {
        var setting = Settings.SingleOrDefault(s => s.Name == Name);
        return setting.Value<T>();
    }

    public void Setting<T>(string Name, T Value) {
        var setting = Settings.SingleOrDefault(s => s.Name == Name);
        setting.Value<T>(Value);
    }
</code></pre>

As you can see the extension methods are very easy to create. This gives us the benefit of accessing the values in a cleaner way

<pre><code>var setting = db.Settings.First();
setting.Value<string>("this is a new value");
var theValue = setting.Value<string>();
</code></pre>

The generic methods are what make the settings Model powerful and (almost) seamless.

###- Ben