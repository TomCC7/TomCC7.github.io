---
layout: portfolio
permalink: /
title: "Che Chen"
excerpt: "Che Chen — robot learning and the software that brings models to real robots. Projects, research and open source."
author_profile: false
redirect_from:
  - /about/
  - /about.html
---

<section class="hero" aria-labelledby="intro-title">
  <div>
    <p class="eyebrow">Robotics / Software / Exploration</p>
    <h1 id="intro-title">Che Chen / <span lang="zh">陈澈</span></h1>
    <p class="lede">I’m C.C., a robotics engineer at Dimensional.</p>
    <p>I work across robot learning and robotics software.</p>
    <div class="actions"><a class="button primary" href="{{ '/projects/' | relative_url }}">Explore projects</a><a class="button" href="https://github.com/TomCC7">GitHub ↗</a></div>
  </div>
  <img src="{{ '/images/cc23.jpg' | relative_url }}" alt="Che Chen" width="220" height="260">
</section>

<section class="section" aria-labelledby="work-title">
  <div class="section-top"><h2 id="work-title">Work</h2><a href="{{ '/work/dimos/' | relative_url }}">Work details →</a></div>
  <h3>Dimensional</h3>
  <p class="eyebrow">Member of Technical Staff · June 2026–present · San Francisco</p>
  <p>I develop tools for collecting demonstrations, running learned policies and connecting them to robot hardware through <a href="https://github.com/dimensionalOS/dimos">dimOS</a>, our open-source robotics platform.</p>
</section>

<section class="section" aria-labelledby="projects-title">
  <div class="section-top"><h2 id="projects-title">Selected projects</h2><a href="{{ '/projects/' | relative_url }}">All projects →</a></div>
  <div class="project-grid">{% assign projects = site.projects | where: 'featured', true | sort: 'order' %}{% for project in projects %}{% include portfolio-project.html project=project %}{% endfor %}</div>
</section>

<section class="section" aria-labelledby="research-title">
  <div class="section-top"><h2 id="research-title">Research</h2><a href="{{ '/publications/' | relative_url }}">All publications →</a></div>
  {% assign papers = site.publications | sort: 'date' | reverse %}{% for paper in papers limit:2 %}{% include portfolio-publication.html paper=paper %}{% endfor %}
</section>
