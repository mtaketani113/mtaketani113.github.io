---
layout: default
title: タイトル一覧
description: 作成した記事を最近作成したものから並べる。
---

## 記事一覧

{% assign sorted_html_pages = site.html_pages | sort:"last_modifeid_at" | reverse %}

{% for html_page in sorted_html_pages %}
    {% if  html_page.category != '' %}
        <li><a href="{{ html_page.url }}">{{ html_page.title }}</a></li>
    {% endif %}
{% endfor %}