---
title: "Shortcode to Insert Raw HTML in Hugo"
date: 2019-12-05T23:31:46+07:00
tags: ["hugo", "markdown", "tricks", "shortcode","blog"]
author: "kn"
description: "Shortcode to Insert Raw HTML in Hugo"
cover: "posts/Shortcode-to-Insert-Raw-HTML-in-Hugo/images/gohugoio-card.png"
---

Hugo is very good framework of creating a website by using markdown. 
But there are times when Markdown falls short. Often, content authors are forced to add raw HTML (e.g., video `<iframes>`) to Markdown content. 

But Hugo does not seem to have a good way to let you do this in a markdown file.
They created shortcodes to circumvent these limitations.

A shortcode is a simple snippet inside a content file that Hugo will render using a predefined template. Note that shortcodes will not work in template files. If you need the type of drop-in functionality that shortcodes provide but in a template, you most likely want a partial template instead.

Here is how to create your own one-line custom shortcode to make that possible.
Add a shortcode template to your site, in `layouts/shortcodes/rawhtml.html`, with the content:

```
<!-- raw html -->
{{.Inner}}
```

This template tells Hugo to simply render the inner content passed to your shortcode directly into the HTML of your site.
You can then use this shortcode in your markdown content, like so:

```html
{{</* rawhtml */>}}
  <iframe src="https://3dwarehouse.sketchup.com/embed/530678b3-058e-4d3c-a09d-a2a6294247da" frameborder="0" scrolling="no" marginheight="0" marginwidth="0" width="580" height="326" allowfullscreen></iframe>
  <p> thanks to @kn </p>
{{</* /rawhtml */>}}
```