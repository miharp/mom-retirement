---
layout: default
title: Compare Facilities
---

# Side-by-Side Comparison

[&larr; Back to all facilities]({{ '/' | relative_url }})

<div class="compare-table-wrapper">
<table class="compare-table">
  <thead>
    <tr>
      <th>Facility</th>
      <th>Care Levels</th>
      <th>Rent/mo</th>
      <th>Care/mo</th>
      <th>Total/mo</th>
      <th>FL Tax Savings/mo</th>
      <th>Effective/mo</th>
      <th>vs. Current</th>
      <th>Distance</th>
      <th>Staff</th>
      <th>Cleanliness</th>
      <th>Amenities</th>
      <th>Overall</th>
      <th>Last Visit</th>
    </tr>
  </thead>
  <tbody>
  {% assign current_fac = site.facilities | where: "current", true | first %}
  {% assign current_total = current_fac.cost.base_monthly | plus: current_fac.cost.care_monthly | plus: current_fac.cost.other_monthly %}
  {% assign candidates = site.facilities | where_exp: "f", "f.current != true" | sort: "name" %}

  {% comment %}— Current facility row first —{% endcomment %}
  {% assign f = current_fac %}
  {% assign latest_visit = site.visits | where: "facility", f.slug | sort: "date" | last %}
  {% assign total = f.cost.base_monthly | plus: f.cost.care_monthly | plus: f.cost.other_monthly %}
  <tr class="current-row">
    <td><a href="{{ f.url | relative_url }}">{{ f.name }}</a> <span class="badge-current">Current</span></td>
    <td>
      {% for level in f.care_levels %}
        <span class="badge badge-{{ level | replace: '_', '-' }}">{{ level | replace: '_', ' ' | capitalize }}</span>
      {% endfor %}
    </td>
    <td>${{ f.cost.base_monthly }}</td>
    <td>{% if f.cost.care_monthly %}${{ f.cost.care_monthly }}{% else %}&mdash;{% endif %}</td>
    <td>${{ total }}</td>
    <td class="baseline-cell">&mdash;</td>
    <td><strong>${{ total }}</strong></td>
    <td class="baseline-cell">&mdash;</td>
    <td>{% if f.distance_miles %}{{ f.distance_miles }} mi{% else %}&mdash;{% endif %}</td>
    {% if latest_visit %}
      <td>{{ latest_visit.staff_rating }}/5</td>
      <td>{{ latest_visit.cleanliness_rating }}/5</td>
      <td>{{ latest_visit.amenities_rating }}/5</td>
      <td><strong>{{ latest_visit.overall_rating }}/5</strong></td>
      <td>{{ latest_visit.date | date: "%b %-d, %Y" }}</td>
    {% else %}
      <td colspan="5"><em>Not yet visited</em></td>
    {% endif %}
  </tr>

  {% comment %}— Candidate facilities —{% endcomment %}
  {% for facility in candidates %}
    {% assign latest_visit = site.visits | where: "facility", facility.slug | sort: "date" | last %}
    {% assign total = facility.cost.base_monthly | plus: facility.cost.care_monthly | plus: facility.cost.other_monthly %}
    {% if facility.state == "FL" %}
      {% assign effective = total | minus: site.fl_tax_savings %}
    {% else %}
      {% assign effective = total %}
    {% endif %}
    {% assign vs_current = current_total | minus: effective %}
    <tr>
      <td><a href="{{ facility.url | relative_url }}">{{ facility.name }}</a></td>
      <td>
        {% for level in facility.care_levels %}
          <span class="badge badge-{{ level | replace: '_', '-' }}">{{ level | replace: '_', ' ' | capitalize }}</span>
        {% endfor %}
      </td>
      <td>${{ facility.cost.base_monthly }}</td>
      <td>{% if facility.cost.care_monthly %}${{ facility.cost.care_monthly }}{% elsif facility.cost.wellness_package_monthly %}
        <span title="Wellness package L1–L6">${{ facility.cost.wellness_package_monthly.level_1 }}–${{ facility.cost.wellness_package_monthly.level_6 }}/mo</span>
      {% else %}&mdash;{% endif %}</td>
      <td>${{ total }}</td>
      <td>{% if facility.state == "FL" %}<span class="vs-savings">-${{ site.fl_tax_savings }}</span>{% else %}&mdash;{% endif %}</td>
      <td><strong>${{ effective }}</strong></td>
      <td>
        {% if vs_current > 0 %}
          <span class="vs-savings">-${{ vs_current }}/mo</span>
        {% elsif vs_current < 0 %}
          {% assign over = 0 | minus: vs_current %}
          <span class="vs-over">+${{ over }}/mo</span>
        {% else %}
          &mdash;
        {% endif %}
      </td>
      <td>{% if facility.distance_miles %}{{ facility.distance_miles }} mi{% else %}&mdash;{% endif %}</td>
      {% if latest_visit %}
        <td>{{ latest_visit.staff_rating }}/5</td>
        <td>{{ latest_visit.cleanliness_rating }}/5</td>
        <td>{{ latest_visit.amenities_rating }}/5</td>
        <td><strong>{{ latest_visit.overall_rating }}/5</strong></td>
        <td>{{ latest_visit.date | date: "%b %-d, %Y" }}</td>
      {% else %}
        <td colspan="5"><em>Not yet visited</em></td>
      {% endif %}
    </tr>
  {% endfor %}
  </tbody>
</table>
</div>

<p class="savings-footnote">FL Tax Savings = ${{ site.fl_tax_savings }}/mo ($3,500/yr ÷ 12) estimated CA state income tax savings from relocating to Florida. Effective = Total − Tax Savings. "vs. Current" compares effective cost to Sakura Gardens.</p>
