---
layout: default
---
<div>
{% for post in site.posts %}
    <p>
    {{ post.excerpt }} <a href="{{ post.url }}">Read more...</a>
    </p>
    <hr>
{% endfor %}
</div>
