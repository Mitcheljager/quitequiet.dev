---
layout: default
title: "Developing developer"
---

I am a web and game developer wanting to contribute to the world through writing. This website is a personal repository of things I felt worth sharing; dev blogs, code snippets, or just about anything someone might someday find somewhat useful.

The internet is rapidly devolving through the involvement of LLMs, agents, and other tools not fit for a world made for sharing. These pages are meant to as a respite of all that trouble. Together we can make the web into something worthwhile, still. So please browse and let my quarrels be a source of whatever it is you seek.

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
