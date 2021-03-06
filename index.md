---
layout: default
title: Home
nav_order: 1
permalink: /
---

# Welcome to Geunny's Blog
{: .fs-9 }

## Geunny's Blog의 목적

업무나 스터디를 진행하다 보면 새롭게 배운 부분들이나 실수한 부분들이 생기기 마련이다. 해당 내용을 블로그에 정리함으로서 생각을 한번 더 정리하게 되고 좀 더 깊게 배울 수 있다 생각한다. 또한, 비슷한 문제에 부딪혔을때 정리한 내용들을 보면 보다 편하게 처리하기 위해 블로그 활용을 결심하게 되었다. 

---

## About the project

Just the Docs is &copy; 2017-{{ "now" | date: "%Y" }} by [Patrick Marsceill](http://patrickmarsceill.com).

### License

Just the Docs is distributed by an [MIT license](https://github.com/pmarsceill/just-the-docs/tree/master/LICENSE.txt).

#### Blog posed by...

<ul class="list-style-none">
{% for contributor in site.github.contributors %}
  <li class="d-inline-block mr-1">
     <a href="{{ contributor.html_url }}"><img src="{{ contributor.avatar_url }}" width="32" height="32" alt="{{ contributor.login }}"/></a>
  </li>
{% endfor %}
</ul>

### Code of Conduct

Just the Docs is committed to fostering a welcoming community.

[View our Code of Conduct](https://github.com/pmarsceill/just-the-docs/tree/master/CODE_OF_CONDUCT.md) on our GitHub repository.
