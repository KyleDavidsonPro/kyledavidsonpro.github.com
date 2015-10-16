---
layout: post
title: "Maintaining Large Applications with Storyboards"
description: ""
tagline: "A look at the power of storyboard references"
category: technology
tags: [programming, technology]
---
{% include JB/setup %}

##Building UIs in iOS Development

When it comes to designing the views in your application, there are 3 approaches at your disposal:

<ol>
	<li><b>Code</b>: As you would expect, creating and handling all your visual component's as well as their positioning, animations and so on, via code. Unless there is a very specific requirement that would force you to do this you really shouldn't. In general, hand coded UIs are tedious, increase the amount of tests required, difficult to refactor and aren't really tangible compared to the visual tools at your disposal.</li>
	<li><b>NIBs/XIBs</b>: With nib files, you create and manipulate your user interfaces graphically, using Xcode, instead of programmatically. Because you can see the results of your changes instantly, you can experiment with different layouts and configurations very quickly. You can also change many aspects of your user interface later without rewriting any code. This has been a favourite approach for many and generally successful in larger projects.</li>
	<li><b>Storyboards</b>: While NIBs give the ability to visually represent individual views, storyboards are a visual tool for laying out not only multiple views but also representing the navigation between them. Since their introduction in iOS 5 storyboards have become ever more popular and the recommended way to design the user interface of your application because they enable you to visualize the appearance and flow of your whole application on one canvas</li>
</ol>

My stance on the 'correct' way to do things is that you should go with the trend of the company whose technology you are using. Since Xcode 5 the default project type uses storyboards, every single demo in last year's WWDC used storyboards, there's even more functionality available for visual objects in storyboards than there is in NIB files. 

##The Story with Storyboards

It's important to note a storyboard is a single file internally represented by XML therefore if you have multiple people working in parallel on the same storyboard file, merge conflicts will be inevitable and not easily resolved. As a very simple example, imagine developer-a and developer-b branch off from master and both make changes to the same storyboard file, even something as simple as changing the position of a label would result in a merge conflict when they sync up:
<script src="https://gist.github.com/KyleDavidsonPro/e0acbcdef154d9ecf68f.js"></script>

You could imagine that in a large project with multiple developers these conflicts would get out of hand and be practically impossible to successfully resolve without choosing one set of changes over the other. So how do we deal with this? 

##Break Up Your Storyboards

When it comes to large apps with a bunch of features it would be much better to split out the UI into several storyboards depending on the natural sections/modules of your application. With multiple storyboards the application will be easier to manage and reduces the chance of merge conflicts as you can coordinate work on particular parts of the UI more easily. However this raises the question of how to connect each storyboard together. Before iOS 9 this would typically be done via code like so:
<script src="https://gist.github.com/KyleDavidsonPro/e6abdf42476725e9a4b1.js"></script> 

Obviously the problem with this is that we're making the connection via code and therefore it's not clear how each of the storyboards in your application are connected without knowing the code base, well with iOS 9 came a better way, Storyboard References.

##iOS 9 Storyboard References
As an example let's create a new tabbed application using the default Xcode template and take a look at the main storyboard file that is created for us.

![Initial Storyboard]({{ site.url }}/assets/Tab Bar Storyboard.png)  

Since this is a tab bar application it’s likely each tab will encompass a hierarchy of views and UI components. In this case it's easy to identify the natural modules/sections of the UI since it's really just each tab, we can therefore begin to split the application up into multiple storyboards. This is known as Refactoring. You can select views in an existing storyboard to be refactored out into a new storyboard by highlighting them and selecting Xcode's <b>Editor</b> menu > <b>Refactor to Storyboard</b>.

![Refactor Storyboard]({{ site.url }}/assets/Refactor to Storyboard.png)  

This will prompt you to give a name for the new Storyboard file that will be created, in my case <b>FirstTab.storyboard</b> and then it will move the selected views to the new storyboard and insert a storyboard reference in the original storyboard.

![Storyboard Reference]({{ site.url }}/assets/Storyboard Reference.png) 

If you double click the storyboard reference it will take you to the storyboard file associated with that reference. As well as refactoring existing views into new storyboards you can also create storyboards from scratch and reference them later, for example if we created a new Storyboard file for a new tab in our application called <b>ThirdTab.storyboard</b> and gave it a view controller it might look something like this.

![Third Tab]({{ site.url }}/assets/Third Tab.png) 

<i>Note the View Controller's properties panel on the right hand side where I've checked <b>Is Initial View Controller</b>, this creates a <b>Storyboard Entry Point</b> so that any storyboard reference will by default instantiate this view controller when triggered. You can link to other scenes in the storyboard rather than the initial view controller by setting the <b>Storyboard ID</b> in the Identity Inspector for the view controller you wish to instantiate, however the default will do for this example</i>

With the storyboard for our third tab created we can then drag in a new Storyboard Reference in <b>Main.storyboard</b> from the Object Library.

![Object Library]({{ site.url }}/assets/Object Library.png)

You can set the storyboard associated by the storyboard reference by selecting it and choosing the new storyboard from the dropdown.

![Set Storyboard]({{ site.url }}/assets/Set Storyboard.png)

Then all that's left to do is to Ctrl + Drag from the Tab Bar Controller to our Storyboard Reference to connect them with a Relationship Segue.

![Set Relationship]({{ site.url }}/assets/Set Relationship.png)

This creates a new tab on the tab bar for us and Voilà! 

![Final Storyboard]({{ site.url }}/assets/Final Storyboard.png)