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



## Naming Conventions

#### Files

We recommend prefixing your file name with the name of the parent folder

*Why?*:

- Helps to easily find the file with your IDE


```javascript

/* BAD */
// car/list.jsx
export class List extends React.Component{
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

Use the .jsx extension for React Components

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

- Helps to easily differentiate files describing components and other containing regular javascript  

#### Folders

- Don't create more than 2 levels of hierarchy of folders
- Use dots in your filename to indicate hierarchy

*Why?*:

- To many nested folder can make it hard to find your code


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
