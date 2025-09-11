---
layout: home
title: "我的博客"
---

# 欢迎来到我的博客 👋
这里记录学习、思考和成长，也包含我的算法之旅。

## 📚 最新文章
{% for post in site.posts limit:5 %}
- [{{ post.title }}]({{ post.url }}) <small>({{ post.date | date: "%Y-%m-%d" }})</small>
  {% endfor %}

## 🧑‍💻 算法之旅
👉 [点这里进入刷题记录](algorithm-journey/)

## 🌱 关于我
- 从土木转行到程序员
- 专注于算法、Java 后端和成长笔记  
