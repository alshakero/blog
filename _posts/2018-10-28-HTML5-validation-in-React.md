---
layout: post
title: Leveraging HTML5 form validation & validation UI in React — Validate forms in 2 mins
description: The power of reportValidity
type: tech
---

![HTML5 validation error]({{ site.baseurl }}/images/postsImages/errors.jpg)

*Sorry to say that this might be a waste of your time if you care about IE. If you don’t, first I love you, second let’s do it!*

Form validation in React is a hot topic. Some people think it’s a pain in the neck. Some people think it’s easy. Some people think it’s indeed painful but more robust than vanilla forms.

## What’s reportValidity?

Since HTML5, browsers have some basic yet sometimes enough abilities to validate your form fields. You can use required for all inputs. You can use [pattern](https://www.w3schools.com/tags/att_input_pattern.asp) attribute to enforce a certain pattern. MDN has [a great article](https://developer.mozilla.org/en-US/docs/Learn/HTML/Forms/Form_validation) on HTML validation.

reportValidity is a new method (available since September 2018 in all evergreen browsers) that triggers the browser’s form validation mechanism on demand! See [MDN’s article](https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormElement/reportValidity) on it. Let’s see why this is awesome.

Say you have this form

![HTML5 validation error]({{ site.baseurl }}/images/postsImages/error-validation.png)

This form has two fields, both are required. One has to be filled with anything, and the other must match `\d{3}` i.e. must have three digits exactly.

Sure, you can write avalidate JS method and validate that in JS. But why not #usetheplatform? Using the platform will liberate you from

1. writing your validation logic, regex and whatnot

1. creating your own validation output

1. writing localized error messages

1. worrying about the accessibility of it all. Accessible user notification [isn’t quite easy](https://www.w3.org/WAI/tutorials/forms/notifications/).

By using native validation, you get a lot for free and with less code.

The form above code would be:

```html
<input placeholder="I'm required" required />
<input placeholder="Match \d{3} (three digits)" pattern="\d{3}"/>
```

## The React part

While reportValidity does work on individual fields, I suggest wrapping your fields in a semantic form element and calling reportValidity on that form. This way you won’t need to even know about fields existence in your code.

### Grabbing the form’s ref

First, you need to grab your form’s ref. To do that you can do (from React 16.3+):

```js
class MyForm extends React.Component {
  constructor() {
    super();
    this.form = React.createRef();
  }
  render() {
    return (
      <div>
        <form ref={this.form}>          
        </form>
      </div>
    );
  }
}
```

Now you have this.form reference to your form. You can now add the fields you need with their HTML validation:

```js
<div>
  <form ref={this.form}>
    <input placeholder="I'm required" required />
    <input
      placeholder="I must match \d{3} (three digits)"
      pattern="\d{3}"
    />
  </form>
</div>
```

Creating your validate method

```js
validate() {
    return this.form.current.reportValidity();
}
```

Isn’t this the most beautiful validate method ever?

*If you’re confused about the word current see [React docs on refs](https://reactjs.org/docs/refs-and-the-dom.html).*

## I don’t want the form to submit automatically!

Sure, just add

```html
<form ref={this.form} onSubmit={e => e.preventDefault()}>
```

## The whole thing together

```js
import React from "react";
import ReactDOM from "react-dom";

import "./styles.css";

class ReactForms extends React.Component {
  constructor() {
    super();
    this.form = React.createRef();
    this.validate = this.validate.bind(this);
  }
  validate() {
    this.form.current.reportValidity();
  }
  render() {
    return (
      <div>
        <form ref={this.form} onSubmit={e => e.preventDefault()}>
          <input placeholder="I'm required" required />
          <input
            placeholder="I must match \d{3} (three digits)"
            required
            pattern="\d{3}"
          />
        </form>
        <button onClick={this.validate}>Validate me</button>
      </div>
    );
  }
}

const rootElement = document.getElementById("root");
ReactDOM.render(<ReactForms />, rootElement);
```

With this, you can add as many fields as you want without adding any React code. Just maintain your input fields’ HTML5 validation attributes and they will take affect. Can it get more declarative?

### See it running on CodeSandbox [here](https://codesandbox.io/s/30q85ylpn6)

## Styling invalid fields

You can use :invalid and :valid pseudo-classes to style your invalid fields without the need to add any conditional classes. See [MDN’s article](https://developer.mozilla.org/en-US/docs/Web/CSS/:invalid) on :invalid.

```css
.field:invalid {
    border: 1px solid red;
}
```

And you’re good to go.

## Do I need to update the component’s state?

That’s your decision. But I almost always use fetch API and live the happy life without involving React nor Redux. Especially that the form is validated at this point, you can do something like:

```js
if(this.validate()) {
  fetch('/sendmessage', {
    method: 'POST',
    body: new FormData(this.form.current)
  })
}
```

## Caveats

HTML5 validation is somewhat limited. Naturally, it religiously sticks to specs when it validates spec’ed things like emails. For example “a@b” passes `type=email` validation. Which is a little sad.

Also, validating the password-couple (password and password again) is a little tricky. You’ll for sure need to do that in your JS. To keep a consistent validation UI, you may use [`setCustomValidity`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLSelectElement/setCustomValidity).

## Conclusion

From now on, I’ll personally use [Constraint validation](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/HTML5/Constraint_validation) native API whenever I can. To save time designing validation UI, localizing error messages, taking care of accessibility and because it’s convenient.
