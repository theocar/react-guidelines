# React Guidelines
> OuiCar React Guidelines &amp; Best Practices


## Purpose

This document describes best practices and guidelines used by the frontend team at [OuiCar](http://www.ouicar.fr/).
This style guide has two main purposes :
-  Present coding conventions with real cases examples
-	 Allow new developer joining Ouicar to easily embrace our development philosophy

For each rule, we explain why and how we do it.
These guidelines are based on our development experience with [React](https://facebook.github.io/react/) at OuiCar.

It's an opinionated document so you may or may not agree with all explanations. However, it worked for us so why not for you 🙂.


## Table of content

  1. [Introduction](#introduction)
	1. [React basic guidelines](#react-guidelines)
		1. [Naming Conventions](#naming-conventions)
		1. [Components: break them down](#break-them-down)
  1. [Intermediate applications: smart and dumb components](#containers-and-dumb-components)
	1. [Discussion about top-down](#top-down)


## Introduction

### Resources
##### Some very insightful resources on the react eco-system:
- MUST WATCH: [Hacker Way: Rethinking Web App Development at Facebook](https://www.youtube.com/watch?v=nYkdrAPrdcw)
- [Dan Abramov - Live React: Hot Reloading with Time Travel at react-europe 2015](https://www.youtube.com/watch?v=xsSnOQynTHs)
- [On immutability and immutableJS](https://www.youtube.com/watch?v=I7IdS-PbEgI)

##### Insightful documentations about the React way:
- https://redux.js.org/

##### Other interesting libraries:
- [reselect](https://github.com/reactjs/reselect)
- [immutable](https://facebook.github.io/immutable-js/)
- [redux-sage](https://github.com/redux-saga/redux-saga)

##### For curious people:
- [DraftJS](https://draftjs.org/)


### Context
At Ouicar we use the following stack:
- *React* for view management
- *React-router* for routing
- *FlowType* for type checking
- *Redux for* store management
- *Redux-saga* (enhanced with effects) for side effects

### Foreword
The idea behind this doc is to rationalize the way we build the ouicar frontend application.


* *React*:

	React was created with the idea that old web development was made easier by the fact that the server acted like a **state machine**. Indeed, when a user made a request, the server would find the user and its data in the database and generate an HTML page that represented the current state of this user.
	
	No dynamic content, no dynamic user interaction, no complexity. One state corresponded to one view.
	
	With frontend frameworks such as angular, which works notably with double variable bindings, the complexity of an error could grow exponentially making the work of debugging tedious.
	
	React solves to that problem with one-way binding and top down approach. The React components tree acts as a **state machine**: for one application state corresponds exactly one view.
	
	So our goal is to create an application that is very easy to reason about. A big state machine with sub state machines, each composed of sub state machines and so on...


* *Redux*:

	*Redux* (a flux framework, see the video [here](https://www.youtube.com/watch?list=PLb0IAmt7-GS188xDYE-u1ShQmFFGbrk0v&time_continue=622&v=nYkdrAPrdcw)) works hand to hand with React by providing a way to easily store application data. Data can be modified through **synchronous actions**, making it very simple to reason about. 
	
	The data store is then connected to the React components tree that handles the view. 
	
	Voilà, objective fulfilled: **one state = only one view ==> pure view function!**
	
	Don't hesitate to refresh your memory on functional programming, it is core to the React-redux philosophy. [Here is a very good tutorial](https://maryrosecook.com/blog/post/a-practical-introduction-to-functional-programming) :)


* *React-router*:
	React-router integrates well with the above basic stack **by using the URL as part of the global state of the application**. You can specify route params, query params and what part of the React components tree should be active when a specific route is mounted. Route params and query params will be included in your global state via props passed to the child component of the route.
	
	
* *Redux-saga*:
	As mentioned above, redux handles store state updates through **synchronous actions**. This a very important point as it ensures that your global application acts as a pure (view) function. One action will trigger a store update, which will trigger a React components tree update. That's it. No side effect, no surprise.
	
	But what about side effect then? How do you handle API calls for example? That's when *Redux-saga* comes into place. The idea of redux-saga is to handle your side effects in one place, and then dispatching synchronous actions back to your system, to handle asynchronous API call return values for example.
	
	**As far as Redux is concerned, only synchronous actions are being seen. Easy.**
	
	
## 1. React basic guidelines

### Naming conventions

1) Folders

* Folder name should be in lowercase and caret-separated
* Folders represent a goup of related components. 
	Components can be related by a feature (example: *payment* folder) or by a problem they solve (example: *form-options*).
* Limit the depth of the folders tree if possible
* If there are too many files in a folder, sub-folders should be created

*Why?*:

- Too many nested folder can make it hard to find your code.
- Too many files in a folder can make it hard to find your code.
- Smart file organization improves development speed.

2) Files

* File names should be in lowercase and caret-separated
* A file name describes what it contains **in the context of the containing folder**
* Sometimes files are seperated by **.**, this is equivalent to a folder, but flattened.
* Suffix component files with .jsx (not .js)

*Why?*:

- Helps to easily differentiate files describing components and other containing regular Javascript.

3) Components

* We recommend prefixing your component name with the name of the parent folder.
* When searching for how to prefix your component name, think of the react dev-tool and what
you would like to see when searching for your component in the components tree.


*Why?*

- Helps to easily find the component within your react dev-tools.
- Good component name allows to rapidly grab the meaning of it within its folder context.
- Too much prefixing is useless, but you need to understand what the component does when
you see it surrounded by its parent component.

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

### Components: break them down

React components can be compared to legos. Legos are small and useless when taken alone, but can be assembled to form big and complex pieces of [work](http://lolwat.me/imagearticle/201709/1505925304-construction-lego-enorme-grande.jpg). Actually, React is even more powerful than that, because once you have built a component, it can be reused anywhere, as many time as you want. No extra work, no code duplication.
Components are self explanatory and generally come as black boxes with an explicit API. Inside, they can handle custom cases to fit in many different situations.
Components are only responsible for themselves. They should generally have no side effects on their own but rather allow their smart parents, through hooks, to take actions.


#### Define one exported React Component per file. 

The component should have one responsibility over a single functionality. Inside a file you can still declare several components for the sake of clarity and component smallness but **only one component should be exposed**

*Why?*:
- Better testing, More readable, eases maintainability.
- Avoid confusion, no more "which component should I import ? " There's only one!

##### example:
```javascript
/* BAD */
// user.js
export class UserAvatar extends React.Component{
}

export class UserProfile extends React.Component{
}


/* GOOD */
// user-avatar.jsx
export default class UserAvatar extends React.Component{
}

// user-profile.jsx
export default class UserProfile extends React.Component{
}
```
 
 
#### Components should have a simple and predictable API

Components are small applications for themselves. They answer one problem. They are dumb. They want to be as independent from their context as possible.

*Why?*:
	- Better reusability
	- Faster construction: if your components are simple and predictable, they can be reused. They can be assembled to form bigger, but still simple and predictable, components. These can then be reused to form bigger components etc etc...
	- Easier to reason about: components act as black boxes that answer one problem and are very easy to reason about. Complexity is pushed to the edges of the leaves! When constructing your big and smart component, you wouldn't have to care about its children components, they manage themselves (if you respect their APIs)!
	
##### example:
```javascript
/* BAD */
// user.jsx

// See how User render function is not clear.
// Nothing in it is reusable. Imagine somewhere you need another UserAvatar? => code duplication and no simple API.
export class User extends React.Component{
	render() {
		const {user} = this.props;
		
		return (
			<div>
				<ul>
					<li><Link to={'/'}></Link></li>
					<li><Link to={'/about'}></Link></li>
					<li><Link to={'/bookings'}></Link></li>
				</ul>
				
				<div className='user-avatar-wrapper'>
					<img 
						src={user ? user.imgSrc : placeHolderSrc}
						className='user-avatar' 
					/>
				</div>
				
				<div className='user-profile-wrapper'>
					...
				</div>
			</div>
		)
	}
}


/* GOOD */
// user.jsx
import Navbar from './navbar.jsx';
import UserAvatar from './avatar.jsx';
import UserProfile from './profile.jsx';

// Now see how the User component becomes simple to read and reason about.
// User is only responsible for assembling small components but do almost nothing itself!
export class User extends React.Component{
	render() {
		const {user} = this.props;
		
		return (
			<div>
				<Navbar />
				
				// See how UserAvatar can now handle custom operations very simply.
				// When using UserAvatar, you don't have to worry about display,
				// You simply mention what you want and it will be handled accordingly.
				// Don't hesitate to allow custom styles inside small components to make them very customizable and reusable!
				<UserAvatar 
					size='medium' 
					user={user}
					
					// These checks could even be automatically performed inside the component
					// But for the sake of example we expose them here.
					color={user.specialColor} 
					withPicture={user.withPicture}
				/>
				
				<UserProfile 
					user={user}
				/>
			</div>
		)
	}
}
 ```

#### Don't let your components grow big

Sometimes you feel you have a very long component that for example uses several subrender functions.
Well there you could split this component into several small components (functional or not). These small components would just be sugar for making your small render functions more explicit and easy to reason about. Also you would not need to put these small components into separated files as they are not needed anywhere else, they are just internal library for this component, like an internal subrender function would be.

##### example
```javascript

// Initially...
export class Home extends React.Component{
	props: {
		prop1: any,
		prop2: any,
		prop3: any,
		prop4: any,
	}

	renderContent1 = () => {} // Some markup returned, uses prop1
	renderContent2 = () => {} // Some markup returned, uses prop2
	renderContent3 = () => {} // Some markup returned, uses prop3
	renderContent4 = () => {} // Some markup returned, uses prop4

	render() {
		// BAD: by looking at the code we don't know what part of the props each renderFunction needs
		return (
			<div>
				{this.renderContent1()} // use prop1 ?
				{this.renderContent2()} // use prop2 ?
				{this.renderContent3()} // use prop3 ?
				{this.renderContent4()} // use prop4 ?
			</div>
		);
	}
}

// Would become...
const SubComponent1 = ({ prop1 }) => {} // some markup
const SubComponent2 = ({ prop2 }) => {} // some markup
const SubComponent3 = ({ prop3 }) => {} // some markup
const SubComponent4 = ({ prop4 }) => {} // some markup
// And it is reusable!

export class Home extends React.Component{
	render() {
		const { prop1, prop2, prop3, prop4 } = this.props;
		return (
			<div>
				<SubComponent1 prop1={prop1} /> // We see it uses prop1 !
				<SubComponent2 prop2={prop2} /> // We see it uses prop2 !
				<SubComponent3 prop3={prop3} /> // We see it uses prop3 !
				<SubComponent4 prop4={prop4} /> // We see it uses prop4 !
			</div>
		);
	}
}
```

## Intermediate applications: containers and dumb components

There is two types of components. Smart components, or container, and dumb components or pure view components.
Take a look at https://redux.js.org/docs/basics/UsageWithReact.html for a basic overview.

#### Smart components, or containers:
* Are applications or micro-services themselves if you consider them + their children (data not included).
	They interact with other applications (other containers for example) via dispatch, but they handle everything from data computations/derivations to display.
	Of course in our case data are stored and managed in redux, API call are done in helpers BUT these are just additional layers to make our lives easier by better separating concerns.

* Are the interface between the business logic and the pure view.
	They handle the interactions inside AND outside of their scope of the sub-application that they represent. Only them can do that.

* Like every applications, they have a state they depend on. Where they get their state from is another story.
	They can get it through a redux-connect, through props or even through internal component state. In our case we use redux so most of the time the state will be contained in Redux.

* They are very explicit with their dumb children components. They use them as puppets, pure libraries that know as little about the business logic as possible, specifying everything they need from the component. If a functionality is missing, the hook should be added inside the dumb component after having asked yourself: "Is it still my responsability as a dumb component to handle this part of the view logic?". If so, go ahead and add the endpoint to the dumb component API.

* They compute derived data and pass them down to their dumb components.

* They use dumb component hooks, which names describe a view action (as much as possible), to trigger actions related to the business logic OR related to views outside of its sub-application scope.

* They are responsible for the logical view of their scope but not to the pure view (how things look).
	-> They compute derived data (logical view) and pass them down to dumb components.

* They never display markup, they should have only one child component, because they are not aware of how things look. They just know what are the data that need to be shown and the logical actions that need to be triggered.


For the sake of example we won't use connect react-redux helper here, but again this would make our lives easier.

```javascript
/* BAD */
// car/ListContainer.jsx

type Props = {,
	cars: [{
		id: string,
		name: string,
		hasBeenFullyLoaded: bool,
		toBeRepaired: bool
	}],
	dispatch: (action: Action) => void,
	user: {
		id: string,
		hasAlreadyPaid: bool
	}
}

export class CarListContainer extends React.Component {
	props: Props

	// BAD: this is pure display logic
	componentDidMount() {
		$('.beautiful-div').triggerAnimation();
	}

	// BAD: this is pure display logic
	triggerAnimation = () => {
		// Some code...
	};

	// BAD: we are mixing business logic with display.
	// The container should only say smth about the logic,
	// Like: hasUserAlreadyPaid -> the dumb component will display if this fn returns true
	displayAdditionalContent = () => {
		if (this.props.user && this.props.user.hasAlreadyPaid) {
			return (
				<div className='some-class'>
					Bonjour
				</div>
			)
		}
	}

	render() {
		return (

			// BAD: markup = how things look
			<div className='beautiful-div'>
				<CarList

					// BAD: no control over dumb component which will choose 
					// itself what to do
					// ALSO BAD: the scope of dispatch is generally the full app,
					// so the dumb component can reach outside of the scope 
					// of the sub-application.
					dispatch={this.props.dispatch}

					// In the dumb component CarList, cars should be filtered given a 
					// criterium (example price < 30 euro per day) so...
					// BAD! The filter relates to business logic, so filtering 
					// should be done in the container
					cars={this.props.cars}

					displayAdditionalContent={this.displayAdditionalContent}
				/>
			</div>
		);
	}
}

/* GOOD */
type Props = {
	cars: [{
		id: string,
		name: string,
		hasBeenFullyLoaded: bool,
		toBeRepaired: bool
	}],
	dispatch: (action: Action) => void,
	user: {
		id: string,
		hasAlreadyPaid: bool
	}
}

export class CarListContainer extends React.Component {
	props: Props

	// BAD: this is pure display logic
	componentDidMount() {
		this.doSomethingsWithBusinessLogic()
	}

	doSomethingsWithBusinessLogic = () => {}

	hasUserAlreadyPaid = () => {
		const {user} = this.props;
		return user && user.hasAlreadyPaid;
	}

	addToCart = carId => this.props.dispatch({ type: 'addToCart', carId });

	getLoadedCars = filter(cars, car => car.hasBeenFullyLoaded);

  render() {
		const {cars} = this.props;

		return (
			<CarList
				// GOOD: the cars are already filtered logically.
				// The dumb component only know what to display, 
				// not why some elements were filtered.
				// The dumb component is now reusable in "many" circumstances
				cars={this.getLoadedCars(cars)}

				// GOOD: the dumb component here can display disabled elements,
				// We tell him what field to use, but the dumb component does not know
				// the relationship to the logic.
				disableField='toBeRepaired'

				// GOOD: the dumb component what the selection is for, but it knows that
				// car elements can be selected and that it needs to call this callback
				// The name "onSelect" is as dumb as possible, 
				// it assumes nothing on the logic behind
				onSelect={this.addToCart}

				// GOOD: the hook of the dumb component refers to display logic,
				// The hooked function of the container refers to business logic.
				// Plus, the dumb component only takes care of the view now
				shouldDisplayAdditionalContent={this.hasUserAlreadyPaid}
			/>
		);
	}
}
```

#### Pure view components:

* They do not know anything about the logic of your application. They do not compute derived data from the state, nor do they activate things based on logic, except when it is pure view logic that they are responsible for.
	Example: A list that should display a list filtered by a certain criterium -> the view component should not handle the filtering operation, because how to filter the data given the criteria is a business logical operation, not a pure view operation.
	Other example: Now imagine your components take a list which has certain items that should appear disabled. Here, the container passes the list AND the criterium to use to know the disabled items, and the view component is responsible for rendering correctly the disability given the criterium, because it is view logic!

* They are pure functions:

	* They have almost no interaction outside of their scope (their own or their children ones). The only interaction outside of their scope that they can have is through hooks that they provide to their parents as an API. It is very important to understand that. Pure components should be like a library. They have a very clean API that can be understood from outside the component (ie the parent).

	* A pure component can be viewed as a pure function that generates markup, it creates a stable view given the parameters. In our case, the parameters are the props AND the state.
		That means that you cannot render something that depends on something else than props or state. Example: A component that would act differently given the url -> not pure given props and state anymore.

	* They can have an internal state, this does not alter the purity of then render function. You should only use component state to handle a part of the global state (generally the view state) that nobody else is interested in.

* The hooks names should be as non-opinionated as possible, as well as the name of the component itself.
	Sometimes/Often components handle a very specific part of the UI and you cannot find a non-opinionated name. That's ok, but nonetheless try to think about the component generally do, what it could be used for in a other context and you will generally find a better name that the original one you came up with.

* Other implementation details:
	* All markup should be contained inside view components: HTML AND CSS!
	* You can use component state, it's ok. But only when you need to handle a part of the UI that nobody else is interested in. For example, a CSS transition.
	* Be as dumb as possible. Your component should be reusabled, easily extended or split
	* Dumb components can have containers as children. To them they are just black boxes that take props and are responsible for a part of the UI

```javascript
/* BAD */
// car/list.jsx

type Props = {
	cars: [{
		id: string,
		name: string,
		hasBeenFullyLoaded: bool,
		toBeRepaired: bool
	}],
	dispatch: () => void,
	displayAdditionalContent: () => ReactElement
}

export class CarList extends React.Component {
	props: Props

	// BAD, component decides what to do
	addToCart = carId => this.props.dispatch({ type: 'addToCart', carId })

  render() {
		const {cars, displayAdditionalContent} = this.props;
		const toDisplayCars = filter(cars, car => car.hasBeenFullyLoaded); // <-- BAD, dumb components chooses how to filter list!

		return (
			<div>
				{
					map(toDisplayCars, car => (
						<button
							key={car.id}

							// BAD: Component knows what specific field to use, not reusable in another context!
							disabled={car.toBeRepaired}

							// BAD, naming is to tightly coupled to logic
							onClick={() => this.addToCart(car.id)}>  
							{car.name}
						</button>
					)
				}

				// BAD: the view component is not in charge of the view.
				// Some of its view is outside of his scope (parent decides what to display)
				{displayAdditionalContent()}
			</div>
		);
	}
}

/* GOOD */
type Props = {
	cars: [{ // <-- Already filtered by container parent!
		name: string,
	}],
	disableField: string,
	onSelect: Function,
	shouldDisplayAdditionalContent: bool,
}

type State = {
	selectedCarId: ?string
}

export class CarList extends React.Component {
	props: Props
	state: State

	// No issue here, the display of the car has no impact on the global state
	// No concrete action has been taken by the user, the action is only a view action
	// Of course, we could handle that in the store, but if it is useless, why bother?
	displayCar = selectedCarId => this.setState({ selectedCarId });

	// GOOD: Now the dumb component handle his own view.
	displayAdditionalContent = () => {
		return (
			<div className='some-class'>
				Bonjour
			</div>
		)
	}

  render() {
		// Cars are already filtered by the container parent.
		const {cars, onSelect, shouldDisplayAdditionalContent} = this.props;
		const {selectedCarId} = this.state;

		return (
			<div>
				{
					map(toDisplayCars, car => (
						<div>
							<button

								// It's ok to be specific here, it is a view component indeed.
								// So it is tighly coupled to the CSS (even though better solutions exist).
								className='car-button'

								// GOOD: Component handle the disabling of the view, but does not
								// know what the data logic is. The container parent told him <- REUSABLE
								disabled={car[props.disableField}

								// GOOD, we do not assume anything on the logic behind,
								// All we know is this car has been selected <- REUSABLE
								onClick={() => onSelect(car.id)}>
								{car.name}
							</button>

							<button
								// The component is responsible for the preview, so it allows to select...
								onClick={() => this.displayCar(car.id)}>
								{`Display ${car.name} (no selection will be made)`}
							</button>
						</div>
					)
				}

				// ... and to display what was selected
				// Note that this could have been handled by a child component, it would still be
				// In the scope of this component.
				{
					selectedCarId &&
					<div className='displayed-car'>
						{selectedCarId}
					</div>
				}

				{ 
					// GOOD: now container handles the business logic
					// The dumb component knows whether it should display or not and handles the if on the view side.
					shouldDisplayAdditionalContent &&
					this.displayAdditionalContent()
				}
			</div>
		);
	}
}
```

## Discussion about top-down

Transfer *props* from the parents to the children.
It is ok to use state inside dumb components only when the state concerns a part
of the general state of the application that is not used by anyone else other than
the dumb component itself.

*Why?*

The top-down components are simpler to reason about, reuse and test.
But top down should be used wisely as mixing dumb substate of the application
with business state makes the line between business logic and view logic thinner.

You should use top-down and store dispatch only when other part of the application
outside of the scope of the dumb component needs to know what going on.


```javascript
// car/list.jsx
class CarList extends React.Component {
	// It's ok, it is just a temporary display with
	// No impact outside of the scope of the component.
	state: displayedCarId

	tempSelectCar = carId => this.setState({ displayedCarId: carId });

  render() {
    const {cars} = this.props;
		const {displayedCarId} = this.state;

		return (
			<div>
				{
					map(cars, car => (
						<div>
						<a onClick={() => tempSelectCar(car.id)}>
						{car.name}
						</a>
						</div>
					))
				}

				{
					displayedCar ?
						<div>
							<TempDisplayCar
								car={cars[displayedCarId]}
							/>
						</div> :
						<div>
							No car selected
						</div>
				}
			</div>
		);
  }
}

export default connect(state => ({
  cars: state.cars
}))(CarList);
```
