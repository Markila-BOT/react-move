<div style="text-align:center;">
  <a href="https://github.com/react-tools/react-move" target="\_parent"><img src="https://github.com/react-tools/media/raw/master/logo-react-move.png" alt="React Table Logo" style="width:450px;"/></a>
</div>

# React-Move

Beautiful, data-driven animations for React.

[![Build Status](https://travis-ci.org/react-tools/react-move.svg?branch=master)](https://travis-ci.org/react-tools/react-move)
[![npm version](https://img.shields.io/npm/v/react-move.svg)](https://www.npmjs.com/package/react-move)
[![npm downloads](https://img.shields.io/npm/dm/react-move.svg)](https://www.npmjs.com/package/react-move)
[![license](https://img.shields.io/github/license/mashape/apistatus.svg?maxAge=2592000)](https://github.com/react-tools/react-move/blob/master/LICENSE)
![gzip size](http://img.badgesize.io/https://npmcdn.com/react-move/dist/react-move.min.js?compression=gzip)
[![Join the community on Spectrum](https://withspectrum.github.io/badge/badge.svg)](https://spectrum.chat/react-move)

## Features

* Animate HTML, SVG & React-Native
* Fine-grained control of delay, duration and easing
* Animation lifecycle events: start, interrupt, end
* Custom tweening functions
* Awesome documentation and lots of examples
* Supports TypeScript

## Installation

```bash
$ yarn add react-move
# or
$ npm install react-move
```

## React Move 4.0 is here!!!

Lots of exciting news and don't worry upgrading is a breeze and can be done in 5 minutes.  You will not have to make a single change to your existing components. 🎉 

### 4.0 Highlights
- React Move is now just **3.5kb (gzipped)! Almost 60% smaller.**
- Application developers and package maintainers can now make much smaller bundles.
- You can now use any interpolator you want which opens new creative doors for designers.
- Tons of performance improvements. 🚀 
- Much easier debugging of animations.

### Upgrading to 4.0

This version of React Move breaks the hard dependency on d3-interpolate.  React Move now exports just two factory functions:
- createNodeGroup(getInterpolator, displayName) => NodeGroup
- createAnimate(getInterpolator, displayName) => Animate

The big change in this release is the `getInterpolator` function. This function opens up a lot of doors to be more efficient and creative with your animations. You can also debug your animations much more easily by console logging in `getInterpolator` to check if your animations are working as expected. 

For starters, you'll get exactly the same component setup you have in react-move 2.x.x and 3.x.x by creating them locally like this:

First install d3-interpolate locally:
```
npm install d3-interpolate
```

Then in your app:
```js
// THIS IS HOW YOU UPGRADE TO 4.0

import { createNodeGroup, createAnimate } from 'react-move'
import { interpolate, interpolateTransformSvg } from 'd3-interpolate'

function getInterpolator(begValue, endValue, attr, namespace) {
  if (attr === 'transform') {
    return interpolateTransformSvg(begValue, endValue)
  }

  return interpolate(begValue, endValue)
}

// YOUR NEW LOCAL <NodeGroup /> AND <Animate /> - EQUIVALENT TO 2.X.X AND 3.X.X

export const NodeGroup = createNodeGroup(getInterpolator, 'NodeGroupDisplayName') // displayName is optional
export const Animate = createAnimate(getInterpolator, 'AnimateDisplayName') // displayName is optional
```

### New `getInterpolator` function

The above `getInterpolator` function is how react-move has been hard wired for some time.  It's modeled after how [D3](https://d3js.org/) selects interpolators and is quite useful. If you're not concerned about bundle size then the above will give you a lot of flexibility.  The `interpolate` function exported from d3-interpolate is very clever.  It will interpolate numbers, colors and strings with numbers in them without you needing to worry about it.  

The `interpolate` function exported from d3-interpolate also includes a lot of code (e.g. d3-color) that may not be needed for your project. For example, if you are just interpolating numbers in your components you could replace all that code with just a simple a interpolation function.  React Move will allow you to apply an easing functions to your transitions to get a variety of effects.  A basic numeric interpolator would look like this:

```js
import { createNodeGroup, createAnimate } from 'react-move'

const numeric = (beg, end) => {
  const a = +beg
  const b = +end - a
  
  return function(t) {
    return a + b * t
  } 
}

function getInterpolator(begValue, endValue, attr, namespace) {
  return numeric(begValue, endValue)
}

export const NodeGroupNumeric = createNodeGroup(getInterpolator, 'NodeGroupDisplayName') // displayName is optional
export const AnimateNumeric = createAnimate(getInterpolator, 'AnimateDisplayName') // displayName is optional

```

Your `getInterpolator` function should avoid a lot of logic and computation.  It will get called at high frequency when transitions fire in your components.  You get the begin and end values and what the attribute name (string) is.  You will also get the namespace string (less common) if you are using them in your state.  **See the sections below on starting states and transitions for more on attrs and namespaces.**

Of course you can create as many custom components as you want and organize them in a way that makes sense to you.  You can use any interpolation library or write your own. 

## Demos

* [CodeSandbox - Animated Bars](https://codesandbox.io/s/w0ol90x9z5) ([@animateddata](https://github.com/animateddata))
* [CodeSandbox - Collapsible Tree](https://codesandbox.io/s/ww0xkyqonk) ([@techniq](https://github.com/techniq))
* [CodeSandbox - Draggable List](https://codesandbox.io/s/j2povnz8ly)
* [CodeSandbox - Circle Inferno](https://codesandbox.io/s/n033m6nw00)
* [CodeSandbox - Animated Mount/Unmount](https://codesandbox.io/s/9z04rpypny)
* [Examples](https://react-move.js.org)

# Documentation

The docs below are for version **4.x.x** of React-Move.

Older versions:

* [Version 1.x.x](https://github.com/react-tools/react-move/tree/v1.6.1)

The API for `NodeGroup` and `Animate` have not changed in 4.0, but if you want to refer back:
* [Version 2.x.x](https://github.com/react-tools/react-move/tree/v2.9.1)
* [Version 3.x.x](https://github.com/react-tools/react-move/tree/v3.1.0)

# Getting Started

React Move exports just two factory functions:
- createNodeGroup => NodeGroup - If you have an **array of items** that enter, update and leave
- createAnimate => Animate - If you have a **singe item** that enters, updates and leaves

To get some components to work with in your app you can use this code to create them with some good defaults:

```
npm install react-move d3-interpolate
```

Then in your app:
```js
import { createNodeGroup, createAnimate } from 'react-move'
import { interpolate, interpolateTransformSvg } from 'd3-interpolate'

function getInterpolator(begValue, endValue, attr, namespace) {
  if (attr === 'transform') {
    return interpolateTransformSvg(begValue, endValue)
  }

  return interpolate(begValue, endValue)
}

export const NodeGroup = createNodeGroup(getInterpolator, 'NodeGroupDisplayName') // displayName is optional
export const Animate = createAnimate(getInterpolator, 'AnimateDisplayName') // displayName is optional
```
Then just import them in other components in your app.

## Starting state

Before looking at the components it might be good to look at starting state.  You are going to be asked to define starting states for each item in your `NodeGroup` and `Animate` components. This is a key concept and probably the most error prone for developers working with React Move.  The starting state for each item is always **an object with string or number leaves**.  The leaf keys are referred to as "attrs" as in "attribute."  There are also "namespaces" which are a purely organizational concept.

Two rules to live by for starting states:
- Don't use the strings "timing" or "events" as an attr or namespace.
- There should never be an array anywhere in your object.

Example starting state:
```js
// GOOD
{
  attr1: 100,
  attr2: 200,
  attr3: '#dadada'
}

// BAD
{
  attr1: [100], // NO ARRAYS
  attr2: 200,
  attr3: '#dadada'
}
```

A more concrete example might be:
```js
{
  opacity: 0.1,
  x: 200,
  y: 100,
  color: '#dadada'
}
```

You can add "namespaces" to help organize your state:
```js
{
  attr1: 100,
  attr2: 200,
  attr3: '#ddaabb',
  namespace1: {
    attr1: 100,
    attr2: 200
  }
}
```
Or something like:
```js
{
  namespace1: {
    attr1: 100,
    attr2: 200
  },
  namespace2: {
    attr1: 100,
    attr2: 200
  }
}
```
You might use namespaces like so:
```js
{
  inner: {
    x: 100,
    y: 150,
    color: '#545454'
  },
  outer: {
    x: 300,
    y: 350,
    color: '#3e3e3e'
  }
}
```

#### Starting state in NodeGroup

In `NodeGroup` you are working with an array of items and you pass a start prop (a function) that receives the data item and its index.  The start prop will be called when that data item (identified by its key) enters.  Note it could leave and come back and that prop will be called again.  Immediately after the starting state is set your enter transition (optional) is called allowing you to transform that state.

```js
<NodeGroup
  data={data} // an array (required)
  keyAccessor={item => item.name} // function to get the key of each object (required)
  start={(item, index) => ({ // returns the starting state of node (required)
    ...
  })}
>
  {(nodes) => (
    ...
      {nodes.map(({ key, data, state }) => {
        ...
      })}
    ...
  )}
</NodeGroup>
```

#### Starting state in Animate

In `Animate` you are animating a single item and pass a start prop that is an object or a function.  The start prop will be called when that the item enters.  Note it could leave and come back by toggling the show prop.  Immediately after the starting state is set your enter transition (optional) is called allowing you to transform that state.

```js
<Animate
  start={{ // object or function
    ...
  }}
>
  {state => (
    ...
  )}
</Animate>
```

## Transitioning state

You return an object or an array of config objects in your **enter**, **update** and **leave** props functions for both `NodeGroup` and `Animate`. Instead of simply returning the next state these objects describe how to transform the state. Each config object can specify its own duration, delay, easing and events independently.

There are two special keys you can use: **timing** and **events**. Both are optional.
Timing and events are covered in more detail below.

If you aren't transitioning anything then it wouldn't make sense to be using NodeGroup.
That said, it's convenient to be able to set a key to value when a node enters, updates or leaves without transitioning.
To support this you can return four different types of values to specify how you want to transform the state.

* `string or number`: Set the key to the value immediately with no transition.  Ignores all timing values.

* `array [value]`: Transition from the key's current value to the specified value. Value is a string or number.

* `array [value, value]`: Transition from the first value to the second value. Each value is a string or number.

* `function`: Function will be used as a custom tween function.


Example config object:
```js
{
  attr1: [200],
  attr2: 300,
  attr3: ['#dadada']
  timing: { duration: 300, delay: 100 }
}
```

Using namespaces:
```js
{
  attr1: [100],
  attr3: '#ddaabb',
  namespace1: {
    attr1: [300],
    attr2: 200
  },
  timing: { duration: 300, delay: 100 }
}
```

To have different timing for some keys use an array of config objects:
```js
[
  {
    attr1: [200, 500],
    timing: { duration: 300, delay: 100 }
  },
  {
    attr2: 300, // this item, not wrapped in an array, will be set immediately, so which object it's in doesn't matter
    attr3: ['#dadada']
    timing: { duration: 600 }
  },
]
```

### Example Transitions in NodeGroup

```js
<NodeGroup
  data={this.state.data}
  keyAccessor={(d) => d.name}

  start={(data, index) => ({
    opacity: 1e-6,
    x: 1e-6,
    fill: 'green',
    width: scale.bandwidth(),
  })}

  enter={(data, index) => ({
    opacity: [0.5], // transition opacity on enter
    x: [scale(data.name)], // transition x on on enter
    timing: { duration: 1500 }, // timing for transitions
  })}

  update={(data) => ({
    ...
  })}

  leave={() => ({
    ...
  })}
>
  {(nodes) => (
    ...
  )}
</NodeGroup>
```

Using an array of config objects:
```js
import { easeQuadInOut } from 'd3-ease';

...

<NodeGroup
  data={this.state.data}
  keyAccessor={(d) => d.name}

  start={(data, index) => ({
    opacity: 1e-6,
    x: 1e-6,
    fill: 'green',
    width: scale.bandwidth(),
  })}

  enter={(data, index) => ([ // An array
    {
      opacity: [0.5], // transition opacity on enter
      timing: { duration: 1000 }, // timing for transition
    },
    {
      x: [scale(data.name)], // transition x on on enter
      timing: { delay: 750, duration: 1500, ease: easeQuadInOut }, // timing for transition
    },
  ])}

  update={(data) => ({
    ...
  })}

  leave={() => ({
    ...
  })}
>
  {(nodes) => (
    ...
  )}
</NodeGroup>
```

## Timing

If there's no timing key in your object you'll get the timing defaults.
You can specify just the things you want to override on your timing key.

Here's the timing defaults...

```js
const defaultTiming = {
  delay: 0,
  duration: 250,
  ease: easeLinear
};
```

For the ease key, just provide the function. You can use any easing function, like those from d3-ease...

[List of ease functions exported from d3-ease](https://github.com/d3/d3-ease/blob/master/index.js)

## < NodeGroup />

### Component Props

| Name                                               | Type     | Default  | Description                                                                                                                                                                              |
| :------------------------------------------------- | :------- | :------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <span style="color: #31a148">data \*</span>        | Array    |          | An array of data objects. The data prop is treated as immutable so the nodes will only update if prev.data !== next.data.                                                                |
| <span style="color: #31a148">keyAccessor \*</span> | function |          | Function that returns a string key given a data object and its index. Used to track which nodes are entering, updating and leaving.                                                      |
| <span style="color: #31a148">start \*</span>       | function |          | A function that returns the starting state. The function is passed the data and index and must return an object.                                                                         |
| enter                                              | function | () => {} | A function that **returns an object or array of objects** describing how the state should transform on enter. The function is passed the data and index.                                 |
| update                                             | function | () => {} | A function that **returns an object or array of objects** describing how the state should transform on update. The function is passed the data and index.                                |
| leave                                              | function | () => {} | A function that **returns an object or array of objects** describing how the state should transform on leave. The function is passed the data and index.                                 |
| <span style="color: #31a148">children \*</span>    | function |          | A function that renders the nodes. It should accept an array of nodes as its only argument. Each node is an object with the key, data, state and a type of 'ENTER', 'UPDATE' or 'LEAVE'. |

* required props

## < Animate />

### Component Props

| Name | Type | Default | Description |
|:-----|:-----|:-----|:-----|
| show | bool | true |  Boolean value that determines if the child should be rendered or not. |
| <span style="color: #31a148">start *</span> | union:<br>&nbsp;func<br>&nbsp;object<br> |  |  An object or function that returns an obejct to be used as the starting state. |
| enter | union:<br>&nbsp;func<br>&nbsp;array<br>&nbsp;object<br> |  |  An object, array of objects, or function that returns an object or array of objects describing how the state should transform on enter. |
| update | union:<br>&nbsp;func<br>&nbsp;array<br>&nbsp;object<br> |  |  An object, array of objects, or function that returns an object or array of objects describing how the state should transform on update. ***Note:*** although not required, in most cases it make sense to specify an update prop to handle interrupted enter and leave transitions. |
| leave | union:<br>&nbsp;func<br>&nbsp;array<br>&nbsp;object<br> |  |  An object, array of objects, or function that returns an object or array of objects describing how the state should transform on leave. |
| <span style="color: #31a148">children *</span> | function |  |  A function that renders the node.  The function is passed the data and state. |

* required props

## React-Move vs React-Motion

* React-move allows you to define your animations using durations, delays and ease functions.
  In react-motion you use spring configurations to define your animations.

* React-move is designed to plugin interpolation for strings, numbers, colors, SVG paths and SVG transforms.
  With react-motion you can only interpolate numbers so you have to do a bit more work use colors, paths, etc.

* In react-move you can define different animations for entering, updating and leaving with the ability to specify delay, duration and ease on each individual key.
  React-motion allows you to define a spring configuration for each key in the "style" object.

* React-move has lifecycle events on its transitions.
  You can pass a function to be called on transition start, interrupt or end.
  React-motion has an "onRest" prop that fires a callback when the animation stops (just the `Motion` component not `TransitionMotion` or `StaggeredMotion`).

* React-move also allows you to pass your own custom tween functions. It's all springs in react-motion.

## Contributing

We love contributions from the community! Read the [contributing info here](https://github.com/react-tools/react-move/blob/master/CONTRIBUTING.md).

#### Run the repo locally

* Fork this repo
* `npm install`
* `cd docs`
* `npm install`
* `npm start`

#### Scripts

Run these from the root of the repo

* `npm run lint` Lints all files in src and docs
* `npm run test` Runs the test suite locally
* `npm run test:coverage` Get a coverage report in the console
* `npm run test:coverage:html` Get an HTML coverage report in coverage folder

Go to [live examples, code and docs](https://react-move.js.org)!
