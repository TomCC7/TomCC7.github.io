---
layout: portfolio
title: "Projects"
permalink: /projects/
excerpt: "Personal projects in robotics, hardware and developer tools."
author_profile: false
---

<p class="lede">Personal projects in robotics, hardware and developer tools.</p>

<h2 class="archive-heading">Robotics &amp; tooling</h2>
<div class="project-grid">{% assign projects = site.projects | where: 'era', 'portfolio' | sort: 'order' %}{% for project in projects %}{% unless project.work_only or project.hide_card %}{% include portfolio-project.html project=project %}{% endunless %}{% endfor %}</div>

<h2 class="archive-heading">Earlier projects</h2>
<div class="project-grid">{% assign projects = site.projects | sort: 'date' | reverse %}{% for project in projects %}{% unless project.era == 'portfolio' %}{% include portfolio-project.html project=project %}{% endunless %}{% endfor %}</div>
