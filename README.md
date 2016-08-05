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



## Single Responsibility

Define 1 React Component per file. The component should have a responsibility over a single functionality.

*Why?*:

- Better testing, More readable, eases maintainability.

- Avoid confusion no more "which component should i import ? " There's only one !

##### example 1 :
```javascript

/* BAD */
// user.js
export class UserAvatar extends React.Component{
}

export class UserProfile extends React.Component{
}


/* GOOD */
// user-avatar.jsx
export class UserAvatar extends React.Component{
}

// user-profile.jsx
export class UserProfile extends React.Component{
}

 ```


 ##### example 2 :
 ```javascript

/* BAD */
// navigation.jsx
 export class Navigation extends React.Component{
	 sendGoogleAnalytics() {}
	 loadUsers(){};
	 render(){};
 }

 /* GOOD */
 // navigation.jsx
 export class Navigation extends React.Component{
	 render(){};
 }

 // service-user.js
 export class ServiceUser{
	 static loadUsers(){};
 }

 // service-analytics.js
 export class ServiceAnalytics{
	static sendGoogleAnalytics(){};
 }

  ```
