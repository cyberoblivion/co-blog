---
layout: post
title:  "How to Add Vue.js to Jekyll"
date:   2024-10-28 11:34:44 -0400
categories: jekyll update vuejs
permalink: /howto/add-vuejs-to-jekyll/
description: "Learn how to enhance your Jekyll site with Vue.js for dynamic, interactive content"
---


<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/3.2.45/vue.global.prod.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.21/lodash.min.js"></script> <!-- debounce CDN -->

## Adding Vue.js to Jekyll for a More Interactive Blog

In this post, we’ll explore adding Vue.js to a Jekyll blog to create a dynamic, interactive experience without any complicated setup. Using Vue with Jekyll is perfect for adding interactive elements like image galleries, dynamic tables, and live previews—all while keeping the main structure static and lightweight.

You might already know how to [add Bootstrap to Jekyll]({{ site.baseurl }}/howto/add-bootstrap-to-jekyll/), but today we'll try out Vue.js. This experiment will give us insights into using Vue in the Jekyll frontend, enabling us to create features like real-time markdown previews, galleries, and more interactive tables and charts.

### Setting Up Vue for Jekyll Pages

If you were building a standalone Vue project, you’d typically scaffold it with the following command:
{% highlight bash %}
  npm create vue@latest
{% endhighlight %}

This command sets up a full Vue project with options for tools like Vue Router, Pinia for state management, Vitest for testing, and more. However, here we’ll keep things simple by using Vue directly in our HTML and Markdown pages with no need for bundling or compiling.

Vue is extensive but we are going to use it in our frontent for this demo. We will keep it simple and work with Vue in the plain html/mardown pages of Jekyll and avoid any need to compile any code.

### Inline Setup of Vue.js in Jekyll
Instead of configuring Vue through a front matter flag (like we did with Bootstrap), we’ll keep everything within this Markdown post. This approach works well for quick demos and makes it easy to see changes without extra setup.

### Live Markdown Editor with Vue.js
To showcase Vue’s capabilities, let’s build a simple markdown editor. The editor will include:

1. A Markdown input—where users can type markdown.
2. A Live Preview—showing the formatted markdown in real-time.

We'll also use **Lodash** for debouncing input changes and **Marked.js** to render markdown into HTML.

**Here's what it looks like:**
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
The `output` computed property transforms the markdown in `input` into HTML. This property is reactive, meaning that any change to `input` automatically updates `output`—no extra function calls needed.

3. Rendering HTML with the `v-html` Directive
Vue’s `v-html` directive displays `output` as HTML inside the preview area. It’s perfect for rendering raw HTML safely, bypassing Vue’s default text rendering for a direct, styled preview of the markdown.

4. Parsing Markdown with `marked`
The `marked.parse()` function from the Marked.js library converts markdown syntax from `input` into HTML. This HTML displays as styled content within the output area, allowing users to visualize their formatted content in real time.

5. Optional: Improving Performance with Debouncing
For heavy input use cases, consider adding Lodash’s `debounce` function. This handy optimization limits the rate of updates, ensuring Vue doesn’t recompute `output` excessively, boosting performance during rapid typing.



## Gotchas When Using Vue.js Inside a Jekyll Blog

Integrating Vue.js into a Jekyll blog can bring dynamic interactivity to your static site, but it comes with a few quirks and pitfalls to watch out for we'll discuss those in a future post

---

## Conclusion
With these updates, you now have a fully functioning Markdown editor that listens for input changes and renders formatted output instantly. This feature is a practical example of how Vue.js can bring interactivity to a static Jekyll site.

All the code for this blog is available on github 
[here](https://github.com/cyberoblivion/co-blog)
{% include comments.html %}

