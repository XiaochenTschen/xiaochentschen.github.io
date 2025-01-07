---
layout: page
title: About Me
permalink: /about/
published: true
---

<div class="page" markdown="1">

{% capture page_subtitle %}
<img
    class="me"
    alt="{{ author.name }}"
    src="{{ site.author.photo | relative_url }}"
    srcset="{{ site.author.photo2x | relative_url }} 2x"
/>
{% endcapture %}

{% include page/title.html title=page.title subtitle=page_subtitle %}

## Embedded Software Engineer

As an engineer with an academic background in automotive engineering, it took me some time to transition to a different career direction. I have experience working as a development engineer in vehicle electronics, focusing on HiL testing and thermal management software development within the AUTOSAR framework. Currently, I am working as a software engineer specializing in non-AUTOSAR platform development.

Beyond my job responsibilities, I am deeply interested in MCU programming, whether using RTOS or bare-metal programming. I thoroughly enjoy exploring the details and understanding how every piece of code works.

Writing blogs has become a valuable part of my learning process. It not only reinforces my understanding but also provides an opportunity to share knowledge and help others.

</div>
