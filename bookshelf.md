---
layout: page
title: Bookshelf
quote: "Your schooling may be over, but remember your education still continues."
quote-author: Unknown
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