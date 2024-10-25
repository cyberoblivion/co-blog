---
layout: post
title:  "Let's Get Started!"
date:   2024-04-23 23:34:44 -0400
categories: jekyll update bootstrap
bootstrap-enabled: true
permalink: /howto/add-bootstrap-to-jekyll/
---

It wouldn’t be much of a hacker blog without some hacking, right? **Back to the HACKS!**

Today, we’re exploring Jekyll, our trusty blog platform, and seeing if we can add a bit more style and interactivity to this site by bringing in Bootstrap. 

### Why Bootstrap with Jekyll?

Turning this lightweight Jekyll site into a playground of web components and libraries? Maybe not the most conventional choice—but conventions are overrated, right?
<br/>
Adding Bootstrap is actually straightforward once you understand the Jekyll basics. It’s all about following conventions and putting files in the right places. 

### Including Bootstrap in a Basic HTML Page
For starters, adding Bootstrap to any HTML site is simple—just add a CSS link in the `<head>` and a JavaScript script at the end of the `<body>`. Here’s a basic example that uses Cloudflare as the CDN for Bootstrap:

{% highlight html %}
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>CyberOblivion Blog</title>
  <!-- Bootstrap CSS -->
  <link href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.3.3/css/bootstrap.min.css" 
    rel="stylesheet" crossorigin="anonymous">
  <!-- Custom styles to override Bootstrap -->
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

### Customizing Jekyll’s Theme Files for Bootstrap

Jekyll lets you extend or override files in your theme to customize the site’s design. You can start by locating the theme’s directory with this command:

{% highlight bash %}
bundle show <theme-name>
{% endhighlight %}

This will show the theme’s directory path, allowing you to explore the structure and understand which files you might want to override. When you copy a file from the theme into your own project (in the same folder structure), Jekyll will use your version instead of the default one. 

You can also check out the [Minima theme file structure](https://jekyllrb.com/docs/structure/) on github to see where each layout or include file lives.

#### Overriding `_layouts/default.html` and `_includes/head.html`
To include Bootstrap, we’ll override minima’s `_layouts/default.html` and `_includes/head.html` files. Copy these files from your theme directory into your project, or download them from the [Minima GitHub repository](https://github.com/jekyll/minima) (make sure to get the right version).

Let’s create a new Jekyll site and set up these overrides:

{% highlight bash %}
jekyll new new-blog
cd new-blog/
mkdir _layouts _includes
cp /path/to/gems/minima-2.5.2/_layouts/default.html _layouts/
cp /path/to/gems/minima-2.5.2/_includes/head.html _includes/
{% endhighlight %}

### Enabling Bootstrap with a Front Matter Flag

To conditionally include Bootstrap only on pages that need it, we’ll add a `bootstrap-enabled` flag in the front matter of each page that requires Bootstrap. This keeps your site lightweight by loading Bootstrap selectively.

In `_includes/head.html`, add the following code to include Bootstrap CSS only if the page’s front matter has `bootstrap-enabled: true`:

{% highlight liquid %}
{% raw %}
{% if page.bootstrap-enabled %}
  <link href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.3.3/css/bootstrap.min.css"
  rel="stylesheet" crossorigin="anonymous">
{% endif %}
{% endraw %}
{% endhighlight %}

Next, update `_layouts/default.html` to include Bootstrap’s JavaScript at the end of the body if the flag is set:

{% highlight liquid %}
{% raw %}
{% if page.bootstrap-enabled %}
  <!-- Include Bootstrap JavaScript -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.3.3/js/bootstrap.bundle.min.js" 
    integrity="sha384-whatever-key" crossorigin="anonymous"></script>
{% endif %}
{% endraw %}
{% endhighlight %}

And that’s it! Now, with the `bootstrap-enabled` flag in the front matter, you control where Bootstrap gets loaded.

### Adding Bootstrap Components
Let’s test it out by adding a Bootstrap button that triggers a modal popup. Add this button and modal code to any page:

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
        <div class="modal-body">
        <p><strong>Hacker Mode Activated!</strong></p>        
        <p>Bootstrap is now live and ready to roll. Press the Escape key or Hit "Close"</p>        
      </div>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
      </div>
    </div>
  </div>
</div>

---
<br/>
Now, let’s add some interactivity to CyberOblivion’s Jekyll blog with Bootstrap! This setup gives you the flexibility to decide which pages load the library, while keeping the design consistent and lightweight.

Ready for more? We’ll keep pushing Jekyll’s limits—stay tuned for the next hack!

{% include comments.html %}