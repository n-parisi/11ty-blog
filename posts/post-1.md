---
layout: post-layout.njk 
title: Creating a Blog With 11ty
date: 2021-03-18
tags: ['post']
---
<!-- Excerpt Start -->
This is my first post! I wanted to share my experience building this site with 11ty and Nunjucks.
<!-- Excerpt End --> 
Welcome to my blog and personal site! I hope some of the words I write here might be interesting or useful to someone one day. I have had the desire to build a small personal page like this for a while - it's basically a right of passage for people who write code for a living. 

What finally pushed me to create this site was that I was hearing a lot about the JAMstack and Netlify and it seemed interesting to me. If you don't know, JAMstack is less of a technology stack and more of a philosophy about building static sites that are served to clients and don't need to communicate with a backend. Any dynamic content is assembled when the site is generated, or retrieved by public APIs on the client's side. This is nice because it means your site is fast, and the cost to host it is basically nonexistent (or free in the case of Netlify). 

While this website is not _really_ a stellar example of the JAMstack since it doesn't make use of any  public APIs (yet), it was still a good opportunity to dip my toes into some popular JAMstack technologies. That's where [11ty](11ty.dev) comes in. 11ty is a static site generator, which means it can transform a bunch of templates and Markdown files into a bunch of HTML that you can serve as a static site. I don't really have any experience with other SSG's, but 11ty is apparently all the rage these days so this was a good chance to dip my toes in. Along with 11ty I used the [Nunjucks](https://mozilla.github.io/nunjucks/) templating language from Mozilla to create templates for the different kinds of pages on this site. Then 11ty takes my Markdown and turns it into blog posts - sweet!

Shoutouts to this article written by Jon Keeping which was crucial for helping me get started with 11ty and Nunjucks, thanks Jon!  
[https://keepinguptodate.com/pages/2019/06/creating-blog-with-eleventy/](https://keepinguptodate.com/pages/2019/06/creating-blog-with-eleventy/)

If you want to see the source code for this site, check it out [here](https://github.com/n-parisi/11ty-blog).

#### Getting Started With 11ty

Let's begin! I want to keep things brief here since there are endless resources on the web for creating a site with 11ty. My goal is not to give an exhaustive explanation, but to show how this website works. If you want a fuller tutorial, I would highly recommend Jon Keeping's article that I linked above.

The structure of this project is rather simple:
```
├── blog.njk
├── .eleventy.js
├── img/
├── _includes/
│   ├── base-layout.njk
│   ├── index-layout.njk
│   └── post-layout.njk
├── index.md
├── package.json
├── package-lock.json
├── posts
    └── post-X.md
```
#### Working with Nunjucks

#### Deploying to Netlify

#### Final Thoughts
