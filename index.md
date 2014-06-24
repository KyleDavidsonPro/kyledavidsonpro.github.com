---
layout: page
title: Kyle Davidson
tagline: Colour my hair, do what I dare
---
{% include JB/setup %}

Software Engineer @[Kainos](http://kainos.com). You can catch me on [Twitter](https://twitter.com/KyleDavidsonPro). Or for further details [Linkedin](https://www.linkedin.com/in/kyledavidsonpro).

## Ramblings
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

## Things I'm working on
<ul>
	<li><a href="/Plumbyard/">Plumbyard - A local plumbing supplies website</a></li>
</ul>
    
