---
layout:  page
title: 记录一些好的paper&&slides
group: thinking
---
{% for paper_slide in site.data.paper_slides %}
### [{{paper_slide.title}}]({{paper_slide.url}})
🔗: [{{ paper_slide.tag }}]({{ BASE_PATH }}{{ site.JB.tags_path }}#{{ paper_slide.tag }}-ref)  
📝: {{ paper_slide.note }}
<hr style="width:100%"/>
{% endfor %}
