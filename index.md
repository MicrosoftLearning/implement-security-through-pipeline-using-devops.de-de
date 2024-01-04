---
title: Implementieren von Sicherheit über eine Pipeline mithilfe von Azure DevOps
permalink: index.html
layout: home
---

# Implementieren von Sicherheit über eine Pipeline mithilfe von Azure DevOps

Die folgenden Übungen wurden entwickelt, um die Module zur [Implementierung der Sicherheit über eine Pipeline mit DevOps](https://learn.microsoft.com/training/paths/implement-security-through-pipeline-using-devops/) zu unterstützen.

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}
