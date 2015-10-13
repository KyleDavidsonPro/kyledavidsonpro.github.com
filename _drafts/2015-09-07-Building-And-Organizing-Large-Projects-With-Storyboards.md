---
layout: post
title: "Building and Organizing Large Project UI with Storyboards"
description: ""
tagline: "A look at the past and present of building and maintaining UI in iOS"
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

My philosophy on the 'correct' way to do things is that you should go with the trend of the company whose technology you are using, since Xcode 5 the default project type uses storyboards, every single demo in last year's WWDC used storyboards, there's even more functionality available for visual objects in storyboards than there is in NIB files. 

##The Story with Storyboards

It's important to note a storyboard is a single file internally represented by XML therefore if you have multiple people working in parallel on the same storyboard file, merge conflicts will be inevitable and not easily resolved. As a very simple example, imagine developer-a and developer-b branch off from master and both make changes to the same storyboard file, these could be changes as simple as changing the position of a label or adding different layout guides through Interface Builder, this would result in a merge conflict when they sync up like so:
<script src="https://gist.github.com/KyleDavidsonPro/e0acbcdef154d9ecf68f.js"></script>

You could imagine that in a large project with multiple developers these conflicts would get out of hand and be practically impossible to succesfully resolve without choosing one set of changes over the other. So how do we deal with this?

##Break Up Your Storyboards
