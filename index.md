---
title: Exercícios do Azure OpenAI
permalink: index.html
layout: home
---

# Exercícios do Azure OpenAI

Os exercícios a seguir foram projetados para dar suporte aos módulos no [Microsoft Learn](https://learn.microsoft.com/training/browse/?terms=OpenAI).


{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}
