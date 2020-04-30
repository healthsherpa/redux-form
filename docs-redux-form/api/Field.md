# `Field`

[`View source on GitHub`](https://github.com/redux-form/redux-form/blob/master/src/Field.js)

The `Field` component is how you connect each individual input to the Redux store. There are
three fundamental things that you need to understand in order to use `Field` correctly:

1.  The `name` prop is required. It is a string path, in dot-and-bracket notation, corresponding to
    a value in the form values. It may be as simple as `'firstName'` or as complicated as
    `'contact.billing.address[2].phones[1].areaCode'`.

2.  The `component` prop is required. It may be a `Component`, a stateless
    function, or a string name of one of the default supported DOM inputs (`input`, `textarea` or
    `select`) (see the [Usage](#usage) section below). To learn about the props that will be provided to
    whatever your `component` is, see the [Props](#props) section below.

3.  All other props will be passed along to the element generated by the `component` prop.

## Importing

```javascript
var Field = require('redux-form').Field // ES5
```

```javascript
import { Field } from 'redux-form' // ES6
```

## Props you can pass to `Field`

#### `name : String` [required]

A string path, in dot-and-bracket notation, corresponding to a value in the form values. It may
be as simple as `'firstName'` or as complicated as
`contact.billing.address[2].phones[1].areaCode`. See the [Usage](#usage) section below for details.

Numeric field names, e.g. `name="42"` or `name="foo.5.email"`, are not supported, as they can
be confused for array indexes.

#### `component : Component<FieldProps>|String` [required]

A `Component` (stateful class or stateless function), or string corresponding to a default JSX element.
See the [Usage](#usage) section below for details.

#### `format : (value, name) => formattedValue` [optional]

Formats the value from the Redux store to be displayed in the field input. Common use cases are
to format `Number`s into currencies or `Date`s into a localized date format.

`format` is called with the field `value` and `name` as arguments and should return the
new formatted value to be displayed in the field input.

To respect React 15 input behavior there is `defaultFormat = value => value == null ? '' : value` internally used. To disable that you can pass `null` as `format` prop.

#### `normalize : (value, previousValue, allValues, previousAllValues, name) => nextValue` [optional]

A function to convert whatever value the user has entered into the value that you want stored in
the Redux store for the field. For instance, if you want the value to be in all uppercase, you
would pass `value => value.toUpperCase()`. The parameters to your normalize function are:

> ##### `value : any`

> The value entered by the user.

> ##### `previousValue : any`

> The previous value for the field.

> ##### `allValues : Object`

> All the values in the entire form with the new value. This will be an Immutable `Map` if you are
> using Immutable JS.

> ##### `previousAllValues : Object`

> All the values in the entire form before the current change. This will be an Immutable `Map` if
> you are using Immutable JS.

> ##### `name : String`

> The name of the field being normalized

#### `onBlur : (event, newValue, previousValue, name) => void` [optional]

A callback that will be called whenever an `onBlur` event is fired from the underlying input.
If you call `event.preventDefault()`, the `BLUR` action will _NOT_ be dispatched, and the value
and focus state will not be updated in the Redux store.

#### `onChange : (event, newValue, previousValue, name) => void` [optional]

A callback that will be called whenever an `onChange` event is fired from the underlying input.
If you call `event.preventDefault()`, the `CHANGE` action will _NOT_ be dispatched, and the value
will not be updated in the Redux store.

#### `onDragStart : (event, name) => void` [optional]

A callback that will be called whenever an `onDragStart` event is fired from the underlying input.

#### `onDrop : (event, newValue, previousValue, name) => void` [optional]

A callback that will be called whenever an `onDrop` event is fired from the underlying input.
If you call `event.preventDefault()`, the `CHANGE` action will _NOT_ be dispatched, and the value
will not be updated in the Redux store.

#### `onFocus : (event, name) => void` [optional]

A callback that will be called whenever an `onFocus` event is fired from the underlying input.
If you call `event.preventDefault()`, the `FOCUS` action will _NOT_ be dispatched, and
the focus state will not be updated in the Redux store.

#### `props : object` [optional]

Object with custom props to pass through the `Field` component into a component provided
to `component` prop. This props will be merged to props provided by `Field` itself. This _may_ be
useful if you are using TypeScript. This construct is completely optional; the primary way of
passing props along to your `component` is to give them directly to the `Field` component, but
if, for whatever reason, you prefer to bundle them into a separate object, you may do so by
passing them into `props`.

#### `parse : (value, name) => parsedValue` [optional]

Parses the value given from the field input component to the type that you want stored in the
Redux store. Common use cases are to parse currencies into `Number`s or
localized date formats into `Date`s.

`parse` is called with the field `value` and `name` as arguments and should return the new
parsed value to be stored in the Redux store.

#### `type : string` [optional]

Used to determine the exact shape of the `input` prop passed through to the component provided to
`component` prop, as detailed below:

- if `type === 'checkbox'` or `type === 'radio'`, the boolean `checked` field will be passed through
- if `type === 'select-multiple'`, the value will always be an array (i.e. will default to `[]` instead of an empty string)
- if `type === 'file'`, the value will always be of type `?File` (i.e. will default to `undefined` instead of an empty string)

Use this if either:

1. `component` is a `string` value (i.e. a native HTML element)
2. `component` requires the `input` prop to follow the special semantics described above

`type` is unusual in that it is passed through to `component`, unlike other props consumed by `Field`.

#### `validate : Array<Function> | (value, allValues, props, name) => error` [optional]

Allows you to provide a field-level validation rule. The function is given the fields current value, all other form values, the props passed to the form, and the name of field currently being validated. If the field is valid it should return `undefined`. If the field is invalid it should return an error (usually, but not necessarily, a `String`). Note: if the validate prop changes the field will be re-registered.
Note: If an array of functions is given, the validation chain is stopped whenever a function return an error and the next validation will not be called.

#### `value : string` [optional]

When `type === radio`, this value is compared with the current field value to determine the value of `checked` in the `input` prop passed to `component`.
Otherwise, this prop is ignored.

#### `warn : Array<Function> | (value, allValues, props) => warning` [optional]

Allows you to provide a field-level warning rule. The function is given the fields current value, all other form values, and the props passed to the form. If the field does not need a warning it should return `undefined`. If the field needs a warning it should return the warning (usually, but not necessarily, a `String`). Note: if the warn prop changes the field will be re-registered.
Note: If an array of functions is given, the warn chain is stopped whenever a function return an error and the next warning will not be called.

#### `forwardRef : boolean` [optional]

If `true`, the rendered component will be available with the `getRenderedComponent()` method.
Defaults to `false`. **Cannot be used if your component is a stateless function component.**

#### `immutableProps : Array<String>` [optional]

> Prop names that only require strict-equals, not deep equals, to determine
> `shouldComponentUpdate`. Useful for performance and compatibility with 3rd
> party immutable libraries. Defaults to `[]`.

## Usage

The `component` prop will be passed to
[`React.createElement()`](https://reactjs.org/docs/react-api.html#createelement),
which accepts one of three possible things:

### 1. A normal component

This can be any component class that you have written or have imported from a third party library.

```js
// MyCustomInput.js
import React, { Component } from 'react'

class MyCustomInput extends React.Component {
  render() {
    const {
      input: { value, onChange }
    } = this.props
    return (
      <div>
        <span>The current value is {value}.</span>
        <button type="button" onClick={() => onChange(value + 1)}>
          Inc
        </button>
        <button type="button" onClick={() => onChange(value - 1)}>
          Dec
        </button>
      </div>
    )
  }
}
```

Then, somewhere in your form...

```js
import MyCustomInput from './MyCustomInput'

...

<Field name="myField" component={MyCustomInput}/>
```

To learn what props will be passed to your component, see the [Props](#props) section below.

### 2. A stateless function

This is the most flexible way to use `<Field>`, as it gives you complete control over how the
input is rendered. It is especially useful for displaying validation errors. It will also be the
most familiar to people migrating from previous versions of `redux-form`. **You must define the
stateless function outside of your `render()` method, or else it will be recreated on every
render and will force the `Field` to rerender because its `component` prop will be different.**
If you are defining your stateless function inside of `render()`, it will not only be slower, but
your input will lose focus whenever the entire form component rerenders.

```js
// outside your render() method
const renderField = (field) => (
    <div className="input-row">
      <input {...field.input} type="text"/>
      {field.meta.touched && field.meta.error &&
       <span className="error">{field.meta.error}</span>}
    </div>
  )

// inside your render() method
<Field name="myField" component={renderField}/>
```

To learn what props will be passed to your stateless function, see the [Props](#props) section
below.

### 3. A string: `input`, `select`, or `textarea`

So all you really need to render an `redux-form`-connected text input is:

```js
<Field component="input" type="text" />
```

Remember the third rule above that all other props are passed to the element generated by
`component`? So the `type="text"` prop will be given to the input.

## Instance API

The following properties and methods are available on an instance of a `Field` component.

#### `dirty : boolean`

> `true` if the current value is different from the initialized value, `false` otherwise.

#### `name : String`

> When nested in `FormSection`, returns the `name` prop prefixed with the `FormSection` name.
> Otherwise, returns the `name` prop that you passed in.

#### `pristine : boolean`

> `true` if the current value is the same as the initialized value, `false` otherwise.

#### `value : any`

> The current value of the field.

#### `getRenderedComponent()`

> Returns the instance of the rendered component. For this to work, you must provide a
> `forwardRef` prop, and your component must not be a stateless function component.

## Props

These are props that `Field` will pass to your wrapped component. **The props provided by `redux-form`
are divided into `input` and `meta` objects.**

**Any custom props passed to `Field` will be merged into the props object on the same level as the `input` and `meta` objects.**

### Input Props

The props under the `input` key are what connects your input component to Redux and are meant
to be destructured into your `<input/>` component.

#### `input.checked : boolean` [optional]

> An alias for `value` _only when `value` is a boolean_. Provided for convenience of destructuring
> the whole field
> object into the props of a form element.

#### `input.name : String`

> When nested in `FormSection`, returns the `name` prop prefixed with the `FormSection` name.
> Otherwise, returns the `name` prop that you passed in.

#### `input.onBlur(eventOrValue) : Function`

> A function to call when the form field loses focus. It expects to _either_ receive the
> [React SyntheticEvent](https://reactjs.org/docs/events.html) _or_ the current
> value of the field.

#### `input.onChange(eventOrValue) : Function`

> A function to call when the form field is changed. It expects to _either_ receive the
> [React SyntheticEvent](https://reactjs.org/docs/events.html) _or_ the new value
> of the field.

#### `input.onDragStart(event) : Function`

> A function to call when the form field receives a `dragStart` event. Saves the field value
> in the event for giving the field it is dropped into.

#### `input.onDrop(event) : Function`

> A function to call when the form field receives a `drop` event.

#### `input.onFocus(event) : Function`

> A function to call when the form field receives focus.

#### `input.value: any`

> The value of this form field. It will be a boolean for checkboxes, and a string for all other
> input types. If there is no value in the Redux state for this field, it will default to
> `''`. This is to ensure that the input is
> [controlled](https://facebook.github.io/react/docs/forms.html#controlled-components).
> If you require that the value be of another type (e.g. `Date` or `Number`), you _must_ provide
> `initialValues` to your form with the desired type of this field.

### Meta Props

The props under the `meta` key are metadata about the state of this field that `redux-form` is
tracking for you.

#### `meta.active : boolean`

> `true` if this field currently has focus. It will only work if you are passing `onFocus` to your
> input element.

#### `meta.autofilled : boolean`

> `true` if this field has been set with the `AUTOFILL` action and has not since been changed
> with a `CHANGE` action. This is useful to render the field in a way that the user can tell that
> the value was autofilled for them.

#### `meta.asyncValidating : boolean`

> `true` if the form is currently running asynchronous validation because this field was blurred.

#### `meta.dirty : boolean`

> `true` if the field value has changed from its initialized value. Opposite of `pristine`.

#### `meta.dispatch : Function`

> The Redux `dispatch` function.

#### `meta.error : String` [optional]

> The error for this field if its value is not passing validation. Both synchronous,
> asynchronous, and submit validation errors will be reported here.

#### `meta.form : String`

> The name of the `form`. Could be useful if you want to manually dispatch actions.

#### `meta.initial : any`

> The initial value of the field.

#### `meta.invalid : boolean`

> `true` if the field value fails validation (has a validation error). Opposite of `valid`.

#### `meta.pristine : boolean`

> `true` if the field value is the same as its initialized value. Opposite of `dirty`.

#### `meta.submitting : boolean`

> `true` if the field is currently being submitted

#### `meta.submitFailed : boolean`

> `true` if the form had `onSubmit` called and failed to submit _for any reason_. A subsequent successful submit will set it back to false.

#### `meta.touched : boolean`

> `true` if the field has been touched. By default this will be set when the field is blurred.

#### `meta.valid : boolean`

> `true` if the field value passes validation (has no validation errors). Opposite of `invalid`.

#### `meta.visited: boolean`

> `true` if this field has ever had focus. It will only work if you are passing `onFocus` to
> your input element.

#### `meta.warning : String` [optional]

> The warning for this field if its value is not passing warning validation.