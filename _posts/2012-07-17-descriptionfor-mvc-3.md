---
layout: default
title: DescriptionFor mvc 3
---

<p>I'm surprised this particular feature doesn't exist for MVC - it's such a simple function.</p>

<p>I needed a description of the fields being entered for a form and I decided to use the <strong>Description</strong> parameter of the <strong>Display</strong> attribute. To add the attribute you just:</p>

<pre><code>public class RegistrationModel {
    [Display(Name = "Referrer", Description="If anyone referred you to us please enter their name?")]
    public string Referrer { get; set; }
}
</code></pre>

<p>Nice and simple. However, there's no easy way to get the description text out of the property. </p>

<pre><code>public static IHtmlString DescriptionFor<TModel, TValue>(this HtmlHelper<TModel> helper, Expression<Func<TModel, TValue>> expression) {
    return DescriptionFor<TModel, TValue>(helper, expression, null);
}

public static IHtmlString DescriptionFor<TModel, TValue>(this HtmlHelper<TModel> helper, Expression<Func<TModel, TValue>> expression, string descriptionText) {
    ModelMetadata metadata = ModelMetadata.FromLambdaExpression(expression, helper.ViewData);
    string text = descriptionText ?? metadata.Description;

    if (string.IsNullOrEmpty(text)) return new HtmlString("");

    TagBuilder tag = new TagBuilder("span");
    tag.SetInnerText(text);

    return new HtmlString(tag.ToString(TagRenderMode.Normal));
}
</code></pre>

This technique is also useful if you want to create additional attributes with html helpers for some custom stuff. Like a mask attribute or something.

<strong>-Ben Dornis</strong>