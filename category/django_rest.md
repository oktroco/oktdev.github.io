---
layout: category
title: Django REST API
category: django_rest
permalink: '/category/django_rest/0/'
---

<div class="columns is-multiline">
    <div class="column is-12">
        {% include pagination.html %}
    </div>
    {% for post in site.categories['django_rest'] %}
    <div class="column is-12">
        {% include post-card.html %}
    </div>
    {% endfor %}
    <div class="column is-12">
        {% include pagination.html %}
    </div>
</div>