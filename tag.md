---
layout: page
title: Tag
quote: "Pay attention to your enemies, for they are the first to discover your mistakes."
quote-author: Antisthenes
---

{% assign rawtags = "" %}
{% for post in site.posts %}
	{% assign ttags = post.tags | join:'|' | append:'|' %}
	{% assign rawtags = rawtags | append:ttags %}
{% endfor %}
{% assign rawtags = rawtags | split:'|' | sort %}

{% assign tags = "" %}
{% for tag in rawtags %}
	{% if tag != "" %}
		{% if tags == "" %}
			{% assign tags = tag | split:'|' %}
		{% endif %}
		{% unless tags contains tag %}
			{% assign tags = tags | join:'|' | append:'|' | append:tag | split:'|' %}
		{% endunless %}
	{% endif %}
{% endfor %}

<h1>Tags</h1>
<div class="tag-list">
{% for tag in tags %}
	<a class="btn-tag" href="#{{ tag | slugify }}"> <i class="fa fa-fw fa-tag"></i>{{ tag }} </a>
{% endfor %}
</div>
<hr/>
<div class="posts-in-tags">
{% for tag in tags %}
	<h2 id="{{ tag | slugify }}" >{{ tag }}</h2>
	 {% for post in site.posts %}
		 {% if post.tags contains tag %}
		 	<a href="{{ post.url }}">{{ post.title }}</a><br/>
		 {% endif %}
	 {% endfor %}
{% endfor %}
</div>
