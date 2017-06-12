---
layout: page
title:  "About"
date:   2016-09-23 12:00:00
---

<img alt="silly decoration" src="https://s.gravatar.com/avatar/5cd9689d6866c557adbfc5134867ce71?s=380" />

<ul>
{% for item in site.social %}
  <li>
    <a class="post-link" href="{{ item.url }}" rel="me">
      <span class="icon  icon--{{ item.icon }}">
        <i class="fa fa-{{ item.icon }}" aria-hidden="true"></i>
      </span>

      <span class="username">{{ item.label }}</span>
    </a>
  </li>
{% endfor %}
</ul>
