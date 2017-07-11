---
layout: page
title: Archive
quote: "Pay attention to your enemies, for they are the first to discover your mistakes."
quote-author: Antisthenes
---

{% for post in site.posts %}

{% capture currentyear %}{{ post.date | date: "%Y" }}{% endcapture %}
{% if currentyear != year %}

<h2>{{currentyear | strip_html}}</h2>

{% capture year %}{{ currentyear }}{% endcapture %}

{% endif %}

<table cellpadding="0" cellspacing="0" border="0"> 

  <tr><td><a href="{{ post.url }}">{{post.type}}: {{ post.title }}</a></td>
  <td style = "text-align:right">{{post.date | date: "%-d %B"}}</td></tr>
</table>
{% endfor %}