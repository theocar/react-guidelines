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
	1. [Introdution](#introduction)
	1. [Single Responsibility](#single-responsibility)
  1. [Intermediate applications: containers and dumb components](#containers-and-dumb-components)
  1. [Generally top-down](#top-down)
  1. [Naming Conventions](#naming-conventions)


## Introduction

##### Some very insightful resources on the react ecosystem:
- [Hacker Way: Rethinking Web App Development at Facebook](https://www.youtube.com/watch?v=nYkdrAPrdcw)
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

#### React
The idea behind React is to


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
export default class UserAvatar extends React.Component{
}

// user-profile.jsx
export default class UserProfile extends React.Component{
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
 export default class Navigation extends React.Component{
	 render(){};
 }

 // service-user.js
 export default class ServiceUser{
	 static loadUsers(){};
 }

 // service-analytics.js
 export default class ServiceAnalytics{
	static sendGoogleAnalytics(){};
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

					// BAD: no control over dumb components which will choose himself what to do
					// ALSO BAD: the scope of dispatch is generally the full app, so we give a function
					// outside of the scope of the sub-application, and the dumb component now can access everything.
					dispatch={this.props.dispatch}

					// In the dumb component CarList, cars should be filtered given a criterium (example price < 30 euro per day) so...
					// BAD! The filter relates to business logic, so filtering should be done in the container
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
				// The dumb component only know what to display, not why some elements were filtered.
				// The dumb component is now reusable in "many" circumstances
				cars={this.getLoadedCars(cars)}

				// GOOD: the dumb component here can display disabled elements,
				// We tell him what field to use, but the dumb component does not know
				// the relationship to the logic.
				disableField='toBeRepaired'

				// GOOD: the dumb component what the selection is for, but it knows that
				// car elements can be selected and that it needs to call this callback
				// The name "onSelect" is as dumb as possible, it assumes nothing on the logic behind
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

## Generally top-down

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

## Naming Conventions

#### Files

We recommend prefixing your file name with the name of the parent folder.
When searching for how to prefix your file name, think of the react dev-tool and what
you would like to see when searching your component in the tree of components.

*Why?*

- Helps to easily find the file with your react dev-tools.
- To much prefixing is useless, but you need to understand what the component does when
you see it surrounded by his context components.


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

- Folders represent a feature
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
