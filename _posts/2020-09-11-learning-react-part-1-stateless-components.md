---
layout: post
title: "Learning React Part 1: Stateless Components"
---
How does defining stateless components in React differ from Vue? This article will go over my initial observations, and is the first in a series that will cover my experience learning React having worked primarily with Vue in the past.

## Defining stateless components in React

```jsx
import React from 'react';

const myComponent = props => {
    function onClickHandler() {
    return this.props.myProp.toUpperCase();
}

    return (
        <div className="myComponent">
            <p>props.myProp</p>
            <button onClick={onClickHandler}>All Caps</button>
        </div>
    );
}

export default myComponent;
```

Just like in Vue, React has more than one way to define components. Being that most SPAs are basically component trees or a composition of components, and the most common components used in building an application's UI are stateless. Stateless components seem like a good place to start.

The most common way to define stateless components in React is using functional component syntax. In essence you are simply defining and exporting a function which will then be passed to ```ReactDOM.render()```.

The function encapsulates not only any actions the component might execute but more importantly returns the markup itself in the ```return``` statement - in JSX, which is just JavaScript at the end of the day. This can be done without the enclosing parens if it's single line or between parens for multiline, nested markup.

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

Behind the scenes React is using ReactDOM.createElement() to render the actual markup in the DOM, hence the need to import React.

Attributes are handled a bit differently than Vue SFCs. Since you are not really working with html but JSX, camelCase is used to work with attributes.

So ```class="myComponentClass"``` is ```className="myComponentClass``` and similarly for DOM events ```onclick="handleClick"``` is ```onClick="handleClick``` in React.

The last step is to export the component itself so that it may be used by other components. 

Since this component will not hold any state, it is important that we pass the ```props``` argument in the arrow function assigned to the component to access data the consumer may pass down to it.

Internally, this allows ```this``` to be scoped and reference the component itself, whereby you can access its props via object dot notation.

## Defining stateless components in Vue
```html
<template>
    <div class="myComponent">
        <p>{{myProp}}</p>

        <button @click="onClickHandler">All Caps</button>
    </div>
</template>

<script lang="ts">
import Vue from 'vue';
import {Component, Prop} from 'vue-property-decorator';

@Component
export default class extends Vue {
    @Prop
    myProp: string;

    onClickHandler() {
        return this.myProp.toUpperCase();
    }
}
</script>
```

Vue uses a different approach with it's Single File Component Syntax, which allows you to define the markup, scripts, and styles in one file, although this is simply a convention. You can also define the 3 in separate files and reference the syles and script in the ```.vue``` template file just like you would in any html file.

Mark up is defined in the ```<template></template>``` tag and you can directly access and manipulate props with interpolation using the double mustache ```{{}}``` which binds the data.

Vue allows you to use html attributes like you regularly would but also provides it's own special attributes called directives. This allows you to bind an expression or handler which reactively applies side effects to the DOM whenever the value changes.  For the sake of keeping things scoped to this example we want to primarily look at the ```v-on:click=""``` attribute or simply ```@click=""``` shorthand notation which allows us to us to listen to DOM events and pass a handler when they're triggered.

We then define the component in the script tag. There are multiple ways to do this. I prefer class-based components using TypeScript with the vue-property-decorator library which allows me to define the component using decorators that Vue can then understand and use in the compilation process.

```@Prop``` allows us to define prop attributes on the component, and also allows for defaults to be defined if necessary.

```@Component``` is actually part of the Vue Class Component library which vue-property-decorator depends heavily on. This decorator allows us to use class-style syntax to define our components.


These are my preliminary thoughts on stateless components as I am going through the process of learning React. Subsequent posts will go over other aspects, similarities, and differences.