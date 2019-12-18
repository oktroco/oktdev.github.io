---
layout: category
title: Django REST API
category: [development]
permalink: '/category/development/0/'
---

<div class="columns is-multiline">
    <div class="column is-12">
        {% include pagination.html %}
    </div>
    {% for post in site.categories['development'] %}
    <div class="column is-12">
        {% include post-card.html %}
    </div>
    {% endfor %}
    <div class="column is-12">
        {% include pagination.html %}
    </div>
</div>