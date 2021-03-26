---
layout: post
title: "Creating a Showcase"
author:
    name: Sean Raven
    github: dragon0
date: 2021-03-25 19:00:00 -7:00
description: "How I set up my Showcase section in Jekyll"
tags: []
---

{% raw %}

You might have noticed, but I now have a [Showcase](/showcase) section on my site. It took a bit of work, so I'm sharing the steps I took here

## What's wrong with the Projects section?

Although [GitHub's profile template](https://github.com/github/personal-website) already includes a section to list your repos, it's not very configurable. It just lists your repos, sorted either by recently updated or most starred (and I'm pretty sure it only updates when the Jekyll blog is rebuilt), and only shows the repo's title and tagline, relying on a visitor to click through and read the readme.

I wanted to be able to show off specific projects, and include a detailed write-up explaining what I did and why. I could also include media demonstrating a project in use. Lastly, some of the projects I want to show are in Organization repos rather than my personal ones.

Jekyll being Jekyll, some of this setup was a bit finicky, but overall wasn't too difficult.

## Creating a Collection

Jekyll has a [Collections](https://jekyllrb.com/docs/collections/) feature that makes it pretty straightforward to add new content types. I just needed to add a simple declaration in `_config.yml`:

```yaml
collections:
    showcase:
        output: true
        sort_by: priority
```

With this declaration, I can now add markdown files to the `_collections/` folder and access them using `site.showcase` in Liquid. The `output: true` enables generation of individual pages for each file. `sort_by: priority` is supposed to automatically sort the collection by a front matter key, but I couldn't get it to work. I tried Web searching, but ultimately resorted to sorting it manually in Liquid (more on that below).

## Creating the Showcase page

The page served at `/showcase` from `showcase.html` is pretty straightforward:

```yaml
---
layout: showcase
title: Showcase
---
```

Like the other HTML files in the template, it just has some YFM that loads a layout. The real work is in the `_layouts/showcase.html` layout file:

```html
{% include header.html %}

{% if site.layout == 'stacked' %}
  <div class="container-lg py-6 p-responsive text-center">
    {% include masthead.html metadata=false %}

    <div class="container-md f4 text-left border rounded-2 bg-white p-3 p-sm-5 mt-6">
      <p class="f5"><a href="/" class="d-flex flex-items-center">{% octicon chevron-left height:16 class:"mr-2 v-align-middle" fill:#0366d6 aria-label:Home %}Home</a></p>
      <h1 class="f00-light lh-condensed mb-5">{{ page.title }}</h1>
      {{ content }}

      <div class="d-sm-flex flex-wrap gutter-condensed mb-4">
        {% assign sshow = site.showcase | sort: "priority" %}
        {% for item in sshow limit: 6 %}
        <div class="col-sm-6 col-md-12 col-lg-6 col-xl-4 mb-3">
          {% include showcase-card.html %}
        </div>
        {% endfor %}
      </div>
    </div>
  </div>
{% else %}
  <div class="d-md-flex min-height-full {% unless site.style == 'dark' %}border-md-bottom{% endunless %}">
    <div class="flex-self-stretch {% if site.style == 'dark' %}bg-gray-dark{% else %}border-md-right border-gray-light bg-white{% endif %} col-md-5 col-lg-4 col-xl-3 px-4 px-md-6 px-lg-7 py-6">
      {% include masthead.html metadata=true %}
    </div>

    <div class="col-md-7 col-lg-8 col-xl-9 px-4 py-6 px-lg-7 border-top border-md-top-0 bg-gray-light" {% if site.style == 'dark' %}style="background-color: #2f363d !important"{% endif %}>
      <div class="mx-auto" style="max-width: 900px;">
        <div class="f4 {% if site.style == 'dark' %}text-white{% endif %} mb-6">
          <div class="f4 {% if site.style == 'dark' %}text-white{% endif %}">
            <p class="f5"><a href="{{ site.url }}{{ site.baseurl | append:'/' }}" class="d-flex flex-items-center {% if site.style == 'dark' %}text-white{% endif %}">{% octicon chevron-left height:16 class:"mr-2 v-align-middle" fill:{{ icon_color }} aria-label:Home %}Home</a></p>
            <h1 class="f00-light lh-condensed mb-5">{{ page.title }}</h1>
            {{ content }}

            <div class="d-sm-flex flex-wrap gutter-condensed mb-4">
              {% assign sshow = site.showcase | sort: "priority" %}
              {% for item in sshow limit: 6 %}
              <div class="col-sm-6 col-md-12 col-lg-6 col-xl-4 mb-3">
                {% include showcase-card.html %}
              </div>
              {% endfor %}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
{% endif %}

{% include footer.html %}
```

This file is just a modified copy of my repo template, so I'm replicating the relevant part here:

```html
{% assign sshow = site.showcase | sort: "priority" %}
{% for item in sshow limit: 6 %}
<div class="col-sm-6 col-md-12 col-lg-6 col-xl-4 mb-3">
  {% include showcase-card.html %}
</div>
{% endfor %}
```

Like I said above, I couldn't get the automatic sorting to work, so it's done manually here. But, like the other templates for Projects and Blog posts, it pretty much just loops through my new collection and includes a card template for each.

The card template in `_includes/showcase-card.html` is again copied from the repo card template:

```html
<div class="height-full text-left {% if site.style == 'dark' %}box-shadow{% else %}border border-gray-light{% endif %} bg-white rounded-1 p-3">
  <div class="d-flex flex-justify-between flex-items-start mb-1">
    <h1 class="f4 lh-condensed mb-1">
      <a href="{{ site.baseurl }}{{ item.url }}">
        {{ item.title }}
      </a>
    </h1>
  </div>
  <div class="text-gray mb-2 ws-normal">{{ item.description }}</div>
</div>
```

Like the other cards, it has a header that links to the item's dedicated page, and the item's description.

## Creating the dedicated pages

The dedicated pages are styled using a layout called "showcase-item", which is pretty much a copy of the post layout:

```html
{% include header.html %}

{% capture post_body %}
  {% if site.social_media %}
    {% assign shareable_social_media = '' | split: ',' %}

    {% for account in site.social_media %}
      {% assign service_shortname = account[0] %}
      {% assign service = site.data.social_media[service_shortname] %}

      {% if service.share_url_prefix %}
        {% assign shareable_social_media = shareable_social_media | push: service %}
      {% endif %}
    {% endfor %}

    {% assign total_shareable_services = shareable_social_media | size %}

    {% if total_shareable_services > 0 %}
      <div class="col-sm-4 col-lg-3 d-flex flex-wrap flex-items-center d-sm-block float-sm-right border rounded-2 bg-white p-3 mb-5 ml-md-5">
        <h3 class="text-gray-dark mr-3 mr-sm-0">Share</h3>
        <ul class="d-flex d-sm-block list-style-none">
          {% for service in shareable_social_media %}
            <li class="mt-sm-3">
              <a href="{{ service.share_url_prefix }}{{ page.url | prepend: site.url | prepend: site.baseurl | url_encode }}" title="Share on {{ service.name }}" class="d-flex flex-items-center">
                <div style="width:32px">{{ service.icon_svg }}</div><span class="d-none d-sm-inline-block text-gray-light">{{ service.name }}</span>
              </a>
            </li>
          {% endfor %}
        </ul>
      </div>
    {% endif %}
  {% endif %}
  <div class="article">
    {{ content }}
  </div>
{% endcapture %}

{% if site.layout == 'stacked' %}
  <div class="container-lg py-6 p-responsive text-center">
    {% include masthead.html metadata=false %}

    <div class="container-md f4 text-left border rounded-2 bg-white p-3 p-sm-5 mt-6">
      <p class="f5"><a href="/showcase" class="d-flex flex-items-center">{% octicon chevron-left height:16 class:"mr-2 v-align-middle" fill:#0366d6 aria-label:Showcase %}Showcase</a></p>
      <h1 class="f00-light lh-condensed">{{ page.title }}</h1>
      <p class="{% if site.style == 'dark' %}text-white{% else %}text-gray{% endif %} mb-5"><a href="{{ page.link }}">View this project</a></p>
      {{ post_body }}
    </div>
  </div>
{% else %}
  <div class="d-md-flex min-height-full {% unless site.style == 'dark' %}border-md-bottom{% endunless %}">
    <div class="flex-self-stretch {% if site.style == 'dark' %}bg-gray-dark{% else %}border-md-right border-gray-light bg-white{% endif %} col-md-5 col-lg-4 col-xl-3 px-4 px-md-6 px-lg-7 py-6">
      {% include masthead.html metadata=true %}
    </div>

    <div class="col-md-7 col-lg-8 col-xl-9 px-4 py-6 px-lg-7 border-top border-md-top-0 bg-gray-light" {% if site.style == 'dark' %}style="background-color: #2f363d !important"{% endif %}>
      <div class="mx-auto" style="max-width: 900px;">
        <div class="f4 {% if site.style == 'dark' %}text-white{% endif %} mb-6">
          <div class="f4 {% if site.style == 'dark' %}text-white{% endif %}">
            <p class="f5"><a href="{{ site.url }}/showcase" class="d-flex flex-items-center {% if site.style == 'dark' %}text-white{% endif %}">{% octicon chevron-left height:16 class:"mr-2 v-align-middle" fill:{{ icon_color }} aria-label:Showcase %}Showcase</a></p>
            <h1 class="f00-light lh-condensed">{{ page.title }}</h1>
            <p class="{% if site.style == 'dark' %}text-white{% else %}text-gray{% endif %} mb-5"><a href="{{ page.link }}">View this project</a></p>
            {{ post_body }}
          </div>
        </div>
      </div>
    </div>
  </div>
{% endif %}

{% include footer.html %}
```

The biggest changes are to have the back button go to the Showcase page instead of the home page, and to include a link to the project (which is declared in a page's YFM).

## Adding a short list to the home page

Like the existing info, the homepage section is just a loop to display cards. First I defined it in `_includes/spotlight.html`:

```html
<h2 {% if site.style == 'dark' %}class="text-white"{% endif %}>Spotlight</h2>
<p class="f4 mb-4 {% if site.style == 'dark' %}text-white{% else %}text-gray{% endif %}">My more interesting projects. View more in the <a href="/showcase">Showcase</a></p>
<div class="d-sm-flex flex-wrap gutter-condensed mb-4">
  {% assign sshow = site.showcase | sort: "priority" %}
  {% for item in sshow limit: 6 %}
    <div class="col-sm-6 col-md-12 col-lg-6 col-xl-4 mb-3">
      {% include showcase-card.html %}
    </div>
  {% endfor %}
</div>
```

Like the dedicated page, it just loops through the collection. And, like the dedicated page, it has to sort them manually first. Unlike the dedicated page, it only displays the first 6 items. This is the main reason I wanted the sort functionality to work: I wanted to make sure my best work was shown on the home page.

Now to display it. That was simple: just include it in `_layouts/home.html`:

```html
<div class="my-6">
  {% include spotlight.html %}
</div>
```

One last thing: display it in the masthead (`_includes/masthead.html`):

```html
<div class="d-flex flex-wrap flex-items-start {% if site.layout == 'stacked' %}flex-justify-center mt-1 mt-md-3{% endif %}">
  <a href="/showcase">Showcase</a>
</div>
```

And with that, I have a shiny new [Showcase](/showcase)!

{% endraw %}
