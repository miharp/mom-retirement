---
layout: default
title: Facilities
---

# Assisted Living Facilities

[View side-by-side comparison &rarr;]({{ '/compare' | relative_url }})

{% assign current_fac = site.facilities | where: "current", true | first %}
{% assign current_total = current_fac.cost.base_monthly | plus: current_fac.cost.care_monthly | plus: current_fac.cost.other_monthly %}

## Current Residence

<div class="facility-card current-card">
  <h2><a href="{{ current_fac.url | relative_url }}">{{ current_fac.name }}</a> <span class="badge-current">Current</span></h2>
  <p class="card-meta">{{ current_fac.address }}</p>
  <p class="card-cost">${{ current_total }}/mo</p>
  <div class="care-levels">
    {% for level in current_fac.care_levels %}
      <span class="badge badge-{{ level | replace: '_', '-' }}">{{ level | replace: '_', ' ' | capitalize }}</span>
    {% endfor %}
  </div>
</div>

## Candidates

<div class="facility-grid">
{% assign candidates = site.facilities | where_exp: "f", "f.current != true" | sort: "name" %}
{% for facility in candidates %}
  {% assign latest_visit = site.visits | where: "facility", facility.slug | sort: "date" | last %}
  {% assign total = facility.cost.base_monthly | plus: facility.cost.care_monthly | plus: facility.cost.other_monthly %}
  {% if facility.state == "FL" %}
    {% assign effective = total | minus: site.fl_tax_savings %}
  {% else %}
    {% assign effective = total %}
  {% endif %}
  {% assign vs_current = current_total | minus: effective %}
  <div class="facility-card">
    <h2><a href="{{ facility.url | relative_url }}">{{ facility.name }}</a></h2>
    <p class="card-meta">{{ facility.address }}</p>
    {% if facility.distance_miles %}
    <p class="card-meta">{{ facility.distance_miles }} miles away</p>
    {% endif %}
    <p class="card-cost">${{ total }}/mo
      {% if facility.state == "FL" %}<span class="card-effective">&rarr; ${{ effective }}/mo effective</span>{% endif %}
    </p>
    {% if vs_current > 0 %}
    <p class="card-meta"><span class="vs-savings">Saves ${{ vs_current }}/mo vs. current</span></p>
    {% elsif vs_current < 0 %}
      {% assign over = 0 | minus: vs_current %}
    <p class="card-meta"><span class="vs-over">${{ over }}/mo more than current</span></p>
    {% endif %}
    <div class="care-levels">
      {% for level in facility.care_levels %}
        <span class="badge badge-{{ level | replace: '_', '-' }}">{{ level | replace: '_', ' ' | capitalize }}</span>
      {% endfor %}
    </div>
    {% if latest_visit %}
    <p class="card-meta" style="margin-top:0.75rem;">
      Last visit: {{ latest_visit.date | date: "%b %-d, %Y" }}
      &mdash; Overall: {{ latest_visit.overall_rating }}/5
    </p>
    {% else %}
    <p class="card-meta" style="margin-top:0.75rem;"><em>Not yet visited</em></p>
    {% endif %}
  </div>
{% endfor %}
</div>
