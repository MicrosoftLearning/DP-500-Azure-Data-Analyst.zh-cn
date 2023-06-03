---
title: 联机托管说明
permalink: index.html
layout: home
---

# 数据分析师练习

这些练习支持 Microsoft 课程 [DP-500：使用 Microsoft Azure 和 Microsoft Power BI 设计和实现企业级分析解决方案](https://docs.microsoft.com/training/courses/dp-500t00)

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/labs'" %}
| ILT 模块 | 实验室 |
| --- | --- | 
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

<!--

## Demos

{% assign demos = site.pages | where_exp:"page", "page.url contains '/Instructions/Demos'" %}
| Module | Demo |
| --- | --- | 
{% for activity in demos  %}| {{ activity.demo.module }} | [{{ activity.demo.title }}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}
 
-->
