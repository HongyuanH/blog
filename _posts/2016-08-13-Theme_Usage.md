---
layout:            post
title:             "JekyllDecent Usage/Features"
menutitle:         "JekyllDecent Usage"
date:              2016-08-13
tags:              Jekyll Theme
category:          Others
author:            hongyuan
redirect_from:     "/YAML-Features-Redirect/"
cover:             /assets/mountain-alternative-cover.jpg

---

This is just a summary of the posts from the original [jekyllDecent template](http://jwillmer.github.io/jekyllDecent/blog/readme/Readme) explaining/demonstrating the theme usage/features.


## Screenshots

<div class="album">

<figure>
<img src="{{ site.github.url }}/res/2016-08-13-Theme_Usage/2016-06-08-Readme-front-page-previewe.jpg" />
<figcaption>Frontpage</figcaption>
</figure>

<figure>
<img src="{{ site.github.url }}/res/2016-08-13-Theme_Usage/2016-06-08-Readme-post-preview.jpg" />
<figcaption>Post</figcaption>
</figure>

<figure>
<img src="{{ site.github.url }}/res/2016-08-13-Theme_Usage/2016-06-08-Readme-content-preview.jpg" />
<figcaption>Demo</figcaption>
</figure>

<figure>
<img src="{{ site.github.url }}/res/2016-08-13-Theme_Usage/2016-06-08-Readme-about-preview.jpg" />
<figcaption>About Page</figcaption>
</figure>

</div>


## User Content

Blogposts can be written in [Markdown](https://de.wikipedia.org/wiki/Markdown).

- The folder for blog content is `_posts`
- For author details you need to modify `_data/authors.yml`
- For site properties (like the name) you need to modify `_config.yml` and `robots.txt`
- For the about page you need to modify the `about.md` in `_pages`

After modifying `*.yml` files you need to restart jekyll to take effect.

## YAML Features

Following (additional) features are supported in the header ([YAML Front Matter](https://jekyllrb.com/docs/frontmatter/)) of each post.

```bash
---
title:         Example      #Page/post title
author:        jwillmer     #Page/post author
cover:         /image.jpg   #Optional: Posibillity to change cover on a post/page
redirect_from: /foo         #Optional: Redirect url
visible:       false        #Optional: Hide page from listing in the menu.
weight:        5            #Optional: Influence sorting of pages in the menu
menutitle:     Offline      #Optional: Use a secondary name in the menu/post list
tags:          hallo welt   #Optional: Will be displayed as tags and as keywords in posts
keywords:      hallo welt   #Optional: Will add keywords to a page
language:      en           #Optional: Will set a specific language of the page
---
```

In this short post I changed the title that is displayed on the front page from `YAML Custom Features` to `YAML Features`.


```bash
---
title:             "YAML Custom Features"
menutitle:         "YAML Features"
---
```

I also added a redirect to this post. If you browse to [YAML-Feature-Redirect]({{ site.github.url }}/YAML-Features-Redirect) you should be redirected to this page. This only works if you have not removed the plugins.

```bash
---
redirect_from:     "/YAML-Features-Redirect"
---
```

If you like to redirect from multible sources you can specify it as an array.

```bash
---
redirect_from:  
  - "/YAML-Features-Redirect/"
  - "/blog/old-category/YAML-Features/"
---
``````

I also changed the cover image for this block via YAML.

```bash
---
cover:         /assets/mountain-alternative-cover.jpg
---
``````

To generate keywords for the search engines I use the tags that you specify in the post. If you are writing a page you need to specify keywords instead of tags.

```bash
---        
tags:          Jekyll YAML Features Explained  #Only used in posts!
keywords:      Jekyll YAML Features Explained  #Only used in pages!
---
```

Post will be sorted by category on the front page. This is how you define the category in YAML.

```bash
---        
category:     Readme
---
```

On a page I have additional options. For instance I can hide the page from the menu by setting the `visible` tag to `false`.

```bash
---        
visible:       false     
---
```

If I like to have the page in the menu I can add `weight` to the page in order to specify a position in the menu.

```bash
---        
weight:       5  
---
```

The default language of your blog is defined in the `_config.yml` file but if you like to write a post/page in another language you can use the `language` attribute. This will specify that you are using another language on this page for search engines. Please use on of the [language codes](http://www.w3schools.com/tags/ref_language_codes.asp) as value. 

```bash
---        
language:       en  
---
```

Additional features, that can be specified, can be found in the [YAML Front Matter documentation](https://jekyllrb.com/docs/frontmatter/).


## Theme Features

The features described in this section are specific for this template. All other language features can be found in the [kramdown documentation](http://kramdown.gettalong.org/syntax.html).

### Image Features


#### Parallax Effect

Keep in mind that parallax effect will not be captured if you like to print the page.

```html
<div class="bg-scroll" style="background-image: url('{{ site.github.url }}/res/2016-08-13-Theme_Usage/mountain1.jpg')"></div>
```

<div class="bg-scroll" style="background-image: url('{{ site.github.url }}/res/2016-08-13-Theme_Usage/mountain1.jpg')"></div>


#### Caption for Image

```html
<figure>
   <img src="{{ site.github.url }}/res/2016-08-13-Theme_Usage/mountain2.jpg" />
   <figcaption>A nice mountain.</figcaption>
</figure>
```

<figure>
<img src="{{ site.github.url }}/res/2016-08-13-Theme_Usage/mountain2.jpg" />
<figcaption>A nice mountain</figcaption>
</figure>

#### Image Allignment

```html
![]({{ site.github.url }}/res/2016-08-13-Theme_Usage/mountain3.gif#right)
![]({{ site.github.url }}/res/2016-08-13-Theme_Usage/mountain2.gif#left)
```

Allignment with caption

```html
<aside>
   <figure class="left">
      <img src="{{ site.github.url }}/res/2016-08-13-Theme_Usage/mountain3.jpg#left" />
      <figcaption>What a view!</figcaption>
   </figure>
</aside>
```

![]({{ site.github.url }}/res/2016-08-13-Theme_Usage/mountain2.jpg#right)

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Praesent accumsan ante nulla, quis pulvinar nibh tempus sed. In congue congue odio, vel ornare mauris ultrices vel. Vestibulum tristique eros at enim vulputate fringilla. Nullam non augue sit amet elit interdum tempus non ut justo. 

<aside>
<figure class="left">
<img src="{{ site.github.url }}/res/2016-08-13-Theme_Usage/mountain3.jpg" />
<figcaption>What a view!</figcaption>
</figure>
</aside>

Phasellus ut ipsum id leo sagittis pretium a quis neque. Maecenas rutrum lectus id magna malesuada, non dapibus neque tincidunt. Suspendisse ultrices accumsan eros, sit amet facilisis quam hendrerit a. Integer sed felis et diam efficitur accumsan. Suspendisse facilisis lectus non orci mattis vestibulum. Suspendisse molestie vulputate nunc non tincidunt. Maecenas vulputate, mauris ut rhoncus vulputate, tellus augue aliquet nibh, vel egestas nulla ipsum bibendum lorem. Pellentesque posuere laoreet lectus eget luctus. Vestibulum mattis ut ligula sed sodales. Vestibulum sit amet viverra arcu.


#### Fullscreen image

```html
<div class="large">
   ![]({{ site.github.url }}/res/2016-08-13-Theme_Usage/mountain2.jpg)
</div>
```

With caption

```html
<figure class="large" markdown="1">   
   ![]({{ site.github.url }}/res/2016-08-13-Theme_Usage/mountain2.jpg)
   <figcaption>On top of the mountain!</figcaption>
</figure>
```

<figure class="large" markdown="1">
   ![]({{ site.github.url }}/res/2016-08-13-Theme_Usage/mountain2.jpg)
   <figcaption>On top of the mountain!</figcaption>
</figure>

#### Image Gallery

```html
<div class="album">
   ![]({{ site.github.url }}/res/2016-08-13-Theme_Usage/Screenshot_2016-04-09-19-16-28.png)
   ![]({{ site.github.url }}/res/2016-08-13-Theme_Usage/Screenshot_2016-04-02-00-48-25.png)
   ![]({{ site.github.url }}/res/2016-08-13-Theme_Usage/Screenshot_2016-04-01-12-03-36.png)
   ![]({{ site.github.url }}/res/2016-08-13-Theme_Usage/Screenshot_2016-04-01-12-01-33.png)
   ![]({{ site.github.url }}/res/2016-08-13-Theme_Usage/Screenshot_2016-03-24-12-13-58.png)
   ![]({{ site.github.url }}/res/2016-08-13-Theme_Usage/Screenshot_2016-03-17-22-50-05.png)
</div>
```

With caption

```html
<div class="album">
   // ...
   <figure>
      <img src="{{ site.github.url }}/res/2016-08-13-Theme_Usage/mountain2.jpg" />
      <figcaption>On top of the mountain!</figcaption>
   </figure>
   <figure>
      <img src="{{ site.github.url }}/res/2016-08-13-Theme_Usage/mountain3.jpg" />
      <figcaption>What a view</figcaption>
   </figure>
</div>
```

<div class="album">
   <figure>
      <img src="{{ site.github.url }}/res/2016-08-13-Theme_Usage/mountain1.jpg" />
      <figcaption>Mountain and lake</figcaption>
   </figure>   
   <figure>
      <img src="{{ site.github.url }}/res/2016-08-13-Theme_Usage/mountain2.jpg" />
      <figcaption>On top of the mountain!</figcaption>
   </figure>   
   <figure>
      <img src="{{ site.github.url }}/res/2016-08-13-Theme_Usage/mountain3.jpg" />
      <figcaption>What a view</figcaption>
   </figure>
</div>

### Sourcecode Features

With language highlighting

```html
    ```html
    <script>
        var decentThemeConfig = {
            ga: 'YOUR TRACK ID'
        };
    </script>
    ```
```

```html
<script>
     var decentThemeConfig = {
         ga: 'YOUR TRACK ID'
     };
</script>
```

With language highlighting, line numbers and line highlighting


```html
   ```javascript{:.line-numbers}
   Array.prototype.uniq = function () {
       var map = {};
       return this.filter(function (item) {
           if (map[item]) {
               return false;
           } else {
               map[item] = true;
               return true;
           }
       });
   };
   ```
```

```javascript{:.line-numbers}
   Array.prototype.uniq = function () {
       var map = {};
       return this.filter(function (item) {
           if (map[item]) {
               return false;
           } else {
               map[item] = true;
               return true;
           }
       });
   };
```

### Author in quote

```
> Our destiny offers not the cup of despair, but the chalice of opportunity. So let us seize it, not in fear, but in gladness.
> 
> <cite>-- R.M. Nixon</cite>
```

> Our destiny offers not the cup of despair, but the chalice of opportunity. So let us seize it, not in fear, but in gladness.
> 
> <cite>-- R.M. Nixon</cite>

### 404 Page

The 404 page has a fuzzy search implemented that lists urls that are similar to the entered url. Try it out: [Unknown URL]({{ site.github.url }}/features)

### JSON API

The theme offers a JSON API for the blog posts. You can query all blog posts via: [`/api/posts.json`]({{ site.github.url }}/api/posts.json)

### PDF and PowerPoint integration

```html
<iframe src='https://view.officeapps.live.com/op/embed.aspx?src=http://img.labnol.org/di/PowerPoint.ppt' frameborder='0'></iframe>
```

<iframe src='https://view.officeapps.live.com/op/embed.aspx?src=http://img.labnol.org/di/PowerPoint.ppt' frameborder='0'></iframe>

### More

More formating features can be found in the [Kramdown syntax](http://kramdown.gettalong.org/syntax.html).


## License

The theme is released under **[The MIT License (MIT)](https://github.com/jwillmer/jekyllDecent/blob/gh-pages/LICENSE)**.

    The MIT License (MIT)

    Copyright (c) 2016 Jens Willmer

    Permission is hereby granted, free of charge, to any person obtaining a copy
    of this software and associated documentation files (the "Software"), to deal
    in the Software without restriction, including without limitation the rights
    to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
    copies of the Software, and to permit persons to whom the Software is
    furnished to do so, subject to the following conditions:
    
    The above copyright notice and this permission notice shall be included in all
    copies or substantial portions of the Software.
    
    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
    IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
    FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
    AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
    LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
    OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
    SOFTWARE.    
