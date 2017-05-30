---
layout: page
title: Research
quote: "Pay attention to your enemies, for they are the first to discover your mistakes."
quote-author: Antisthenes
---

<h2>Timeline of Research Topics:</h2>


{% assign historyposts = 
	(site.posts | where: "layout" , "history" | reverse ) %}

{% for post in historyposts %}

{% capture currentgroup %}{{ post.group }}{% endcapture %}
{% if currentgroup != lastgroup %}

{{currentgroup | strip_html}}

{% capture lastgroup %}{{ currentgroup }}{% endcapture %}

{% endif %}

<table cellpadding="0" cellspacing="0" border="0"> 
  <tr><td><a href="{{ post.url }}">{{ post.title }}</a></td>
  <td style = "text-align:right">{{ post.timeline }}</td>
  </tr>
</table>

{% endfor %}