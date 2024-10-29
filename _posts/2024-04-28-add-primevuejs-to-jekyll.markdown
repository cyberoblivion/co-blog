---
layout: post
title:  "How to Add PrimeVue to Jekyll"
date:   2025-10-28 15:34:44 -0400
categories: jekyll update vuejs
permalink: /howto/add-primevue-vuejs-to-jekyll/
description: ""
---


<!-- {% raw %} -->
  
 <link rel="stylesheet" href="https://unpkg.com/primeicons/primeicons.css" />
 <link rel="stylesheet" href="https://unpkg.com/primeflex@3/primeflex.min.css" />


<script src="https://unpkg.com/vue@3/dist/vue.global.prod.js"></script>
<script src="https://unpkg.com/primevue/umd/primevue.min.js"></script>
<script src="https://unpkg.com/@primevue/themes/umd/aura.min.js"></script>

<div id="app">
  <p-datepicker inline v-model="date"></p-datepicker>
  <br /><br />
  {{ date }}
  <br/><br/>
  **Messages
    <Message severity="success">Success Message</Message>
    <Message severity="info">Info Message</Message>
    <Message severity="warn">Warn Message</Message>
    <Message severity="error">Error Message</Message>
    <Message severity="secondary">Secondary Message</Message>
    <Message severity="contrast">Contrast Message</Message>
    <br/><br/>


  <Galleria v-model:activeIndex="activeIndex" v-model:visible="displayCustom" v-bind:value="images" v-bind:responsiveOptions="responsiveOptions" v-bind:numVisible="7" containerStyle="max-width: 850px" v-bind:circular="true" v-bind:fullScreen="true" v-bind:showItemNavigators="true" v-bind:showThumbnails="false">
    <template v-slot:item="slotProps">
        <img v-bind:src="slotProps.item.itemImageSrc" v-bind:alt="slotProps.item.alt" style="display: block; max-height: 350px" />
    </template>
    <template v-slot:thumbnail="slotProps">
        <img v-bind:src="slotProps.item.itemImageSrc" v-bind:alt="slotProps.item.alt" style="display: block; max-height: 64px" />
    </template>
  </Galleria>

</div>

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