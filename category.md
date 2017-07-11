---
layout: page
title: Category
quote: "Pay attention to your enemies, for they are the first to discover your mistakes."
quote-author: Antisthenes
---

{% assign rawcats = "" %}
{% for post in site.posts %}
	{% assign rawcats = rawcats | append:'|' | append:post.category %}
{% endfor %}
{% assign rawcats = rawcats | split:'|' | sort %}

{% assign cats = "" %}
{% for cat in rawcats %}
	{% if cat != "" %}
		{% if cats == "" %}
			{% assign cats = cat %}
		{% endif %}
		{% unless cats contains cat %}
			{% assign cats = cats | join:'|' | append:'|' | append:cat | split:'|' %}
		{% endunless %}
	{% endif %}
{% endfor %}

<h1>Categories</h1>
<div class="tag-list">
{% for cat in cats %}
	<a class="btn-tag" href="#{{ cat | slugify }}"> <i class="fa fa-fw fa-tag"></i>{{ cat }} </a>
{% endfor %}
</div>
<hr/>
<div class="posts-in-tags">
{% for cat in cats %}
	<h2 id="{{ cat | slugify }}" >{{ cat }}</h2>
	 {% for post in site.posts %}
		 {% if post.category == cat %}
		 	<a href="{{ post.url }}">{{ post.title }}</a><br/>
		 {% endif %}
	 {% endfor %}
{% endfor %}
</div>
