---
layout: page
title: Django REST API
permalink: '/category/django_rest/'
---

<div class="columns is-multiline">
    <div class="column is-12">
        {% include pagination.html %}
    </div>
    {% for post in site.categories['django_rest'] %}
    {{post.title}}
    <div class="column is-12">
        {% include post-card.html %}
    </div>
    {% endfor %}
    <div class="column is-12">
            {% include pagination.html %}
    </div>
</div>