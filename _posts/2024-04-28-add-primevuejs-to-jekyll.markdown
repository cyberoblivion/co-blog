---
layout: post
title:  "How to Add PrimeVue to Jekyll"
date:   2024-04-28 15:34:44 -0400
categories: jekyll update vuejs
permalink: /howto/add-primevue-vuejs-to-jekyll/
author: "Ben Erridge"
description: "Learn how to integrate PrimeVue UI component library with Jekyll for building rich, interactive interfaces. Complete guide covering setup, component registration, theming with Aura, and practical examples including DatePicker, Messages, and Galleria components. Enhance your static Jekyll blog with professional Vue.js components without complex build tools."
---

Adding Vue.js to Jekyll opens up interactive possibilities, but what if you want more than basic components? **PrimeVue** is a comprehensive UI component library that brings professional, pre-built components to your Vue applications. Think datepickers, galleries, data tables, and complex forms—all ready to use.

In this guide, we'll integrate PrimeVue with Jekyll to demonstrate how you can build rich user interfaces directly in your blog posts without complex build tooling. If you're looking to add professional UI components to your Jekyll site, this is how you do it.

**Back to the HACKS!**

<div class="info-panel">
  <div class="info-icon">&#8505;</div>
    <div class="info-content">
    <strong>Prerequisites:</strong>
    Familiar with <a href="{{ site.baseurl }}/howto/add-vuejs-to-jekyll/">adding Vue.js to Jekyll</a>? This post builds on that foundation by introducing PrimeVue's component library.
    </div>
</div>

## What is PrimeVue?

[PrimeVue](https://primevue.org/) is an open-source UI component library for Vue 3 that provides:

- **80+ Ready-to-Use Components** - Everything from simple buttons to complex data tables
- **Professional Theming** - Pre-built themes including Aura, Material, Bootstrap, and more
- **Accessibility** - WCAG compliant components with proper ARIA support
- **Responsive Design** - Components that work seamlessly across devices
- **No Build Step Required** - Can be loaded via CDN for simple integrations

Unlike building custom Vue components from scratch, PrimeVue gives you production-ready UI elements that look professional out of the box.

## Setting Up PrimeVue in Jekyll

Just like with vanilla Vue.js, we can load PrimeVue via CDN without any build tools. Here's what we need:

### Required Dependencies

<!-- {% raw %} -->

<link rel="stylesheet" href="https://unpkg.com/primeicons/primeicons.css" />
<link rel="stylesheet" href="https://unpkg.com/primeflex@3/primeflex.min.css" />


<script src="https://unpkg.com/vue@3/dist/vue.global.prod.js"></script>
<script src="https://unpkg.com/primevue/umd/primevue.min.js"></script>
<script src="https://unpkg.com/@primevue/themes/umd/aura.min.js"></script>

Add these to the top of your Jekyll post (inside the Markdown file). Here's what each does:

1. **PrimeIcons CSS** - Icon library used by PrimeVue components
2. **PrimeFlex CSS** - Optional utility CSS framework for layouts (similar to Tailwind)
3. **Vue 3** - The core Vue.js framework
4. **PrimeVue UMD** - The PrimeVue component library
5. **Aura Theme** - PrimeVue's modern, clean theme

### The Integration Pattern

The key to using PrimeVue in Jekyll is understanding the three-step process:

1. **Create the Vue app** - Initialize Vue with `createApp()`
2. **Configure PrimeVue** - Register PrimeVue with theme settings using `app.use()`
3. **Register components** - Import only the components you need with `app.component()`

This selective component registration keeps your page lean—you're not loading the entire library, just what you use.

## Example 1: DatePicker Component

Let's start with a simple interactive datepicker:

<div id="app">
  <h3>Select a Date:</h3>
  <p-datepicker inline v-model="date"></p-datepicker>
  <br />
  <strong>Selected Date:</strong> {{ date }}
  <br/><br/>

<script>
  const { createApp, ref } = Vue;
  const app = createApp({
    setup() {
        const date = ref();
        
   // Image data array simulating a photo service      
    const images = ref([
    { 
        itemImageSrc: '/assets/stock/pexels-ethan-brooke-1123775-2128042.jpg', 
        thumbnailImageSrc: '/assets/co-ico-48.png', 
        alt: 'Street' 
    },
    { 
        itemImageSrc: '/assets/stock/pexels-joshsorenson-1054397.jpg', 
        thumbnailImageSrc: '/assets/co-ico-48.png', 
        alt: 'server cables' 
    },
    { 
        itemImageSrc: '/assets/stock/pexels-markusspiske-1089438.jpg', 
        thumbnailImageSrc: '/assets/co-ico-48.png', 
        alt: 'matrix' 
    },
    { 
        itemImageSrc: '/assets/stock/pexels-pixabay-60504.jpg', 
        thumbnailImageSrc: '/assets/co-ico-48.png', 
        alt: 'security pointer' 
    }
]);
      
      const position = ref('bottom');
      const positionOptions = ref([
          { label: 'Bottom', value: 'bottom' },
          { label: 'Top', value: 'top' },
          { label: 'Left', value: 'left' },
          { label: 'Right', value: 'right' }
      ]);
      const responsiveOptions = ref([
          { breakpoint: '1300px', numVisible: 4 },
          { breakpoint: '575px', numVisible: 1 }
      ]);
      // Simulate fetching data on mount
    //   onMounted(() => {
    //       // You can replace this with actual API fetching if needed
    //   });


      return {
        date,
        images,
        position,
        positionOptions,
        responsiveOptions
      };
    },
  });
  app.use(PrimeVue.Config, {
    theme: {
        preset: PrimeVue.Themes.Aura
    }
  });
  app.component('p-datepicker', PrimeVue.DatePicker);
  app.component('Message', PrimeVue.Message);
  app.component('RadioButton', PrimeVue.RadioButton)
  app.component('Galleria', PrimeVue.Galleria)
  app.mount('#app');
</script>



<!-- {% endraw %} -->