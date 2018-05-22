---
layout: page
title: Tags
---

<p class="message">
  Tags listing? IDK if this even works...
</p>

	{% assign pages_list = site.pages %}
      {% for node in pages_list %}
        {% if node.title != null %}
          {% if node.layout == "tag" %}
            <a class="sidebar-nav-item{% if page.url == node.url %} active{% endif %}" href="{{ node.url }}">{{ node.title }}</a>
          {% endif %}
        {% endif %}
	{% endfor %}
