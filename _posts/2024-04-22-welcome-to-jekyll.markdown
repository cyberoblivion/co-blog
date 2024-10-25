---
layout: post
title:  "Welcome to Jekyll!"
date:   2024-04-22 23:34:44 -0400
categories: jekyll review
permalink: /review/jekyll
---
# Exploring Jekyll as the CyberOblivion Blog Platform

Welcome to my Jekyll exploration journey! CyberOblivion runs on Jekyll, a powerful static site generator that has quickly become my go-to platform for building this tech blog. I’m learning to harness its features to create a site that’s fast, flexible, and (dare I say) fun to work on. Here are some examples showing off Jekyll's built-in syntax highlighting and more.

## Why Jekyll?

Jekyll sites are simple to set up, and GitHub will host them for free as long as they’re part of a public repository. Since they’re static sites, deploying them is also incredibly easy. GitHub has automation in place to push changes to production with just a simple code merge into a specified branch and some configuration of Github Pages and Actions. Jekyll’s simplicity, seamless GitHub support, and Markdown compatibility make it an ideal platform for tech blogs, especially those that delve into code-heavy content and complex topics. 

On top of that, Jekyll offers a wide selection of themes to fit various styles and preferences. Whether it’s highlighting code snippets, organizing content with layouts and includes, or handling Markdown files, Jekyll has a lot to offer for anyone building a tech-oriented site. Below are some code examples to showcase how easy it is to integrate and display code snippets in various languages on a Jekyll site.

## Syntax Highlighting Examples

To show off some of Jekyll's syntax highlighting capabilities, here are some code examples in different languages:

### 1. HTML Example

Jekyll makes it simple to add HTML code snippets:

{% highlight html %}
<!DOCTYPE html>
<html>
<head>
  <title>CyberOblivion Blog</title>
</head>
<body>
  <h1>Exploring Jekyll</h1>
  <p>Learning the ins and outs of this platform.</p>
</body>
</html>
{% endhighlight %}

### 2. Python Example

Python snippets are formatted with ease:

{% highlight python %}
def greet(platform):
    print(f"Exploring {platform} with CyberOblivion!")
    
greet("Jekyll")
{% endhighlight %}

### 3. JavaScript Example

JavaScript code fits right in:

{% highlight javascript %}
function welcomeMessage(platform) {
    console.log(`Welcome to exploring ${platform} with CyberOblivion!`);
}

welcomeMessage("Jekyll");
{% endhighlight %}

### 4. Bash Script Example

Jekyll supports shell scripts as well:

{% highlight bash %}
#!/bin/bash
echo "Setting up Jekyll environment!"
bundle install
bundle exec jekyll serve
{% endhighlight %}

### 5. JSON Example

JSON is also easy to add for quick data representation:

{% highlight json %}
{
  "site": "CyberOblivion",
  "platform": "Jekyll",
  "purpose": "Learning and Sharing Tech"
}
{% endhighlight %}

---

## The Verdict

Jekyll’s versatility in handling syntax highlighting, its Markdown support, and customizable templates make it a great fit for CyberOblivion. However, there are a few things missing out-of-the-box, such as site analytics which are crucial for tracking site performance and user engagement. Also, features like comments and any fancy ui components. I’ve already tackled these issues with a bit of ingenuity and some plugins, and I’ll share more about how I’ve integrated those features in an upcoming post. Stay tuned for more updates as I dive deeper into Jekyll’s many features!


Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/


{% include comments.html %}