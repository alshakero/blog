---
layout: post
title: The True Delight of React’s Error and Warning Messages
subtitle: An epitome of code kindness
type: tech
opengraphImage: /images/postsImages/the-sign-you-need.jpg
---
An epitome of code kindness

![Unsplash]({{ site.baseurl }}/images/postsImages/the-sign-you-need.jpg)

<cite>Photo by [Austin Chan](https://unsplash.com/photos/ukzHlkoz1IE?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on Unsplash</cite>

## Introduction

There is this fascinating idea that hasn’t left me for years now, that software developers, with all their prejudices, biases, and humanness in general, shape the world around us.

Think about it. Software, just like music, is an [intangible good](https://en.wikipedia.org/wiki/Intangible_good). It’s not wine or chocolate where raw materials can limit the product quantity hence the number of people who enjoy it. There exists thousands of software pieces that are written by individuals on a Saturday night and are used by millions of people everywhere.

It’s fair to conclude that those individuals have an impact, small or big, on people’s lives. And even if we consider corporations, the number of people who produce software is a microscopic number in relation to the number of said software users. For instance, [Google has ~18K](https://www.developer.com/daily_news/samsung-employs-twice-as-many-software-engineers-as-google.html) engineers worldwide, while around [1.17 billion people use Google](https://www.statista.com/chart/899/unique-users-of-search-engines-in-december-2012/), that’s a ratio of 0.000015%. We can see the huge dependence on software developers’ opinions and ideas to shape the world. There is obviously a developer society and a sub-culture. And if a meme or an idea spreads in this society, it would inevitably shape the technology used by everyone.

Now imagine, in a sci-fi kind of way, that there is an imaginary software technology that solves a world problem. Say it can cure cancer or completely eliminate poverty. *But*, this technology has the cost of ruining the life of the software developer who uses it. Would that technology ever see the light? Hardly. This imaginary story is an extreme example of the conflict of interest between the software developer and the end user. If good software meant the developer being uncomfortable, we will not see good software. And a big part of software developers’ lives is error messages. They’re key to the experience, and paying attention to them goes a long long way. They are directly important to the developer, and indirectly important to everyone of us.

## React

Of all the libraries and packages I’ve used in the last ~10 years, I haven’t seen a library where errors and warnings are as beautiful. Developers, including me, usually try to detect errors that break their own code and report them to the user along with the data they have on hand (scope?). Maybe mention the most common mistake that can cause the error along with an automatically-generated stack trace and that’s it.

```js
TypeError: undefined is not an object
```

But with React, the library works overtime trying to guess for you what went wrong. The error guessing effort is quite obvious and it’s tremendously helpful; it saves you a lot of time debugging and tries its best to save your day.

To help you enjoy the experience like I do writing React apps, I’ll make this article a ride. I’ll show React snippets with mistakes in them, you may try to find the issue or simply look at the warning/error below and decide whether it was helpful for you. This is not to list every message React logs. It’s a very small sample for inspiration.

Let’s start!

### 1. A simple one

```js
class MyComponent extends React.Component {
  componentWillMount() {
    console.log('Component mounted!')
  }
}
```

What’s wrong with this component?

Here is the message:

```js
Warning: MyComponent(…): No `render` method found on the returned component instance: you may have forgotten to define `render`.
```

Beautiful, isn’t it? **The component name** and a correct suggestion. Super easy to fix.

### 2. A trickier one

```js
class MyComponent extends React.Component {
  componentDidUnmount() {
    console.log('Unmounted!');
  }
  render() {
    return <h1>Hi!</h1>
  }
}
```

What’s the issue here?

Here is the message:

    Warning: MyComponent has a method called componentDidUnmount(). But there is no such lifecycle method. Did you mean componentWillUnmount()?

React went out of its way here. It expected you to make this mistake and waited for you there. Silence wouldn’t break React in this case, it would only break your app. Quite sympathetic of the library.

### 3. A little obvious one

```js
class GreetingComponent extends React.Component {
  constructor() {
    super();
    this.state = {name: 'Omar'};
  }
  render() {
    this.setState({name: 'Not Omar'});
    return <div>Hi {this.state.name}!</div>
  }
}
```

What’s the issue?

Here is the warning:

```js
Warning: Cannot update during an existing state transition (such as within `render` or another component’s constructor). Render methods should be a pure function of props and state; constructor side-effects are an anti-pattern, but can be moved to `componentWillMount`.
```

### 4. Not quite obvious

```js
class MyComponent extends React.Component {
  constructor() {
    super();
    this.setState({name: 'John'});
  }
  render() {
    return <h1>Hi {this.state.name}!</h1>
  }
}
```

What’s the issue?

Here is the message:
```bash
Warning: setState(…): Can only update a mounted or mounting component. This usually means you called setState() on an unmounted component. This is a no-op.

Please check the code for the MyComponent component.
```
### 5. Pure elegance

```js
const type = true;

ReactDOM.render( 
  <input type={type} />,
  document.getElementById("root")
);
```
What’s wrong with type here?

Here is the warning:
```bash
Warning: Received `true` for non-boolean attribute `type`.
If this is expected, cast the value to a string.
  in input
```

And that’s another example of an error that doesn’t affect React, rather affects your app.

### 6. The beginner’s rescuer

```js
class greetingComponent extends React.Component {
  render() {
    return <h1>Hi!</h1>
  }
}

ReactDOM.render( 
  <greetingComponent />,
  document.getElementById("root")
);
```

The two warnings:
```sh
Warning: <greetingComponent /> is using uppercase HTML. Always use lowercase HTML tags in React.

Warning: The tag <greetingComponent> is unrecognized in this browser. If you meant to render a React component, start its name with an uppercase letter.
```
I definitely fell for this at least once.

### 7. OK I fixed it, but it still doesn’t work

```js
class GreetingComponent extends React.Component {
  render() {
    return <h1>Hi!</h1>
  }
}

ReactDOM.render( 
  GreetingComponent,
  document.getElementById("root")
);
```

What’s wrong now?

The message:

```bash
Warning: Functions are not valid as a React child. This may happen if you return a Component instead of <Component /> from render. Or maybe you meant to call this function rather than return it.
```

Yup, it should be:
```js
    ReactDOM.render(<GreetingComponent />, document.getElementById("root"));
```
### 8. A very common mistake in the first couple days

```js

class GreetingComponent extends React.Component {
  render() {
    return <h1 class="bold">Hello!</h1>
  }
}

ReactDOM.render( 
  <GreetingComponent />,
  document.getElementById("root")
);
```

What’s up here?

The message:
```bash
Warning: Invalid DOM property `class`. Did you mean `className`?
  in h1 (created by GreetingComponent)
  in GreetingComponent
```
Enough data to land you exactly at your mistake

### 9. Why don’t you come back? 🎼

```js

class GreetingComponent extends React.Component {
  render() {
    <h1>Hello!</h1>
  }
}

ReactDOM.render( 
  <GreetingComponent />,
  document.getElementById("root")
);
```

You said goodbye
I was trying to hide what I felt inside
Until you passed me by
You said you’d return 
Will you ever return? 🎵
```bash
Uncaught Error: GreetingComponent(…): Nothing was returned from render. This usually means a return statement is missing. Or, to render nothing, return null.
```
## 10. And the one that doesn’t need an example:
```bash
You likely forgot to export your component from the file it's defined in, or you might have mixed up default and named imports.
```
*This one is a personal favorite.*

Obviously, I haven’t used every framework and library ever existed. And there may be better examples of nice and helpful error messages. But as a frontend developer, I can safely say that React easily stands out. I’ve been inspired by it and now I am trying my best to guess the areas my code users could make mistakes, and provide them with eleborate and helpful error messages when they do. For I am a true believer that a better developer experience means a better world.

*If you know some other nice examples of React warnings, please feel free to mention them in the comments and I’ll add them.*
