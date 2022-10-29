---
layout: default
---

[RuSTy](https://github.com/ghaith/rusty) is a Structured Text (IEC-61131-3) LLVM Compiler  written in Rust
<h1>Latest Posts</h1>

<ul>
  {% for post in site.posts %}
    <li>
      <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
      {{ post.excerpt }}
    </li>
  {% endfor %}
</ul>
