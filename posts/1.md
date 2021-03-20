---
layout: post-layout.njk 
title: Creating a Blog with 11ty
date: 2021-03-19
tags: ['post']
---
<!-- Excerpt Start -->
This is my first post! I wanted to share my experience building this site with 11ty and Nunjucks.
<!-- Excerpt End --> 
Welcome to my blog and personal site! I hope some of the words I write here might be interesting or useful to someone one day. I have had the desire to build a small personal page like this for a while - it's basically a rite of passage for people who write code for a living. 

What finally pushed me to create this site was that I was hearing a lot about the JAMstack and Netlify and it seemed interesting to me. If you don't know, JAMstack is less of a technology stack and more of a philosophy about building static sites that are served to clients and don't need to communicate with a back-end. Any dynamic content is assembled when the site is generated, or retrieved by public APIs on the client's side. This is nice because it means your site is fast, and the cost to host it is very small (or free in the case of Netlify). 

While this website is not _really_ a stellar example of the JAMstack since it doesn't make use of any  public APIs (yet), it was still a good opportunity to dip my toes into some popular JAMstack technologies. That's where [11ty](11ty.dev) comes in. 11ty is a static site generator, which means it can transform a bunch of templates and Markdown files into HTML that you can serve as a static site. I don't really have any experience with other SSG's, but 11ty is apparently all the rage these days so this was a good chance to try it out . Along with 11ty I used the [Nunjucks](https://mozilla.github.io/nunjucks/) templating language from Mozilla to create templates for the different kinds of pages on this site. Then 11ty takes my Markdown and turns it into blog posts - sweet!

This article written by Jon Keeping was crucial for helping me get started with 11ty and Nunjucks, thanks Jon!  
[https://keepinguptodate.com/pages/2019/06/creating-blog-with-eleventy/](https://keepinguptodate.com/pages/2019/06/creating-blog-with-eleventy/)

If you want to see the source code for this site, check it out [here](https://github.com/n-parisi/11ty-blog).

#### Getting Started With 11ty

Let's begin! I want to keep things brief here since there are endless resources on the web for creating a site with 11ty. My goal is not to give an exhaustive explanation, but to show how this website works. If you want a fuller tutorial, I would highly recommend Jon Keeping's article that I linked above.

The structure of this project is pretty simple:
```
├── blog.njk
├── .eleventy.js
├── static/
├── _includes/
│   ├── base-layout.njk
│   ├── index-layout.njk
│   └── post-layout.njk
├── index.md
├── posts
    └── *.md
```
I'll provide a high level explanation of each top-level file and directory. Check out the repo for more details.

- **blog.njk**
: Nunjucks template file that describes the `/blog` page. It enumerates over the collection of posts and creates a listing for each one with a date and excerpt. 

- **static/**
: Resources directory.

- **_includes/**
: Template files for the homepage and blog page, and a base template with some HTML boilerplate. This folder tells 11ty to include these templates in the build but not build pages from them. 

- **index.md**
: The content in this file is turned into the homepage using the `index-layout`.

- **posts/**
: Each Markdown file in this folder is turned into a blog post using the `post-layout`.

- **.eleventy.js**
: The configuration file for 11ty. Here we tell 11ty to include the `img/` folder, and also create a filter for displaying datetime.

With this setup, I can create new posts by adding Markdown files to the `posts/` folder and 11ty turns them into blog post pages when it generates my site. GitHub is the CMS, and I write the posts in vim. 

#### Working with Nunjucks

Nunjucks is a powerful templating language that I barely scratched the surface of. I mostly used the templates to create reusable boilerplate for different pages. There are a few places where I used Nunjucks to generate some simple dynamic content. This is possible with 11ty's collections since all blog posts share the `tags: ['post']` frontmatter.

On the homepage we use Nunjucks to display links to the last 5 blog posts:
```
{% raw %}
{% for post in collections.post | reverse %}
  {% if loop.index0 < 5 %}
  <li>
    <a href="{{ post.url | url }}">{{ post.data.title }}</a> -
    <i><time datetime="{{ post.date | dateIso }}">{{ post.date | dateReadable }}</time></i>
  </li>
  {% endif %}
{% endfor %}
{% endraw %}
```

And on the blog page we display a list of blog posts with dates and excerpts:
```
{% raw %}
{% for post in posts %}
  <article>
    <h2>{{ post.data.title }}</h2>
	<time datetime="{{ post.date | dateIso }}">{{ post.date | dateReadable }}</time>

	{% excerpt post %}

	<a href="{{ post.url | url }}"
   		aria-label="Read more on {{ post.data.title }}">Read more</a>
 </article>
{% endfor %}
{% endraw %}
```

It's nothing earth-shattering but it's nice to know my pages will always display up to date links and information as I add content to the site. I hope to find other useful applications of Nunjucks in the future!

#### Deploying to Netlify

[Netlify](https://netlify.com) is an awesome service for deploying static sites. For low traffic, personal pages like this one it's essentially free. It's a lot like using AWS S3 to host a static site, but Netlify is a little more streamlined and has easy support for things like form handling. I found it really easy to get setup with Netlify - just login with GitHub and select your repo. Netlify detected that I was using 11ty and I didn't need to configure anything, it built and deployed my site and gave me a domain name, which I then replaced with this one. 

#### Final Thoughts

It was fun exploring the surface of 11ty and Nunjucks. The site is pretty simple, but I'm happy with the result and how usable it is for me. To do some basic styling and formatting, I used the classless CSS framework [new.css](http://newcss.net). One day, I'm going to spend time on CSS proper. Today, I'm satisfied with what new.css does for me.

I hope this post gave you an idea of how 11ty, Nunjucks, and Netlify can be used to deploy a simple personal site. In the future I'd like to add some more dynamic content to the site - look for that in a future post!
