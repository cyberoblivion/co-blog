---
layout: post
title:  "How to Add Vue.js to Jekyll"
date:   2024-10-28 11:34:44 -0400
categories: jekyll update vuejs
permalink: /howto/add-vuejs-to-jekyll/
author: "Ben Erridge"
description: "Discover how to integrate Vue.js into your Jekyll site for adding dynamic elements, such as live previews and interactive components. This guide explores the essentials, benefits, and challenges of enhancing static Jekyll pages with Vue.js."
---


<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/3.2.45/vue.global.prod.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.21/lodash.min.js"></script> <!-- debounce CDN -->

## Experimenting with Vue.js in Jekyll: Can You Make Your Blog Interactive?

If you've ever thought about adding more interactivity to your Jekyll blog, you might be tempted to try Vue.js. Is it a natural fit? Not exactly; but it’s doable, and it’s a fun way to explore what Vue has to offer without fully committing to a single-page application setup.

In this post, we’ll walk through the essentials of adding Vue to Jekyll to see what’s possible, what challenges come with it, and whether the added functionality is worth the effort. Vue can be used to add interactive touches think live previews, galleries, and even animated tables to a Jekyll site. But, like any experiment, it comes with pros and cons.


<div class="info-panel">
  <div class="info-icon">&#8505;</div>
      <div class="info-content">
      <strong>Note:</strong>
          We've already mastered <a href="{{ site.baseurl }}/howto/add-bootstrap-to-jekyll/">adding Bootstrap to Jekyll.</a>
  </div>
</div>

Let's get into the code and start crafting a more interactive, responsive experience one Vue component at a time.


### Setting Up Vue for Jekyll Pages

If you were building a standalone Vue project, you’d typically scaffold it with the following command:
{% highlight bash %}
  npm create vue@latest
{% endhighlight %}

This command sets up a full Vue project with options for tools like Vue Router, Pinia for state management, Vitest for testing, and more. However, here we’ll keep things simple by using Vue directly in our HTML and Markdown pages with no need for bundling or compiling.

### Inline Setup of Vue.js in Jekyll
Instead of configuring Vue through a front matter flag (like we did with Bootstrap), we’ll keep everything within this Markdown post. This approach works well for quick demos and makes it easy to see changes without extra setup.

### Live Markdown Editor with Vue.js
To showcase Vue’s capabilities, let’s build a simple markdown editor. The editor will include:

1. A Markdown input; where users can type markdown.
2. A Live Preview; showing the formatted markdown in real-time.

We'll also use **Lodash** for debouncing input changes and **Marked.js** to render markdown into HTML.

**Here's what it looks like:**  
<div id="app">    
    <div class="editor">
      <strong>Markdown Editor:</strong> type markdown in this input and the output area will automatically update
      <textarea class="input" v-model="input"></textarea>      
      <br/>
      <strong>Preview:</strong>
      <div class="output preview" v-html="output"></div>
    </div>  
</div>
  
<script>
    const { createApp } = Vue;
  
    const app = createApp({
      data() {
        return {
            input: `# Welcome to CyberOblivion 🚀

## Explore Markdown + Vue

- **Bold** text, _italic_ text
- Code samples: \`console.log('Hello World')\`
- [Visit CyberOblivion](https://blog.cyberoblivion.com)

### Why Markdown?
Markdown makes it easy to format text and is a perfect fit for tech blogs.

> "Explore. Learn. Level Up. Innovate."`
        };
      },
      computed: {
        output() {
          return marked.parse(this.input);
        }
      },
      methods: {
        update: _.debounce(function(e) {
          this.input = e.target.value;
        }, 100)
      }
    });        
    app.mount('#app');
</script>

<style>
  .preview {
    border: 1px solid;
  }
  textarea {
    width: 100%; 
    height: 225px;
  }
  #app {
    padding: 15px;
  }
</style>

### Adding the Vue.js Script
**Here's the code**
or try it [on CodePen](https://codepen.io/Ben-Erridge/pen/mdNxzVB)
```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/3.2.45/vue.global.prod.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.21/lodash.min.js"></script> <!-- debounce CDN -->

<div id="app">    
    <div class="editor">
      <strong>Markdown</strong>
      <textarea class="input" v-model="input"></textarea>      
      <br/>
      <strong>Preview:</strong>
      <div class="output preview" v-html="output"></div>
    </div>  
</div>
  
<script>
    const { createApp } = Vue;
  
    const app = createApp({
      data() {
        return {
            input: `# Welcome to CyberOblivion 🚀

## Explore Markdown + Vue

- **Bold** text, _italic_ text
- Code samples: \`console.log('Hello World')\`
- [Visit CyberOblivion](https://blog.cyberoblivion.com)

### Why Markdown?
Markdown makes it easy to format text and is a perfect fit for tech blogs.

> "Explore. Learn. Level Up. Innovate."`
        };
      },
      computed: {
        output() {
          return marked.parse(this.input);
        }
      },
      methods: {
        update: _.debounce(function(e) {
          this.input = e.target.value;
        }, 100)
      }
    });        
    app.mount('#app');
</script>
```
### Explanation of the Code
1. Binding Data with `v-model` on the `<textarea>`
The directive `v-model="input"` connects the `input` data property to the `<textarea>`. As users type, the `input` property updates in real time, ensuring changes reflect immediately.

2. Using a Computed Property for Instant Updates
The `output` computed property transforms the markdown in `input` into HTML. This property is reactive, meaning that any change to `input` automatically updates `output`; no extra function calls needed.

3. Rendering HTML with the `v-html` Directive
Vue’s `v-html` directive displays `output` as HTML inside the preview area. It’s perfect for rendering raw HTML safely, bypassing Vue’s default text rendering for a direct, styled preview of the markdown.

4. Parsing Markdown with `marked`
The `marked.parse()` function from the Marked.js library converts markdown syntax from `input` into HTML. This HTML displays as styled content within the output area, allowing users to visualize their formatted content in real time.

5. Optional: Improving Performance with Debouncing
For heavy input use cases, consider adding Lodash’s `debounce` function. This handy optimization limits the rate of updates, ensuring Vue doesn’t recompute `output` excessively, boosting performance during rapid typing. [Learn about debouncing and how it improves performance](https://css-tricks.com/debouncing-throttling-explained-examples/)




## Gotchas When Using Vue.js Inside a Jekyll Blog

Integrating Vue.js into a Jekyll blog can bring dynamic interactivity to your static site, but it comes with a few quirks and pitfalls to watch out for we'll discuss those in a future post

---

## Conclusion
With this setup, you now have a responsive Markdown editor that updates instantly, showcasing how Vue.js can bring interactive power to a static Jekyll site. Vue is a fascinating framework, showing us that frontend complexity is catching up to backend architecture! Tools like Vue help us keep code secure, efficient, and polished all essentials in today’s web development.

Enough for now… time to get **back to the HACKS!**

All the code for this blog is available on github 
[here](https://github.com/cyberoblivion/co-blog)
{% include comments.html %}

