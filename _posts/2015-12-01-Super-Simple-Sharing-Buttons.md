---
title: Super Simple Sharing Buttons
published: true
layout: post
disqus: yes
fbcomments: no
category: jekyll
tags: 
  - blog
  - jekyll
  - socialmedia
photo_url: /img/socialmediabuttons.jpg
description: Easily generate and add social media share buttons to your site
---

![](/img/socialmediabuttons.jpg)

### Add some share buttons to your posts

Easily generate and add social media share buttons to your site, first go here:

[simplesharingbuttons.com](https://simplesharingbuttons.com/)

go through the steps there until you get the code for your buttons

Then stick the code you get in an include  I chose `_includes/share.html`


    <div class="sharediv">
        Share this post
    <ul class="share-buttons">
      <li><a href="https://www.facebook.com/sharer/sharer.php?u=http%3A%2F%2Fjoshuacox.github.io%2F&t=" target="_blank" title="Share on Facebook" onclick="window.open('https://www.facebook.com/sharer/sharer.php?u=' + encodeURIComponent(document.URL) + '&t=' + encodeURIComponent(document.URL)); return false;"><i class="fa fa-facebook-square fa-2x"></i></a></li>
      <li><a href="https://twitter.com/intent/tweet?source=http%3A%2F%2Fjoshuacox.github.io%2F&text=:%20http%3A%2F%2Fjoshuacox.github.io%2F&via=uberthoth" target="_blank" title="Tweet" onclick="window.open('https://twitter.com/intent/tweet?text=' + encodeURIComponent(document.title) + ':%20' + encodeURIComponent(document.URL)); return false;"><i class="fa fa-twitter-square fa-2x"></i></a></li>
      <li><a href="https://plus.google.com/share?url=http%3A%2F%2Fjoshuacox.github.io%2F" target="_blank" title="Share on Google+" onclick="window.open('https://plus.google.com/share?url=' + encodeURIComponent(document.URL)); return false;"><i class="fa fa-google-plus-square fa-2x"></i></a></li>
      <li><a href="http://www.tumblr.com/share?v=3&u=http%3A%2F%2Fjoshuacox.github.io%2F&t=&s=" target="_blank" title="Post to Tumblr" onclick="window.open('http://www.tumblr.com/share?v=3&u=' + encodeURIComponent(document.URL) + '&t=' +  encodeURIComponent(document.title)); return false;"><i class="fa fa-tumblr-square fa-2x"></i></a></li>
      <li><a href="http://pinterest.com/pin/create/button/?url=http%3A%2F%2Fjoshuacox.github.io%2F&description=" target="_blank" title="Pin it" onclick="window.open('http://pinterest.com/pin/create/button/?url=' + encodeURIComponent(document.URL) + '&description=' +  encodeURIComponent(document.title)); return false;"><i class="fa fa-pinterest-square fa-2x"></i></a></li>
      <li><a href="http://www.reddit.com/submit?url=http%3A%2F%2Fjoshuacox.github.io%2F&title=" target="_blank" title="Submit to Reddit" onclick="window.open('http://www.reddit.com/submit?url=' + encodeURIComponent(document.URL) + '&title=' +  encodeURIComponent(document.title)); return false;"><i class="fa fa-reddit-square fa-2x"></i></a></li>
      <li><a href="http://www.linkedin.com/shareArticle?mini=true&url=http%3A%2F%2Fjoshuacox.github.io%2F&title=&summary=&source=http%3A%2F%2Fjoshuacox.github.io%2F" target="_blank" title="Share on LinkedIn" onclick="window.open('http://www.linkedin.com/shareArticle?mini=true&url=' + encodeURIComponent(document.URL) + '&title=' +  encodeURIComponent(document.title)); return false;"><i class="fa fa-linkedin-square fa-2x"></i></a></li>
      <li><a href="mailto:?subject=&body=:%20http%3A%2F%2Fjoshuacox.github.io%2F" target="_blank" title="Email" onclick="window.open('mailto:?subject=' + encodeURIComponent(document.title) + '&body=' +  encodeURIComponent(document.URL)); return false;"><i class="fa fa-envelope-square fa-2x"></i></a></li>
    </ul>
    </div>


add this to your CSS

    ul.share-buttons{
      list-style: none;
      padding: 0;
    }

    ul.share-buttons li{
      display: inline;
    }
    .sharediv {
        text-align: center;
        background: $bgcolor;
        color: $color;
        padding: 8px 15px;
        border-radius: 5px;

        a {
            font-weight: 700;
            color: $color;
            margin-left: 10px;

            &:hover {
                border-bottom: 1px dashed #fff;
            }
          }
      }


and put an include line on your post.html


{% raw  %}
    {% include share.html %}
{% endraw  %}

### Bam you’ve got shares

and now you’ve got some nice share buttons at the bottom of your post.
