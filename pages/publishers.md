---
layout: page
title: Publishers
permalink: /publishers/
---

{%- comment -%}
Lists items grouped by publisher (publication), A→Z.
Assumes CSV has `publication`, `title`, `objectid`, optionally `date`.
{%- endcomment -%}

{%- assign all = site.data[site.metadata] -%}

{%- comment -%} Filter to only items that have a publication value {%- endcomment -%}
{%- assign with_creator = all | where_exp: 'r', 'r.publication and r.publication != ""' -%}

{%- comment -%} Group by publication and sort groups by name {%- endcomment -%}
{%- assign groups = with_creator | group_by: 'publication' | sort: 'name' -%}

{%- if groups.size == 0 -%}
<p><em>No publishers found.</em></p>
{%- else -%}

  {%- comment -%} Optional A–Z jump list {%- endcomment -%}
  <p class="small mb-3">
    Jump to:
    {%- assign seen = "" -%}
    {%- for g in groups -%}
      {%- assign letter = g.name | strip | upcase | slice: 0,1 -%}
      {%- unless seen contains letter -%}
        <a href="#pub-{{ letter }}">{{ letter }}</a>{% if forloop.last == false %} · {% endif %}
        {%- assign seen = seen | append: letter -%}
      {%- endunless -%}
    {%- endfor -%}
  </p>

  {%- assign current_letter = "" -%}
  {%- for g in groups -%}
    {%- assign name = g.name | strip -%}
    {%- assign letter = name | upcase | slice: 0,1 -%}

    {%- if letter != current_letter -%}
      <h2 id="pub-{{ letter }}" class="mt-4">{{ letter }}</h2>
      {%- assign current_letter = letter -%}
    {%- endif -%}

    <section class="mb-3">
      <h3 class="h5">{{ name }} <span class="text-muted">({{ g.items | size }})</span></h3>
      <ul class="mb-0">
        {%- assign sorted_items = g.items | sort: 'title' -%}
        {%- for it in sorted_items -%}
          <li>
            <a href="{{ '/items/' | append: it.objectid | append: '.html' | relative_url }}">"{{ it.title }}"</a>
            {%- if it.date %} <span class="text-muted">({{ it.date }})</span>{% endif -%}
          </li>
        {%- endfor -%}
      </ul>
    </section>
  {%- endfor -%}
{%- endif -%}
