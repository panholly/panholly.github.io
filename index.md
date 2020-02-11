---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: page
---
<style type="text/css">

.arc-date {
    color: #999999
}
</style>

{% for category in site.categories %}
<!-- <h3>{{ category | first }}</h3> -->
<!-- </span>{{ category | last | size }}</span> -->
<ul class="arc-list">
    {% for post in category.last %}
        <!-- <p>{{ post.date | date:"%Y-%m-%d %H:%M:%S"}}</p> -->
        <h4 class="arc-date">{{ post.date | date: '%B %d, %Y'}}</h4>
        <h4><a href="{{ post.url }}" target="_blank">{{ post.title }}</a></h4>
    {% endfor %}
</ul>
{% endfor %}