---
layout: default
title: "Developing developer"
---

<strong aria-role="heading" aria-level="1">Diary of a developer</strong>

I am a web and game developer wanting to contribute to the world through writing. This website is a personal repository of things I felt worth sharing; dev blogs, code snippets, or just about anything someone might someday find somewhat useful. I don't expect anyone to read or even find my ramblings, it's more so a personal diary of things I wish to put into words.

The internet is rapidly devolving through the involvement of LLMs, agents, and other tools not fit for a world made for sharing. These pages are meant as a respite of all that trouble. Together we can make the web into something worthwhile, still. So please browse and let my quarrels be a source of whatever it is you seek.

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
