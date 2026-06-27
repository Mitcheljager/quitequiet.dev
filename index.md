---
layout: default
title: "Diary of a developing developer"
---

<strong aria-role="heading" aria-level="1">Diary of a developing developer</strong>

I am a web and game developer wanting to contribute to the world through writing. This website is a personal repository of things I felt worth sharing; dev blogs, code snippets, or just about anything someone might someday find somewhat useful. I don't expect anyone to read or even find my ramblings, it's more so a personal diary of things I wish to put into words.

The internet is rapidly devolving through the involvement of LLMs, agents, and other tools not fit for a world made for sharing. These pages are meant as a respite of all that trouble. Together we can make the web into something worthwhile, still. So please browse and let my quarrels be a source of whatever it is you seek.

<strong aria-role="heading" aria-level="2">Some projects</strong>

<a href="https://workshop.codes/" target="_blank">Workshop.codes</a> - A repository for Overwatch Workshop game modes, the largest of its kind. <br>
<a href="https://macrotransactions.org/" target="_blank">Macrotransactions</a> - A personal vendetta against the predatory monetization present in modern games. <br>
<a href="https://helldiverstrainingmanual.com/" target="_blank">Helldivers Training Manual</a> - A record of the efforts of Super Earth in their effort to uphold liberty. <br>
<a href="https://gewoonboeken.nl/" target="_blank">Gewoon Boeken</a> - An aggregation of popular books and where to find them on various Dutch webshops. <br>
<a href="https://mitcheljager.github.io/svelte-confetti/" target="_blank">Svelte Confetti</a> - <a href="https://mitcheljager.github.io/svelte-tiny-slider/" target="_blank">Svelte Tiny Slider</a> - <a href="https://mitcheljager.github.io/svelte-tiny-linked-charts/" target="_blank">Svelte Tiny Linked Charts</a> - A set of tiny Svelte packages. <br>
<a href="https://github.com/Mitcheljager/deep-dead" target="_blank">Deep Dead</a> - A painfully unfinished attempt at a top-down twin-stick shooter, to return to another day. <br>
<a href="https://play.google.com/store/apps/details?id=com.quitequiet.gridsome" target="_blank" rel="noreferrer noopener">Gridsome</a> - A far too difficult mobile puzzle game, desperate for more attention.

<strong aria-role="heading" aria-level="2">Latest ramblings</strong>

{% for post in site.posts %}- [{{ post.title }}]({{ post.url }})
{% endfor %}
