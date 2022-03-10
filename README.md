# ZTM - Complete React Developer (w/ Redux, Hooks, GraphQL)

## React Key Concepts
Before, we had only HTML (to display elements on the page), CSS (for styling them), and JS (for interactivity).
Nowadays, there is a lot of browsers and they all work differently. JQuery allows us to use a unified API to make it work across all browsers.

Backbone.js -> to organize big applications with a lot of file
AJAX for interactivity like a SPA without reloading page -> JS became more important!

2010 -> AngularJS came out!
Facebook worked with AngularJS but data was flowing in an unorganized way with AngularJS, making it hard to maintain and to debug.

2014 -> Facebook came up with their own framework that would solve their problems that Angular could not solve.

DOM -> Document Object Model (tree representation of the page)
Javascript is manipulating that DOM for changing data, structure, adding and remove new elements. 
-> This is an imperative way of doing and changing the DOM that way is expensive

1. With React, it is a declarative style that we are adopting -> according to some data and state, change the DOM so it should look like what it should look like!
  Do not touch the DOM directly -> React is changing it for us.

2. UI is divided into many components.
  Components are functions that receives an input (props, state) and changes the display accordingly.
  They can be reused and shared (very practical). Building websites with lego blocks.

3. Uni-directional data flow (unlike Angular)
  React creates a Virtual DOM, a Javascript Object that looks like our DOM.
  State modification -> only the components that are below that state in the components will be updated -> only a part of the DOM changes 

### Job of a React Developer
1. Decide on Components
2. Decide the state and where it lives
3. What changes when state changes

## React Basics

NPM install and manages packages. 
NPX (Node Package Executor) is a tool to run node packages WITHOUT installing binaries (helps us avoid versioning, dependency issues and installing unnecessary packages that we just want to test).

create-react-app is a good package for getting us started into a new React project really fast.
Babel takes all the JS and transforms it into a JS that old browsers can understand.
Webpack is a package that makes our code more efficient (divides the JS code into chunks to make the app more modular).

A component is a self-contained piece of code that returns visual UI representation of the HTML.

For updating the state, don't update this.state directly but use this.setState. this.setState triggers changes in memory.
setState is a shallow merge.
=== is like asking the question "is it pointing to the same thing in memory?"

setState is asynchronous.

Non-SPA returns the HTML, CSS, JS everytime the routes changes to display new information.
SPA only need to get the initial HTML, CSS, JS and then further UI modifications (endpoints changes, search results) will be done directly via JS without reloading the page.

Be careful with anynomous functions because there are rebuilt on every render.

### Lifecycle methods (only for class components)
- componentDidMount: runs when component renders for the first time
- componentDidUpdate: runs when DOM is updated (new props, setState, forceUpdate)
- componentWillUnmount: runs before the component is removed from the DOM

### Functional Components
- Pure function -> returns the same output when provided the same input (completely isolated)
- Impure function -> output may changed depending on extenal factors/variables
- Side-effect -> make changes outside of the scope of the function

`const [searchField, setSearchField] = useState('')` instead of `this.setState({searchField: 'xxx'})`  with class components

Functional components are run from top to bottom and gets rerender not when calling the set function but when the state changes.

Use `useEffect` instead of `componentDidMount` for data initialization

```
useEffect(() => {
  // data initialization
}, []); // only runs this callback function if the dependency is changed
```

## DOM vs Virtual DOM 
In the real DOM, it is expensive to make changes(remove nodes, update nodes...).

Virtual DOM: Javascript representation of the real DOM.
On changes, it creates a copy of the Virtual DOM, and determines the children affected by the changes.
After the new Virtual DOM has been computed, the changes are reflected into the real DOM. 

To check on the browser what is being rendered in real-time, go to More Tools > Rendering > Check Paint Flashing.

### Updating packages
```
npm update -D

npm audit fix
```

### Prop drilling
This is bad! Because the intermediate components might not need the passed props.
Code might me too cluttered, making it hard to maintain and debug.

## Redux
One way to avoid props drilling is to use Redux. Redux is a library that allows us to manage a global state decoupled from the components.
No need to store the state in the class components anymore and all data will be stored and modified in one state unique to the application.
In Redux, we use:
- actions for triggering events that would potentially change the state
- reducers that handle the actions and contains logic on how the state should be changed

The redux state is passed to components via props. We use connect() for making a component use Redux.
```
import { connect } from 'react-redux';
import { createStructuredSelector } from 'reselect';
import { setCurrentUser } from './redux/user/user.actions';

class App extends React.Component {

  componentDidMount() {
    const { setCurrentUser } = this.props;
  }
}

const mapStateToProps = createStructuredSelector({
  currentUser: selectCurrentUser
});

const mapDispatchToProps = dispatch => ({
  setCurrentUser: user => dispatch(setCurrentUser(user))
});

export default connect(
  mapStateToProps,
  mapDispatchToProps
)(App);
```

In addition, reselect is a practical library for creating memoized "selector" functions, to avoid useless recomputation and rerendering, hence improving performance.
It acts like a computed property in Vue.JS that would be recomputed only when one of its argument changes.

```
import { createSelector } from 'reselect';

const selectUser = state => state.user;

export const selectCurrentUser = createSelector(
  [selectUser],
  user => user.currentUser
);
```

```
import { createSelector } from 'reselect';

const selectCart = state => state.cart;

export const selectCartItems = createSelector(
  [selectCart],
  cart => cart.cartItems
);

export const selectCartHidden = createSelector(
  [selectCart],
  cart => cart.hidden
);

export const selectCartItemsCount = createSelector(
  [selectCartItems],
  cartItems =>
    cartItems.reduce(
      (accumalatedQuantity, cartItem) =>
        accumalatedQuantity + cartItem.quantity,
      0
    )
);

export const selectCartTotal = createSelector(
  [selectCartItems],
  cartItems =>
    cartItems.reduce(
      (accumalatedQuantity, cartItem) =>
        accumalatedQuantity + cartItem.quantity * cartItem.price,
      0
    )
);
```

https://redux.js.org/usage/deriving-data-selectors