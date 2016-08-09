# react-guidelines
Ouicar React Guidelines &amp; Best Practices
## Purpose
This document describes best practices and guidelines used by the frontend team at Ouicar.
This style guide has two main purposes :
-  Present coding conventions with real cases examples
-	 Allow new developer joining Ouicar to easily embrace our development philosophy

For each rule, we explain why and how we do it.
These guidelines are based on our development experience with React Ouicar.

It's an opinionated document so you may or may not agree with all explanations. However, it worked for us so why not for you :-)

## Table of content

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
