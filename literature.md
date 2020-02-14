---
layout: page
title: Literature
permalink: /literature/
---

<style type="text/css">
    .arc-date {
        color: #999999
    }
</style>

{% for category in site.categories %}
    {% assign cate_name = category|first %}
    {% if cate_name == "literature" %}
        {% for post in category.last %}
<ul class="arc-list">
    <h4 class="arc-date">{{ post.date | date: '%B %d, %Y'}}</h4>
    <h4><a href="{{ post.url }}" target="_blank">{{ post.title }}</a></h4>
</ul>
        {% endfor %}
    {% endif %}
{% endfor %}
