---
layout: page
title: Notes
quote: "Your schooling may be over, but remember your education still continues."
quote-author: Unknown
---
<h2>Quick Reference:</h2>

<ul>
{% assign notes = (site.posts | where: "layout" , "note" ) %}

{% for post in notes %}
  <li>
  	<a href="{{ post.url }}">
  	  {{ post.title }}
	</a>
  </li>
{% endfor %}
</ul>