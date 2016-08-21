---
layout: page
title: Bookshelf
---
<ul class="bookshelf">
{% for book in site.books %}
 
  <li>
  	<a class="btn-book" href="{{ book.url }}">
  	  <img src="{{ book.cover }}" alt="{{ book.title }}">
  	  {% include stars.html %}
	</a>
  </li>

{% endfor %}
</ul>