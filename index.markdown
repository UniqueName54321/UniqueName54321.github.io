---
layout: page
title: Home
---

# Hi! :3

I am DifferentDance8. Or UniqueName54321. Or UniqueName12345. Or Olflix. Or Bug Hayes. Or whatever you feel like calling me :P

## My Open Source Projects

Currently, they're... not really too good :P

However, I am planning on two projects: "Jaboticaba" and "LycaraOS". They're not OSS yet, but they will be.

## Blog Posts

{% assign sorted_categories = site.categories | sort %}

{% if sorted_categories.size > 0 %}
  {% for category in sorted_categories %}
### {{ category[0] | capitalize }}

<ul>
  {% assign category_posts = category[1] | sort: "date" | reverse %}
  {% for post in category_posts %}
    <li>
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
      <small> -  {{ post.date | date: "%-d %B %Y" }}</small>
    </li>
  {% endfor %}
</ul>
  {% endfor %}
{% else %}
*No posts yet. The blog void remains undisturbed.*
{% endif %}