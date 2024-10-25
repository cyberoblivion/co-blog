---
layout: post
title:  "Let get started!!"
date:   2024-04-23 23:34:44 -0400
categories: jekyll update bootstrap
bootstrap-enabled: true
permalink: /howto/add-bootstrap-to-jekyll/
---

It wouldn't be much of a hacker blog without some hacking. <strong>Let's.. Go!</strong>
<p>Let's explore this Jekyll fellow and see if we can can make this static site a bit more fun..</p>
<p>First things first </p>

Now let's add some bootstrap to our Jekyll site.
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
<p align="center">
  <img src="/assets/logo_final.svg" height="250px"/>
</p>

