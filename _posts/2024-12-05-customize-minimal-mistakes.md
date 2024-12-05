---
title: "How to Customize Minimal Mistakes Theme: Personal Notes"
date: 2024-12-05
categories:
  - Blog
tags:
  - Jekyll
  - Web Development
  - Minimal Mistakes
---

I recently spent some time customizing my Jekyll website using Minimal Mistakes theme and learned a few neat tricks. Here's what I figured out:

### Removing the Site Title from Header

First thing I wanted to do was get rid of that site title in the header - you know, that text that shows up in the upper left corner? There are actually two ways to do this:

1. The CSS way - adding some code to `main.scss`. This works but isn't the best solution.
2. The better way - modifying the masthead template directly.

For the better solution, you need to:
1. Create an `_includes` folder in your root directory
2. Create a `masthead.html` file inside it
3. Copy the original masthead code and comment out the title section

The key part to comment out is:
```html
{% comment %}
<a class="site-title" href="{{ '/' | relative_url }}">
  {{ site.masthead_title | default: site.title }}
  {% if site.subtitle %}<span class="site-subtitle">{{ site.subtitle }}</span>{% endif %}
</a>
{% endcomment %}
```

### Adding a News Collection

Next up was creating a special section for news posts. This is super useful for things like announcing new papers or career updates. Here's what you need:

1. Set up the collection in `_config.yml`:
```yaml
collections:
  news:
    output: true
    permalink: /:collection/:path/
```

2. Create a `_news` folder for your news posts
3. Make a news layout that organizes posts by year

The cool thing about news posts is they're like regular blog posts but separated into their own section. Each news post just needs some basic front matter:
```yaml
---
title: "Your News Title"
date: 2024-12-04
excerpt: "A brief description"
---
```

### Pro Tips

- File naming matters! Always use the `YYYY-MM-DD-title.md` format for your posts
- When debugging, you can add temporary debug code to check if Jekyll sees your posts
- The front matter order doesn't matter, but make sure you have at least a title and date
- Categories and excerpts are optional but make your posts look nicer

And that's it! Hope this helps someone else trying to customize their Jekyll site. Happy coding! 🚀