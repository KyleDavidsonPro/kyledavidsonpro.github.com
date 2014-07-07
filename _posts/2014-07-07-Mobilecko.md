---
layout: post
title: "Mobilecko"
description: "My final year project"
tagline: "A system to facilitate regular blood donation"
category: programming
tags: [programming, health]
---
{% include JB/setup %}

#Who donates blood reguarly in the UK?

<b>Nobody.</b> Well, not really but the actual figures aren't much better, stand back while I copy and paste some research from my dissertation. <i>'only 4% of people regularly give blood…’ (NHS.uk, 2013). Similarly, in the United States, ‘Although an estimated 38 percent of the U.S. population is eligible to donate blood at any given time, less than 10 percent do so annually.’ (Aabb.org, 2013).</i>  


![shock]({{ site.url }}/assets/shock.gif)  

If you were to decide right now you're going to donate blood for the first time, you're probably gonna google your way to the Northern Ireland Blood and Transfusion Service's website, where they provide a list of upcoming mobile donation sessions that can be attended around the country. You give a quick glance for one in your area, lets assume it's not there, so what do you do? Statistically speaking, you do bugger all.  

To be honest, it's likely there's mobile donation sessions in your area frequently, you just don't give a proactive effort to keep track of that. For all you know you've driven past 3 of them on the way to work this week. You're lazy! But... so are the other 96% of the population, including me. You'd think with the latest Apple 7 and Android Lumia we'd have some kind of app for donating blood wirelessly by now. Well I've got some great news...

##There's an app for that!
Obviously not to donate blood wirelessly... are you crazy? That's imposs- look nevermind. What I'm saying is I built something that allows an organisation such as the NIBTS to be able to have their users download a mobile app that will notify them whenever they are physically near a mobile blood donation session. Obviously, other business markets with an aspect of mobile operation can use this, i.e. food trucks, but let's stick to blood donation as the use case since it is after all the whole inspiration of the solution. 

So hopefully the situation that now arises when someone decides they want to donate blood is:
<ol>
<li>They download the app</li>
<li>They are notified whenever they are physically near a mobile donation session</li>
<li>They donate</li>
</ol>

Besides the main intention of an increase in lives saved, the idea is to provide features within the application to encourage regular donation, the ability to track progression and share via social networking to create further awareness of blood donation.  

![yay]({{ site.url }}/assets/yay.gif)  

I'm gonna give a brief run down of how it actually works below but I've put a header before that happens so people who don't care about that side of things can bail out of this blog. Lastly for those interested, [Early screenshots of the mobile app](http://imgur.com/a/DVXFS#4). Keep in mind these screenshots are very early days and do not include the profile system that was created. Also, [The web client](http://kyledavidsonpro.github.io/MobileckoWeb/login.html). Feel free to create an account and play about on it, keep in mind everything is still in development and bugs are likely!

##Technical details below, get out while you can

The web client is just a basic html/js site that uses a Parse cloud database to store information on users and their events. Events are restricted through user permissions, i.e. GiveBloodNI wouldn't be able to see McDonald's event information etc. Google Geocoder API is used to geolocate the provided event address in order to find out where it is. Any created events can be seen on the Google Map and can be edited on the fly using the table of events (JQuery). Any changes are made asynchronously to Parse. 

The mobile client is an iOS application that uses the cloud database to retrieve the event information, store it locally and notify users of upcoming events using the iOS background location and push notification services. There's a profile system to allow users to log in and share events via Facebook.There's lots of things I want to do with this down the line but it's been temporarily shelved until I get sorted within the new job.