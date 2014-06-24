---
layout: page
title: Kyle Davidson
tagline: Trying to technology as hard as I can
---
{% include JB/setup %}

"On the internet, noone knows you're a dog"

## Things I'm working on
<ul>
	<a href="/MKBathrooms/">MKBathrooms</a>
</ul>
    
## Ramblings

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>