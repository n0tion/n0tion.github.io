---
layout: post
title: "Lazy Loading Images With Vue Render Functions"
---
![LazyCat](https://images.pexels.com/photos/1056251/pexels-photo-1056251.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=640&w=426)

Images can slow the perceived speed of your web page quite a bit, especially when dealing with devices on slow connections. A quick and easy optimization that can make your site feel faster is by utilizing lazy loading. 

Lazy loading images allows for pages to load without having to wait on all its images to load beforehand. Images will be loaded as they appear in the browsers viewport, which means less wait time for the user to start interacting with the page.

As far as implementation, there are many ways to go about it, but I'll be demonstrating how to set up lazy loading with the [lozad.js](https://github.com/ApoorvSaxena/lozad.js) library. It is a lightweight library that makes use of the new [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) which provides a better performing lazy load implementation than how it has been historically done. Not to mention it has virtually zero dependencies and is 910 bytes minified and gzipped.

## Installation
This guide is assuming you already have a Vue 2.x application setup with TypeScript. You'll want to first start by installing lozad as well as it's type definitions.

```javascript
yarn add lozad @types/lozad
```

Once that's set up we can start creating an image wrapper component where we can set up lozad on all ```<img>```. The idea here is to rely on this wrapper when using images, to keep any further logic you wish to add contained within this one component.

```typescript
import Vue, { CreateElement } from vue;
import { Component, Prop } from 'vue-property-decorator';
import {VNodeData} from 'vue/types/vnode';

import lozad from 'lozad'

@Component
export default class ImgWrapper extends Vue {
    @Prop({ required: true })
    src: string;

    mounted() {
        const element = this.$el;

        const observer = lozad(element as HTMLElement);

        observer.observe(); 
    }

    render(createElement: CreateElement) {
        return createElement('img', {
            attrs: {
                ...this.$attrs,
                'data-src': src
            }
        } as VNodeData)
    }
}
```

## So Let's Break This Down 

In the mounted hook we initialized lozad. The very first line is simply capturing the root element of our wrapper component and then passing it to lozad. Out of the box, lozad doesn't perform any special treatment to the element as it loads. You have the ability to provide options, such as a class on the element so that you may target it in a CSS file. In production, you would ideally opt for applying some sort of transition or effect so that the experience is smooth as the load is triggered.

In the render function we're simply spreading any HTML attributes that a consumer may have passed to the component, to the rendered ```<img>``` element.

Notice that within ```attrs``` we are setting ```'data-src': src```. Since this a custom component, we need supply lozad the image path so it knows from where to load the image from.

## Using Our Custom Component

So usage is pretty straightforward, once you've registered the component you can simply use it as you would any other component.

```<ImgWrapper :src='https://unsplash.com/photos/153_VPk1NZQ'></ImgWrapper>```

I made a demo where you can see it in action [here](https://codepen.io/n0tion/pen/wYXwOm/):

![Lazy Cats Demo](https://i.imgur.com/I90Xy4h.jpg)

For reference, I'll have the code for the demo's render function as well as the pages markup. The main difference here is the fact that I'm not using TypeScript and I've added a few classes to style the component appropriately:

```javascript
Vue.component('lazy-img', {
    props: {
        src: {
            type: String,
            required: true
        }
    },
    mounted() {
        const rootElement = this.$el;
        const observer = lozad(rootElement);

        observer.observe();
    },
    render(createElement) {
        return createElement('img', {
            class: {
                'img-fluid': true,
                'img-thumbnail': true,
                'rounded': true
            },
            attrs: {
                'data-src': this.src
            }
        });
    }
});

new Vue({
    el: '#app',
    data: {
        cats: [
            {
                id: 0,
                image: 'https://images.pexels.com/photos/1056251/pexels-photo-1056251.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260'
            },
            {
                id: 1,
                image: 'https://images.pexels.com/photos/956750/pexels-photo-956750.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260'
            },
            {
                id: 2,
                image: 'https://images.pexels.com/photos/979247/pexels-photo-979247.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260'
            },
            {
                id: 3,
                image: 'https://images.pexels.com/photos/1183434/pexels-photo-1183434.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260'
            },
            {
                id: 4,
                image: 'https://images.pexels.com/photos/236606/pexels-photo-236606.jpeg?cs=srgb&dl=adorable-animal-bed-236606.jpg&fm=jpg'
            },
            {
                id: 5,
                image: 'https://images.pexels.com/photos/256632/pexels-photo-256632.jpeg?cs=srgb&dl=adorable-animal-baby-256632.jpg&fm=jpg'
            },
            {
                id: 6,
                image: 'https://images.pexels.com/photos/1056467/pexels-photo-1056467.jpeg?cs=srgb&dl=animal-beach-blur-1056467.jpg&fm=jpg'
            },
            {
                id: 7,
                image: 'https://images.unsplash.com/photo-1466618786657-4df462be674e?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=85657b9d933a4aaa3d81c87c7baaee4a&auto=format&fit=crop&w=1350&q=80'
            },
            {
                id: 8,
                image: 'https://images.unsplash.com/photo-1466155443295-01c6fe2f72d2?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=82dc652b80e199218e0c1733229dcce1&auto=format&fit=crop&w=1350&q=80'
            }
        ]
    }
});
```


```html
<div id="app">
    <header class="text-center">
        <h1 class="mt-4">Lazy Cats</h1>

        <p>Simple image lazy load example using Vue and Lozad</p>
    </header>

    <div class="container imgGrid">
        <div class="row">
            <div class="imgContainer mx-auto" v-for="cat in cats" :key="cat.id">
                <lazy-img :src="cat.image"></lazy-img>
            </div>
        </div>
    </div>
</div>
```
