---
layout: default
title: Imetadataaware and modelmetadata
---

<p>A week or so ago I posted an <a href='http://buildstarted.com/2010/09/14/creating-your-own-modelmetadataprovider-to-handle-custom-attributes/'>article</a> on writing your own <strong>ModelMetadataProvider</strong>. That particular method was a bit overkill for the sample and I wanted to get back to this particular example and provide a much easier method using the <strong>IMetadataAware</strong> attribute. Using the same example I'm going to add the IMetadataAware interface to my TooltipAttribute.</p>

<pre><code>public class TooltipAttribute : Attribute, IMetadataAware {
    public TooltipAttribute(string tooltip) {
        this.Tooltip = tooltip;
    }

    public string Tooltip { get; set; }

    public void OnMetadataCreated(ModelMetadata metadata) {
        metadata.AdditionalValues["Tooltip"] = this.Tooltip;
    }
}
</code></pre>

<p>The magic happens in the OnMetadataCreated implementation. Here we can populate the AdditionalValues with anything we need for our particular Attribute. In this case we're adding a Tooltip key. Your name should be unique as other attributes or providers may add their own keys with the same name It is important to remember that attributes <strong>are not always read in the same order</strong>. So your tooltip attribute may be called first, last or somewhere in the middle. This is an important distinction as it may cause undesired effects.</p>

<p>The interface is consumed by <strong>AssociatedMetadataProvider</strong> and is used by the <strong>DataAnnotationsModelMetadataProvider</strong> and any other provider that derives from AssociatedMetadataProvider.</p>

<p>Now we can utilize the same HtmlHelper as before.</p>

<pre><code>public static IHtmlString TooltipFor<TModel, TValue>(
                             this HtmlHelper<TModel> html,
                             Expression<Func<TModel, TValue>> expression) {
    var x = ModelMetadata.FromLambdaExpression<TModel, TValue>(expression, html.ViewData);
    if (x.AdditionalValues.ContainsKey("Tooltip"))
        return new HtmlString((string)x.AdditionalValues["Tooltip"]);

    return new HtmlString("");
}
</code></pre>

<p>This new interface in MVC 3 is full of awesome as it makes it much easier than before to work with Model Metadata and doesn't require us to create a new provider to handle it.</p>

<h3>-Ben</h3>