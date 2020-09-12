---
layout: post
title: "Learning React Part 1: Basic Components"
---
How does defining a stateless component in React differ from Vue? This article will go over my initial observations and is the first in a series that will cover my experience learning React having worked primarily with Vue in the past.

## Defining stateless components in React
```jsx
import React from 'react';

const myComponent = props => {
    return (
        <div className="myComponent">
            <p>props.myProp</p>
        </div>
    );
}

export default myComponent;
```

Just like in Vue, React has more than one way to define components. Being that most SPAs are basically component trees or a composition of components, and the most common type of components used in building an application's UI are presentational or stateless. Stateless components seem like a good place to start.

The most common way to define stateless components in React is using functional component syntax. In essence, you are simply defining and exporting a function which will then be passed to `ReactDOM.render()`.

The function encapsulates the component in the `return` statement - in JSX, which is just JavaScript at the end of the day. This can be done without the enclosing parens if it's a single line or between parens for multiline/nested markup.

```JavaScript
    const component = () => return <div>Hello world!</div>
```

```JavaScript
    const component = () => return (
        <div>
            <h1>Hello World</h1>

            <p>This is multiline JSX</p>
        </div>
    )
```

Behind the scenes React is using `ReactDOM.createElement()` to render the actual markup in the DOM, hence the need to import React.

Attributes are handled a bit differently than Vue SFCs. Since you are not really working with HTML but JSX, camelCase is used to work with attributes.

So `class="myComponentClass"` is `className="myComponentClass` in React.

The last step is to export the component itself so that it may be used by other components.

Since this component will not hold any state, it is important that we pass the `props` argument in the arrow function assigned to the component to access data the consumer may pass down to it.

## Defining stateless components in Vue SFCs

```vue
<template functional>
    <div class="myComponent">
        <p>{{myProp}}</p>
    </div>
</template>

<script lang="ts">
import Vue from 'vue';
import {Component, Prop} from 'vue-property-decorator';

@Component
export default class extends Vue {
    @Prop
    myProp: string;
}
</script>
```

Vue uses a different approach with its Single File Component syntax, which allows you to define the markup, scripts, and styles in one file, although this is simply a convention. You can also define SFCs in separate, corresponding, files and reference the styles and script in the `.vue` template file just like you would in any HTML file.

Mark up is defined in the `<template></template>` tag and you can directly access and manipulate props with interpolation using the double mustache `{{}}` which binds the data. It is important to either pass the `functional` keyword after `template` in the opening tag in SFCs or as a prop when defining the component with the regular syntax:

### Without JSX

```javascript
Vue.Component('my-component', {
    functional: true,
    props: [myProp],
    render: (createElement, context) {
        return createElement('p', props.myProp)
    }
})
```

### With JSX

```jsx
Vue.Component('my-component', {
    functional: true,
    props: [myProp],
    render: (h) => (
            <p>props.myProp</p>
        )
```

We then define the component in the script tag. There are multiple ways to do this. I prefer class-based components using TypeScript with the vue-property-decorator library which allows me to define the component using decorators that Vue can then understand and use in the compilation process.

`@Prop` allows us to define prop attributes on the component, and also allows for defaults to be defined if necessary.

`@Component` is actually part of the Vue Class Component library which vue-property-decorator depends heavily on. This decorator allows us to use class-style syntax to define our components.

These are my preliminary thoughts on stateless components as I am going through the process of learning React. Subsequent posts will go over other aspects, similarities, and differences.
