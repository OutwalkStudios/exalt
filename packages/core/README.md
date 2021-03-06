# Exalt Core

The framework runtime.

![Actions](https://github.com/exalt/exalt/workflows/build/badge.svg)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/exalt/exalt/blob/main/LICENSE)
[![Donate](https://img.shields.io/badge/patreon-donate-green.svg)](https://www.patreon.com/outwalkstudios)
[![Follow Us](https://img.shields.io/badge/follow-on%20twitter-4AA1EC.svg)](https://twitter.com/OutwalkStudios)

---

## Installation

You can install @exalt/core using npm:

```
npm install @exalt/core
```

---

 Table of Contents
-----------------
- [Building Components](#building-components)
- [Writing Templates](#writing-templates)
- [Store API](#store-api)

---

## Building Components

Exalt Components are a small wrapper over native Web Components. This means they work as valid HTML elements and can be used anywhere HTML can be used, including other frameworks.
You can use components to build independent and reusable pieces of your application. Components allow you to define your own html tags and hook into the component state and lifecycle.

Component names must have a hypen in the name as required by the custom elements standard. Something to keep in mind is that by default in order to provide encapsulation, components make use of
the [ShadowDOM](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM).
When using CSS frameworks such as bootstrap or tailwind, its important to disable the shadow dom in order for global styles to affect the components.

In order to make a component usable, it first needs to be created with `Component.create`, this function provides a few options to customize your component.

**Options**
- useShadow: boolean - tells the component whether or not to use ShadowDOM.
- styles: string[] - set the styles to be used in the component.
- stores: object[] - tells the component to react to changes in the provided stores.

The simplest way to create a component is to create a class that extends `Component` and return a template in the render method.

**Example:**
```js
import { Component, html } from "@exalt/core";

export class HelloWorld extends Component {

    render() {
        return html`
            <h1>Hello World!</h1>
        `;
    }
}

Component.create({ name: "hello-world" }, HelloWorld);
```

### Styling your component

You can apply css to your component in a variety of ways.
When using the ShadowDOM we suggest importing your css as a string and passing it to the `styles` component options.

**Example:**
```js
import { Component, html } from "@exalt/core";
import styles from "./hello-world.css";

export class HelloWorld extends Component {

    render() {
        return html`
            <h1>Hello World!</h1>
        `;
    }
}

Component.create({ name: "hello-world", styles: [styles] }, HelloWorld);
```

### Lifecycle

In applications, you may want to run specific code at certain times in a components life. Exalt provides a couple lifecycle methods for this purpose.

**Example:**
```js
import { Component, html } from "@exalt/core";

export class HelloWorld extends Component {

    /* render the components html */
    render() {}

    /* called when the component is added to the DOM */
    mount() {}

    /* called when the component is removed from the DOM */
    unmount() {}

    /* called when a component's state or attributes are updated */
    onUpdate(key, value) {}

    /* called when a component is about to be updated */
    shouldUpdate(key, value) {}
}

Component.create({ name: "hello-world" }, HelloWorld);
```

### State

Components have access to state and attributes for updating the DOM.

When making use of state, users can initialize a reactive property using `super.reactive()`.
This function will take the value and make the property reactive so that whenever its changed, the view automatically updates.
This example displays the current time and updates the time every second.

**Example:**
```js
import { Component, html } from "@exalt/core";

export class Clock extends Component {

    date = super.reactive(new Date());

    render() {
        return html`
            <h1>Current Time: ${this.date}</h1>
        `;
    }

    mount() {
        this.timer = setInterval(() => this.date = new Date(), 1000);
    }

    unmount() {
        clearInterval(this.timer);
    }
}

Component.create({ name: "my-clock" }, Clock);
```

### Attributes

Attributes are passed into a component like any other html element. When an attribute changes, the component is automatically updated.
Attributes can be accessed using the `props` property. The attributes are also passed into the `render` method for easy destructuring.

**Example:**
```js
import { Component, html } from "@exalt/core";

export class SayHello extends Component {

    render({ name }) {
        return html`
            <h1>Hello ${name}!</h1>
        `;
    }
}

Component.create({ name: "say-hello" }, SayHello);
```

the component could then be used as:

```html
<say-hello name="John Doe" />
```

### Working with DOM elements

Sometimes you may need to access a dom element from the template. Because Exalt renders directly to the real dom,
you can simply use normal dom manipulation methods. Alternatively Exalt offers a ref api to help clean up your dom manipulation code.

Simply use the components `createRef` method and then give an element the corresponding ref attribute name.

**Example:**
```js
import { Component, html } from "@exalt/core";

export class HelloWorld extends Component {

    header = super.createRef();

    render() {
        return html`
            <h1 ref="header">Hello World!</h1>
        `;
    }

    mount() {
        this.header.innerHTML = "Goodbye World!";
    }
}

Component.create({ name: "hello-world" }, HelloWorld);
```
---

## Writing Templates

Exalt provides a tagged template function for creating templates. This is similar to JSX but its entirely native to the web. You can write standard HTML inside them and use JavaScript expressions through placeholders indicated by curly braces prefixed with a dollar sign.

The `html` function provides an easier way to bind events to elements and pass data to components, You can pass any data you want as an attribute and it will process it for you.
Events are functions bound to attributes with the "on" prefix, these can be any native dom events or custom events.

**Example:**
```js
html`<button onclick=${() => alert("I was clicked!")}>Click Me!</button>`;
```

**Example:**
```js

const items = [
    { id: Math.random(), value: "1" },
    { id: Math.random(), value: "2" }
];

html`<list-view items=${items} />`;
```

---

## Store API

Exalt Components have reactive properties for updating the component it belongs to, but in cases where components need to share state, Exalt provides a global state solution via a store api. You can create a store and then tell individial components to listen to changes on the store using the `stores` component option.

**Example:**
```js
import { Component, html, createStore } from "@exalt/core";

/* create the store */
const store = createStore({ count: 0 });

export class Counter extends Component {

    render() {
        return html`
            <button onclick=${() => store.count++}>Clicked: ${store.count}</button>
        `;
    }

}

Component.create({ name: "x-counter", stores: [store] }, Counter);
```

---

## Reporting Issues

If you are having trouble getting something to work with exalt or run into any problems, you can create a new [issue](https://github.com/exalt/exalt/issues).

If this framework does not fit your needs or is missing a feature you would like to see, let us know! We would greatly appreciate your feedback on it.

---

## License

Exalt is licensed under the terms of the [**MIT**](https://github.com/exalt/exalt/blob/main/LICENSE) license.

