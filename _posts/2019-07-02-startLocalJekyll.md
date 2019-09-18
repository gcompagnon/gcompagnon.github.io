---
layout: post
title: "Github Pages in local"
categories: github blog
output:
  html_document:
    highlight: pygments
published: true
comments: false
tags: [github blog jekyll]    
excerpt: Jekyll is the server between Github.io Blog
---
<div class="social-media-list">
<a href="https://twitter.com/share?ref_src=twsrc%5Etfw" class="twitter-share-button" data-show-count="false">Tweet</a>
<script type="IN/Share" data-url="{{ site.url }}{{ page.url }}"></script>
<div class="fb-share-button" data-href="{{ site.url }}{{ page.url }}" data-layout="button" data-size="small"><a target="_blank" href="https://www.facebook.com/sharer/sharer.php?u={{ site.url }}{{ page.url }}" class="fb-xfbml-parse-ignore">Partager</a></div>
</div>

To start a local server (http://localhost:4000) in a folder with Jekyll Gemfile
```
bundle exec jekyll serve
```

Update gems
```
bundle update
```


To install Jekyll, ruby, and all gems:

[https://help.github.com/en/articles/setting-up-your-github-pages-site-locally-with-jekyll](https://help.github.com/en/articles/setting-up-your-github-pages-site-locally-with-jekyll)

