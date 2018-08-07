---
layout:  page
title: Programming Pearls
group: thinking
---

很喜欢《Programming Pearls》这本书，因此用了这个名字。介绍一些好看好玩的编程paper、slides、video等。

{% for paper_slide in site.data.paper_slides %}
### [{{paper_slide.title}}]({{paper_slide.url}})
🔗: [{{ paper_slide.tag }}](/tags.html#{{ paper_slide.tag }})  
📝: {{ paper_slide.note }}
<hr style="width:100%"/>
{% endfor %}
