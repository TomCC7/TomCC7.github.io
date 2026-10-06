---
layout: portfolio
title: "Publications"
permalink: /publications/
excerpt: "Research on safe motion planning and control for robotic manipulators."
author_profile: false
---

<p class="lede">Research on safe motion planning and control for robotic manipulators.</p>
{% assign papers = site.publications | sort: 'date' | reverse %}{% for paper in papers %}{% include portfolio-publication.html paper=paper %}{% endfor %}
