---
title: "CV"
layout: archive
permalink: categories/cv
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.cv %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}