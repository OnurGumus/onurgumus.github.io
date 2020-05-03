---
layout: default
---
<div>
{% for post in site.posts %}

    {{ post.excerpt }}
    [Read more...]({{ post.url }})
    
{% endfor %}
</div>
