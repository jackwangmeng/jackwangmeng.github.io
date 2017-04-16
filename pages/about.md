---
layout: page
title: About Me
description: 个人简介
keywords: 王萌
comments: true
menu: 关于我
permalink: /about/
---

Meng is my name;


## 低调的萌

* 努力和效果之间，永远有这样一段距离。成功和失败的唯一区别是，你能不能坚持挺过这段无法估计的距离。

## 联系我

* GitHub：[@jackwangmeng](https://github.com/jackwangmeng)
* 博客：[{{ site.title }}]({{ site.url }})
* Email：jackwangmeng@outlook.com

## Skill Keywords

#### Software Language Keywords
<div class="btn-inline">
    {% for keyword in site.skill_language_keywords %}
    <button class="btn btn-outline" type="button">{{ keyword }}</button>
    {% endfor %}
</div>

#### Web Developer Keywords
<div class="btn-inline">
    {% for keyword in site.skill_web_app_keywords %}
    <button class="btn btn-outline" type="button">{{ keyword }}</button>
    {% endfor %}
</div>
