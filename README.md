# React Controlled Components

## Overview

In this lesson, we'll discuss how to set up a controlled form in React.

## Objectives

1. Explain how React uses `value` on, e.g., `<input>`
2. Check whether a component is controlled or uncontrolled
3. Describe strategies for using controlled components
4. Use controlled inputs to validate values
5. Distinguish between `value` and `defaultValue` in a React controlled component

## Code Along

If you want to code along there is starter code in the `src` folder. Make sure
to run `npm install && npm start` to see the code in the browser.

## Controlling Form Values From State

Forms in React are similar to their regular HTML counterparts. The JSX we write
is almost identical. The way we store and handle form data, however, is entirely
new. In React, it is often a good idea to set up _controlled_ forms. A
controlled form is **a form that derives its input values from state**. Consider the
following:

```js
class Form extends Component {
  state = {
    firstName: "John",
    lastName: "Henry"
  }

  render() {
    return (
      <form>
        <input type="text" name="firstName" value={this.state.firstName} />
        <input type="text" name="lastName" value={this.state.lastName} />
      </form>
    )
  }
}
```

With the setup above, the two text `input`s will display the corresponding state
values. If `this.state.firstName` changes to "Joe", this will be reflected in
the value displayed in the first `input`.

This turns out to be very useful for a specific purpose - since we can set our
state _elsewhere_, using this set up, its easy to populate forms from available
data.

Imagine a user profile page with an 'Edit' button that opens a form for updating
user info. When a user clicks that 'Edit' button, they expect to see a form with
their user data pre-populated. This way, they can easily make small changes
without rewriting all their profile info.

In a React app, all the user info displayed on a user profile would already be
stored in state somewhere on the application. Its already being displayed after
all.  By setting the value of inputs based on state as we did above, we can
bring in existing state or props and populate a form dynamically.

There is a problem, though. The set up we've created is only half finished.
Right now, if we were to try and type into the `input`s in the example above,
they would not change and will always display "John" and "Henry".

To completely control a form, we also need our form to _update_ state.

## Updating State Via Forms

If we can change state values, React will re-render and our `input`s will
display the new state. Well, we know that `setState` is what we'll need in
order to initiate a state change, but when would we fire it?

We want to fire it **every time the form changes**. Forms should display
whatever changes a user makes, even if it is adding a single letter in an input.
For this, we use an event listener React has set up for us:

```js
<input type="text" id="firstName" onChange={event => this.handleFirstNameChange(event)} value={this.state.firstName} />
<input type="text" id="lastName" onChange={event => this.handleLastNameChange(event)} value={this.state.lastName} />
```

Form inputs in React come with specific events. `onChange` will fire every time
the value of an input changes. In our example, we're invoking an anonymous
function that accepts `event` as its argument (automatically provided by the
event listener), and then calls `this.handleFirstNameChange` or
`this.handleLastNameChange`, passing the `event` as an argument. Let's write out
what these functions look like:

```js
handleFirstNameChange = event => {
  this.setState({
    firstName: event.target.value
  })
}

handleLastNameChange = event => {
  this.setState({
    lastName: event.target.value
  })
}
```

The `event` contains data about the `target`, which is whatever the `event` was
triggered on. That `target`, being an `input`, has a `value` attribute. This
attribute is equal to whatever has been entered into `input`. **This is not the
value we provided from state**. When we read `event.target.value`, we get
whatever content is present when the event fired. In the case of our first
input, that would be a combination of whatever `this.state.firstName` is equal to
_plus_ **the last key stroke**. If you pressed 's', `event.target.value` would equal
"Johns".

In these methods, we're updating state based on `event.target.value`. This in
turn causes a re-render... and the cycle completes. The _new_ state values we
just set are used to set the `value` attributes of our two `input`s. From a
user's perspective, the form behaves exactly how we'd expect, displaying the
text that is typed. From React's perspective, we gain control over form values,
giving us the ability to more easily manipulate what our `inputs`s display, send
form data to other parts of the app or out onto the internet...

Controlling forms makes it more convenient to share form values between
components. Since the form values are stored in state, they are easily passed
down as props, or sent upward via a function supplied in props.

## More on Forms

Form elements include `<input>`, `<textarea>`, `<select>`, and `<form>` itself.
When we talk about inputs in this lesson, we broadly mean the form elements
(`<input>`, `<textarea>`, `<select>`) and not always specifically just
`<input>`.

To control the value of these inputs, we use a prop specific to that type of input:

- For `<input>` and `<textarea>`, we use `value`, as we have seen.

- For `<input type="checkbox">` and `<input type="radio">`, we use `checked`

- For `<option>`, we use `selected`

Each of these attributes can be set based on a state value. Each also has an
`onChange` event listener, allowing us to update state when a user interacts
with a form.

## Uncontrolled vs Controlled Components

![Kpop](https://media.giphy.com/media/QcnfLD17Ebt28/giphy.gif)

React provides us with two ways of setting and getting values in form elements.
These two methods are called _uncontrolled_ and _controlled_ components. The
differences are subtle, but it's important to recognize them — and use them
accordingly (spoiler: most of the time, we'll use _controlled_ components).

The quickest way to check if a component is controlled or uncontrolled is to
check for `value` or `defaultValue`. If the component has a `value` prop, it is
controlled (the state of the component is being controlled by React). If it
doesn't have a `value` prop, it's an uncontrolled component. Uncontrolled
components can optionally have a `defaultValue` prop to set its initial value.
These two props (`value` and `defaultValue`) are _mutually exclusive_: a
component is either controlled or uncontrolled, but it cannot be both.

#### Uncontrolled Components

In uncontrolled components, the state of the component's value is kept in the
DOM itself like a regular old HTML form— in other words, the form element in
question (e.g. an `<input>`) has its _own internal state_. To retrieve that
value, we would need direct access to the DOM component that holds the value,
_or_ we'd have to add an `onChange` handler.

To set an initial value for the element, we'd use the `defaultValue` prop. We
can't use the `value` prop for this: we're not using state to explicitly store
its value, so the component would never update its value anymore (since we're
rendering the same thing). Uncontrolled forms still work just fine in React.

To submit a form, we can use the `onSubmit` handler on the `form` element itself:

```js
<form onSubmit={ event => this.handleSubmit(event) }>
  ...
</form>
```

All the form data in an uncontrolled form is accessible within the `event`, but
accessing _can_ sometimes be a pain, as you end up writing things like
`event.target.children[0].value` to get the value of our first input.

```js
handleSubmit = event => {
  event.preventDefault()
  const firstName = event.target.children[0].value
  const lasstName = event.target.children[1].value
  this.sendFormDataSomewhere({ firstName, lastName })
}
```

On a larger form this can turn into some dense code.

#### Controlled component

In controlled components, we explicitly set the value of a component, and update
that value in response to any changes the user makes. Just to review, lets look
at some code to make things clearer:

```js
// src/components/ControlledInput.js
import React from 'react';

class ControlledInput extends React.Component {
  state = {
    value: '',
  }

  handleChange = event => {
    this.setState({
      value: event.target.value,
    });
  }

  render() {
    return (
      <form onSubmit={event => this.handleSubmit(event)}>
        <input
          type="text"
          value={this.state.value}
          onChange={this.handleChange}
        />
      </form>
    );
  }
}

export default ControlledInput;

```

```js
// src/index.js

import React from 'react';
import ReactDOM from 'react-dom';

import ControlledInput from './components/ControlledInput';

ReactDOM.render(
  <ControlledInput />,
  document.getElementById('root')
);
```

As you can see, we can easily define the initial value by setting the  `value`
property on the state to whatever we want. When you enter something into the
`input`, the value is captured and set as the new state.

Doing something with a submitted form also ends up cleaner:


```js
handleSubmit = event => {
  event.preventDefault()
  this.sendFormDataSomewhere(this.state)
}
```

In this case, our entire state object is just the controlled form data, so we
can send the entire object around wherever it needs to go. Not only that, if we
expanded our form to have _20_ controlled inputs, this `handleSubmit` doesn't
change. It just sends all _20_ state values wherever we need them to go upon
submission.

**Note:** Most often, submitting a form would involve sending a request to a server
somewhere online. We won't get into async React just yet.

## Conclusion

Using a controlled component is the preferred way to do things in React — it
allows us to keep _all_ component state in the React state, instead of relying
on the DOM to retrieve the element's value through its internal state. Whenever
our state changes, the component re-renders, rendering the input with the new
updated value. If we don't update the state, our input wouldn't update when the
user would type. In other words, we need to update our input's state
_programmatically_.

It might seem a little counterintuitive that we need to be so verbose, but this
actually opens the door to additional functionality. For example, let's say we
want to write an input that only takes in a number (let's pretend there is no
`<input type="number">`). We can now validate the data the user enters _before_
we set it on the state, allowing us to block any invalid values. If the input is
invalid, we simply avoid updating the state, preventing the input from updating.
We could optionally set another state property (for example, `isInvalidNumber`).
Using that state property, we can show an error in our component to indicate
that the user tried to enter an invalid value.

If we tried to do this using an uncontrolled component, the input would be
entered regardless, since we don't have control over the internal state of the
input. In our `onChange` handler, we'd have to roll the input back to its
previous value, which is pretty tedious!

## Bonus - Abstracting `setState` When `onChange` is Triggered

You're still here? Well, while you are, let's talk about the `onChange` event
we've got set up in our ControlledInput component. We have two methods in the
class that seem very very similar:

```js
handleFirstNameChange = event => {
  this.setState({
    firstName: event.target.value
  })
}

handleLastNameChange = event => {
  this.setState({
    lastName: event.target.value
  })
}
```

Since each one is changing a different value in our state, we've got them
separated here. You can imagine that once we've got a more complicated form,
this route may result in a very cluttered component. Instead of separate
methods, we could actually condense this down into one abstracted component.
Since `event` is being passed in as the argument, we have access to some of the
`event.target` attributes that may be present.

In this example, our two inputs:

```js
<input type="text" name="firstName" value={this.state.firstName} />
<input type="text" name="lastName" value={this.state.lastName} />
```

Have `name` attributes. If we make sure the `name` attributes match keys in our
state, we can write a generic `handleChange` method like so:

```js
handleLastNameChange = event => {
  this.setState({
    [event.target.name]: event.target.value
  })
}
```

If we connect this method to both of our `input`s, they will both correctly
update state. Why? Because for the first `input`, `event.target.name` is set to
`firstName`, while in the second `input`, it is set to `lastName`. Each
`input`'s `name` attribute will change which part of state is actually updated!

## Resources

- [React Forms](https://facebook.github.io/react/docs/forms.html)
- [Controlled vs Uncontrolled](https://www.sitepoint.com/video-controlled-vs-uncontrolled-components-in-react/) - Video

<p class='util--hide'>View <a href='https://learn.co/lessons/react-forms'>Forms</a> on Learn.co and start learning to code for free.</p>
