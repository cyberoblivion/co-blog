---
layout: post
title:  "Let get started!!"
date:   2024-04-23 23:34:44 -0400
categories: jekyll update bootstrap
bootstrap-enabled: true
permalink: /howto/add-bootstrap-to-jekyll/
---

It wouldn't be much of a hacker blog without some hacking. <strong>Let's.. Go!</strong>
<p>Let's explore this Jekyll fellow and see if we can can make this basic site a bit more fun..</p>
<p>First things first </p>

It's probably a terrible idea to take this nice lightweight Jekyll site and add a bunch of web components and libraries to it
But that is exactly what I am going to do! 

This turns out to not be very hard at all if you know the basic concepts of a Jekyll site. It's all based on convention and putting the right file in the right location. 

First we should also understand that bootstrap is simple to include on any site you only need to add one css link in the head section and one javascript tag as the last element before the body tag.


A basic html page would look somehting like this. This method utilizes cloudflare as a cdn for the necessary files.

{% highlight html%}
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>CyberOblivion Blog</title>
  <!-- Bootstrap CSS -->  
  <link href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.3.3/css/bootstrap.min.css" 
    rel="stylesheet" crossorigin="anonymous">  
    <!-- other styles to override bootstrap-->
  <link href="{{ "/assets/css/custom.css" | relative_url }}" rel="stylesheet">
</head>
<body>

  <!-- Content goes here -->

  <!-- Bootstrap JavaScript Bundle -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js" 
    crossorigin="anonymous"></script>
</body>
</html>

{% endhighlight %}


To extend Jekyll sites you override files from the theme.
To see the contents of the theme and the files in the theme you can first get the them location

{% highlight bash %}
bundle show <theme-name>
{% endhighlight %}

this returns the directory location of the theme. You can then explore the directory
structure in order to understand what you want to overrride.
putting a file with the same name and location in your jekyll project will cause it to be used in place
of the file in the theme directory. This is usefull  because often you will want to use the original as
a template to custize.

You can see the minima directory structure here.

<a href="https://jekyllrb.com/docs/structure/">Minima file structure</a>

In this case we are going to override _layouts/default.html and _includes/head.html

we'll copy the files from the theme into our project folder. you can also get them from minima github repo make sure to get the correct version.<a href="https://github.com/jekyll/minima" >here.</a>

Below we'll create a new jekyll site and override the files necessary.
{%highlight bash%}
jekyll new new-blog;
cd new-blog/;
mkdir _layouts _includes;
cp /home/ben/.local/share/gem/ruby/3.3.0/gems/minima-2.5.2/_layouts/default.html _layouts/;
cp /home/ben/.local/share/gem/ruby/3.3.0/gems/minima-2.5.2/_includes/head.html _includes/;
{%endhighlight%}

Now we can add the markup for including the bootstrapp css in the _includes/head.html. In this case I thought it was
a good idea to put a page level flag to enable inclusion of bootstrap. here is code that will only
include bootstap if there is a flag in the front matter of the page bootstrap-enabled: true. 

{% highlight markdown %}
{% raw %}
{% if page.bootstrap-enabled -%}
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.3.3/css/bootstrap.min.css"
  rel="stylesheet" integrity="sha384-whatever-key" crossorigin="anonymous">
{%- endif -%}  
{% endraw %}
{% endhighlight %}

We also need to update the _layouts/default.html file to include the javascript. 
{% highlight markdown %}
{% raw %}
    {% if page.bootstrap-enabled == true %}
        <!-- Include Bootstrap 5 JavaScript -->
        <script src="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.3.3/js/bootstrap.bundle.min.js" 
         integrity="sha384-whatever-key" crossorigin="anonymous"></script>
     {%- endif -%}    
{% endraw %}
{% endhighlight %}

That should do it!

Now let's add some bootstrap to our Jekyll site. Here is a simple button that triggers a popup.
{% highlight html %}
<button type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal">
  Launch Popup
</button>

<div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="exampleModalLabel">Popup Dialog</h5>
        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
      </div>
      <div class="modal-body">
        This is a simple popup dialog using Bootstrap 5's modal component.
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
      </div>
    </div>
  </div>
</div>
{% endhighlight %}



<button type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal">
  Launch Popup
</button>


<div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="exampleModalLabel">Popup Dialog</h5>
        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
      </div>
      <div class="modal-body">
        This is a simple popup dialog using Bootstrap 5's modal component.
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
      </div>
    </div>
  </div>
</div>


<br/>

