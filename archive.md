---
layout: page
title: Archive
---

{% for post in site.posts %}

{% capture currentyear %}{{ post.date | date: "%Y" }}{% endcapture %}
{% if currentyear != year %}

{{currentyear | strip_html}}

{% capture year %}{{ currentyear }}{% endcapture %}

{% endif %}

<table cellpadding="0" cellspacing="0" border="0"> 

  <tr><td><a href="{{ post.url }}">{{ post.title }}</a></td>
  <td style = "text-align:right">{{post.date | date: "%-d %B"}}</td></tr>
</table>
{% endfor %}