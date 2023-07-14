---
layout: default
title: Archive
---

<section class="intro">
  <div class="wrap">
    <h1>Archive</h1>
  </div>
</section>

<section class="single">
  <div class="wrap">
	{% assign post_year_2 = "null" %}
    {% for post in site.posts %}
		{% assign post_year_1 = post.date | date: "%Y"%}
		{% if post_year_1 != post_year_2 %}
			{% unless post_year_2 == "null" %}
				</ul>
			{% endunless %}
    		<h3>{{ post_year_1 }}</h3>
    		<ul class="posts">
    		{% assign post_year_2 = post.date | date: '%Y' %}
		{% endif %}
      	<li>
        	<span class="post-date">{{ post.date | date: "%b %-d, %Y" }}</span>
        	<a class="post-link" href="{{ post.url | relative_url }}">{{ post.title }}</a>
      	</li>
	{% endfor %}
        </ul>
  </div>
</section>
