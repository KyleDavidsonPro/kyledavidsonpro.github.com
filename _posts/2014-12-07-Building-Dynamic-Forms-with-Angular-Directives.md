---
layout: post
title: "Building Dynamic Forms with Angular"
description: "A look at custom directives"
tagline: "A look at custom directives"
category: technology
tags: [programming, technology]
---
{% include JB/setup %}

##A bit about Custom Directives

If you're not very familiar with Angular, directives are essentially tags you can apply to a DOM element that tell Angular JS's html compiler to attach a specified behaviour to it. For example we could use one of the following:
<script src="https://gist.github.com/KyleDavidsonPro/2acb0a4c20cbc0d2ffdc.js"></script>
In this case we would have created a directive called myForm which could have an associated controller, html template and its own scope. There is a restrict property on directives that defines how you can trigger it, so by default we can use an attribute or an element but you can also match against class name. Typically you'd want to use an element when you are creating a component that is in control of the template and an attribute when you are decorating an existing element with new functionality, but of course it all depends on your project needs.

##The Scenario
Ok so you want to build a form in Angular.js, this is easy to accomplish but gets more complicated when the fields are dynamic. We want to be able to provide data about our fields e.g. key, type, label etc to Angular, and build a form using this information. The way we get the data isn't important, for the sake of this example we'll assume we get the following JSON back from a service.
<script src="https://gist.github.com/KyleDavidsonPro/16a3f98c502787132786.js"></script>

Once our data comes back from some service we can use directives to build our form.

##Designing Directives
When naming your directives: use a unique prefix. Try not to name your directives ng-something, as to others this would suggest it is an internal Angular directive, just use something appropriate to what you're working on and keep it to the convential two-letters, for our project at Kainos Smart I went with sm-*.

_Side note when you name a directive in Javascript you use camelCase, but in html you use it with a hyphen like camel-case_

Secondly, in regards to the template associated with your directive, you can either use <b>template</b> (define the HTML in Javascript) or you can use <b>templateUrl</b> (a URL to an HTML partial). I personally prefer the latter, there's more work involved in testing it that way because you need to preload the templates into the <b>$templateCache</b> for tests but it's definitely my go-to. Also depending on your needs you can avoid the round trip to the server with templateUrl by using script tags with an id, there's some trades off there but it's an option none the less.

Lets take a look at the first outer most directive, smField.

##The Field Directive
What we want to do here is loop through the field objects in our array response, and build a field directive each time.

<script src="https://gist.github.com/KyleDavidsonPro/2c88c58d7098ca69c288.js"></script>
At each iteration, we pass the field object into our directive, along with an object to keep track of all the form data that gets entered. So what you're left with when the user completes the form is an object that contains keys for each field, along with values from what the user has entered. This is particularly useful because we're using an isolated scope for our field directive, that is, the directive definition looks like this:

<script src="https://gist.github.com/KyleDavidsonPro/b807657ded3e49294665.js"></script>
It's worth discussing scope because it can cause a lot of confusion for those new to Angular, especially when working with directives like ng-repeat. You can find a great explanation on scopes [here](https://github.com/angular/angular.js/wiki/Understanding-Scopes). You can define your directive's scope as one of the following:
<ol>
	<li><b>False (default)</b>: The directive does not create a new scope. Do not use this when designing reusable directives,
		you want a reusable component to be independant of the outside world, you should be able to plug it in anywhere in your app
		without relying on or effecting existing scope properties.</li>
	<li><b>True</b>: The directive creates a new child scope that prototypically inherits from the parent scope. </li>
	<li><b>{}</b>: Directive gets a new isolated scope completey detached from the parent scope. This is the favourable choice here as it means our form can be built up in any part of the application without interfering with the parent scope.</li>
</ol>

The directive communicates to the parent scope to get the properties it needs by defining them on the object using special symbols known as *prefixes*. There are 3 types of prefixes:
<ul>
	<li><b>@</b>: Text Binding. Whatever is passed in is parsed and returned as a string.</li>
	<li><b>&</b>: One Way Binding. Allows a directive to trigger evaluation of an expression in the context of the original scope, at a specific time. Any legal expression is allowed, including an expression which contains a function call. Because of this, & bindings are ideal for binding callback functions to directive behaviors.</li>
	<li><b>=</b>: Two way Data Binding. Any changes to the bound value are reflected in all scopes. </li>
</ul>

Our sm-field's template looks like this:
<script src="https://gist.github.com/KyleDavidsonPro/8041371a6fbbdc950873.js"></script>

We use ng-switch to conditionally build a child directive depending on the type of field. At Kainos Smart we have custom directives for many types of form-inputs in which we extend the functionality and provide some custom features, as well as defining some of our own form field types, like a typeahead that queries the Workday API. As you can see these child directives don't have any attributes to pass in data, that's because they're scopes are defined as true, thus inheriting the field and formData objects.

Lastly, I will show what the sm-text directive might look like as a very simple text-input with no additional functionality.
<script src="https://gist.github.com/KyleDavidsonPro/5a0aa51ed8446b9ac7ee.js"></script>
We use formData[field.key] as the model for the input, so if our field key was 'firstName' our formData object would have a key-value pair such as <b>firstName: "whatever the user enters".</b> that is accessible from formData.firstName, This works well for us as Smart has a repeatable field type which recursively calls the initial <b>sm-field</b> directive to allow repeatable form sections. So with a completed form we have a nice neat object that can be validated. It might look something like:

<script src="https://gist.github.com/KyleDavidsonPro/4affc81df1e4582b9025.js"></script>