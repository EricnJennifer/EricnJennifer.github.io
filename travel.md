---
layout: default
title: Eric군 Jennifer양의 여행 이야기
---
<div class="well">
    <div class="list-group">
      {% for post in site.posts %}
      {% if post.categories contains "travel" %}
      <article class="post">
        {% if post.img %}
            <a class="post-thumbnail" style="background-image: url({{"/asset/travel/" | prepend: site.baseurl | append : post.img}})" href="{{post.url | prepend: site.baseurl}}"></a>
        {% else %}
        {% endif %}
  <div class="post-content">
    <h2 class="post-title"><a href="{{post.url | prepend: site.baseurl}}">{{post.title}}</a></h2>
    <p>{{ post.content | strip_html | truncatewords: 15 }}</p>
    <span class="post-date">{{post.date | date: '%Y, %b %d'}}&nbsp;&nbsp;&nbsp;—&nbsp;</span>
    <span class="post-words">{% capture words %}{{ post.content | number_of_words }}{% endcapture %}{% unless words contains "-" %}{{ words | plus: 250 | divided_by: 250 | append: " minute read" }}{% endunless %}</span>
  </div>
</article>
      {% endif %}
      {% endfor %}
    </div>
   

    <!-- Pagination links -->
    {% if paginator.total_pages > 1 %}
    <ul class="pager">
      {% if paginator.previous_page %}
        <li class="previous">
          <a href="{{ paginator.previous_page_path | prepend: site.baseurl | replace: '//', '/' }}">&laquo;</a>
        </li>
      {% else %}
        <li class="previous disabled"><span aria-hidden="true">&laquo;</span></li>
      {% endif %}

      <li><a href="/">First</a></li>

      {% for page in (1..paginator.total_pages) %}
        {% if page == paginator.page %}
          <li class="active">
             <a>{{ page }}<span class="sr-only">(current)</span></a>
          </li>
        {% elsif page == 1 %}
          <li><a href="/">{{ page }}</a></li>
        {% else %}
          <li>
            <a href="{{ site.paginate_path | prepend: site.baseurl | replace: '//', '/' | replace: ':num', page }}">{{ page }}</a>
          </li>
        {% endif %}
      {% endfor %}

      <li><a href="/page{{ paginator.total_pages }}/">Last</a></li>

      {% if paginator.next_page %}
        <li class="next">
          <a href="{{ paginator.next_page_path | prepend: site.baseurl | replace: '//', '/' }}">&raquo;</a>
        </li>
      {% else %}
        <li class="next disabled"><span>&raquo;</span></li>
      {% endif %}
    </ul>
    {% endif %}
</div><!-- end #home -->
