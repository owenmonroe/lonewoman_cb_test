---
layout: page
title: Document Groups
permalink: /groups/
---

{%- comment -%}
Lists each document group, with items grouped by group_reprint_id.
Requires CSV fields: objectid, title, group_reprint_id, reprint_type, publication
{%- endcomment -%}

{%- assign items = site.data[site.metadata] | where_exp: 'r','r.group_reprint_id' -%}
{%- comment -%} Filter out child images (only show compound_object parents) {%- endcomment -%}
{%- assign parents = items | where_exp: 'r','r.display_template == "compound_object"' -%}
{%- if parents.size == 0 -%}
  {%- assign parents = items | where_exp: 'r','r.image_parent_id == nil or r.image_parent_id == ""' -%}
{%- endif -%}
{%- if parents.size == 0 -%}
<p><em>No document groups found yet.</em></p>
{%- else -%}
  {%- assign group_ids = parents | map: 'group_reprint_id' | uniq | sort -%}

  <p class="mb-4">This index lists document groups comprised of an Original and its reprints.</p>

  <div class="cb-group-list">
  {%- for gid in group_ids -%}
    {%- assign group = parents | where: "group_reprint_id", gid -%}
    {%- assign group_heading = group.first.title | default: gid -%}

    <section class="mb-4 pb-3 border-bottom">
      <h3 class="h5">{{ group_heading }}</h3>
      <p class="text-muted small mb-2">
        <strong>Group:</strong> {{ gid }} &bull; <strong>Total:</strong> {{ group.size }}
      </p>

      <ul class="mb-0">
        {%- assign sorted_items = group | sort: 'date' -%}
        {%- for it in sorted_items -%}
          <li>
            {%- if it.reprint_type %}<span class="badge bg-secondary">{{ it.reprint_type }}</span>{%- endif %}
            <a href="{{ '/items/' | append: it.objectid | append: '.html' | relative_url }}">"{{ it.title }},"</a>
            {%- if it.publication %} {{ it.publication }}{%- endif -%}
            {%- if it.date %} <span class="text-muted">({{ it.date }})</span>{%- endif -%}
          </li>
        {%- endfor -%}
      </ul>
    </section>
  {%- endfor -%}
  </div>
{%- endif -%}
