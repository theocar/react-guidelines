# React Guidelines
> OuiCar React Guidelines &amp; Best Practices

## Purpose
This document describes best practices and guidelines used by the frontend team at [OuiCar](http://www.ouicar.fr/).
This style guide has two main purposes :
-  Present coding conventions with real cases examples
-	 Allow new developer joining Ouicar to easily embrace our development philosophy
-	 Allow new developer joining OuiCar to easily embrace our development philosophy

For each rule, we explain why and how we do it.
These guidelines are based on our development experience with [React](https://facebook.github.io/react/) at OuiCar.

It's an opinionated document so you may or may not agree with all explanations. However, it worked for us so why not for you 🙂.

## Table of content
TODO

## Top-down

Transfer *props* from the parents to the children. Do not use internal states in your components.

*Why?*

The top-down components are simpler to reason about, reuse and test.

```javascript
/* BAD */
// car/list.jsx
export class CarList extends React.Component {
  state = {
    cars: []
  };

  render() {
    const {cars} = this.state;
    ...
  }
}

// car/list.jsx
class CarList extends React.Component {
  render() {
    const {cars} = this.props;
    ...
  }
}

export default connect(state => ({
  cars: state.cars
}))(CarList);

/* GOOD */
// car/list.jsx
export class CarList extends React.Component {
  render() {
    const {cars} = this.props;
    ...
  }
}
```

## Naming Conventions

#### Files

We recommend prefixing your file name with the name of the parent folder.

*Why?*

- Helps to easily find the file with your IDE


```javascript

/* BAD */
// car/list.jsx
export class List extends React.Component {
}

/* BAD */
// car/list.jsx
export class ListCar extends React.Component{
}

/* GOOD */
// car/list.jsx
export class CarList extends React.Component{
}
  ```

Use the `.jsx` extension for React Components

```javascript

/* BAD */
// booking/card.js
export class BookingCard extends React.Component{
}

/* GOOD */
// booking/card.jsx
export class BookingCard extends React.Component{
}

  ```

*Why?*:

- Helps to easily differentiate files describing components and other containing regular Javascript.  

#### Folders

- Don't create more than two levels of hierarchy of folders.
- Use dots in your filename to indicate hierarchy

*Why?*:

- Too many nested folder can make it hard to find your code.


```javascript

/* BAD */
// car/edit/disable/form.jsx
export class CarEditDisableForm extends React.Component{
}

/* BAD */
// car/edit/disable/form/date-picker.jsx
export class CarEditDisableFormDatePicker extends React.Component{
}

/* GOOD */
// car/edit/disable.form.jsx
export class CarEditDisableForm extends React.Component{
}

/* GOOD */
// car/edit/disable.form.date-picker.jsx
export class CarEditDisableFormDatePicker extends React.Component{
}
  ```

