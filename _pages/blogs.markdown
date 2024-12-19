---
layout: page
title: Blog Posts
permalink: /blogs/
content-type: eg
---

<style>
.category-content a {
    text-decoration: none;
    color: #4183c4;
}

.category-content a:hover {
    text-decoration: underline;
    color: #4183c4;
}
</style>

<div>
    {% for category in site.categories %}
        <div id="{{ category | first }}">Category: {{ category | first | capitalize }}</div>
        <ul>
        {% for post in category.last %}
            <li><a href="{{post.url}}">{{ post.title }}</a></li>
        {% endfor %}
        </ul>
    {% endfor %}
    <br/>
</div>
