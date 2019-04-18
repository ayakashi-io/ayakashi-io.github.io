---
layout: default
title: Builtin actions
parent: Reference
nav_order: 3
---

<!-- markdownlint-disable MD022 -->
# Builtin actions
{: .no_toc }
<!-- markdownlint-enable MD022 -->

<!-- markdownlint-disable MD022 -->
## Table of contents
{: .no_toc .text-delta }
<!-- markdownlint-enable MD022 -->

<ul>
    {% for member in site.data.core_actions %}
        <li>
<a href="#{{member.name}}">{{member.name}}</a>
        </li>
    {% endfor %}
</ul>
{:toc}

<div>
    {% for member in site.data.core_actions %}
    <div class="core-action">

        <h2 id="{{member.name}}">{{member.name}}</h2>
        {%- highlight js -%}
{{ member.name }}{{ member.signature }}
        {%- endhighlight -%}

        <hr>

        {%- for text_block in member.description  -%}
            {% if text_block.type == "text" %}
                <p>{{text_block.text}}</p>
            {% endif %}
            {% if text_block.type == "code" %}
                <div class="language-js highlighter-rouge">
                <div class="highlight">
                {%- highlight js -%}
{{text_block.text}}
                {%- endhighlight -%}
                </div>
                </div>
            {% endif %}
        {%- endfor -%}
    </div>
    {% endfor %}
</div>

<script>
    const codeBlocks = Array.from(document.querySelectorAll(".highlight figure"));
    codeBlocks.forEach(function(block, i) {
        const parent = block.parentNode;
        const pre = document.createElement("pre");
        pre.className += "highlight";
        pre.appendChild(block.querySelector("code"));
        parent.appendChild(pre);
        parent.removeChild(block);
    });
</script>
