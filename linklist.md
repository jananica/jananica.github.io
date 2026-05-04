---
layout: listing
---
## Link list

This is a list of websites I found useful etc., primarily for me to look them up quickly.

{% for topic in site.data.linklist %}

### {{topic.topic}}

{% for site in topic.sites %}
- [{{site.site.name}}]({{site.site.url}})
{{site.site.description}}
   

{% endfor %}


{% endfor %}