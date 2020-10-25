# How to create a To-Do list application using React Hooks

![Rect hooks](https://www.wisdomgeek.com/wp-content/uploads/2020/08/react-hooks.jpg)

# React hooks

React hooks have been around for quite some time. They have been widely adopted by the React community since then and have started becoming the defacto for newer React applications.

A react hook allows developers to hook into React features. React hooks allow building components by using functions instead of classes. They also give us the ability to have state in functional components. In addition, they provide a way to have life-cycle methods inside functional components.

In this post, we will learn about the most commonly used hooks (useState, useEffect, useReducer and useContext) by building a To-Do application.

**Prerequisites:** Basic understanding of React, JSX and JavaScript is required to follow along with this coding tutorial. Let’s start writing some code!

## Initial setup before getting into React Hooks

We will be using Create React App to build this simple application. Assuming we have bootstrapped a new application using either of the commands:

```
npx create-react-app todo-list-react-hooks-demo
yarn create react-app todo-list-react-hooks-demo
```

We can then open the directory, and then start working in here. Before getting started with the hooks part of the application, let us have the skeleton for the To-Do application in place. We will be having two different sections on the page. The top one will be for displaying the existing items in the to-do list and the bottom one will be to add items to the above list. Let us start with the top section and create it without any hooks related functionality.

## Item component

We will create an Item component that will be used to display the item. This will be a presentational component which takes the item as a prop and display it.

```js
import React from 'react';
const Item = ({ item }) => {
  return (
    <div>
      {' '}
      <span>{item}</span>{' '}
    </div>
  );
};

export { Item as default };
```

## ItemList component

We will also create an ItemList component that will contain a list of all the items that we have in the application. This will take the items as a prop and display them as an unordered list. It will make use of the Item component that we created before for displaying individual elements.

```js
import React from 'react';
import Item from './Item';

const ItemList = ({ items }) => {
  return (
    <div className="items-container">
      <ul>
        {items.map((item) => (
          <li>
            <Item key={item} item={item} />
          </li>
        ))}
      </ul>
    </div>
  );
};

export { ItemList as default };
```

Now that we have those setup, all we need is to hook them up in the Application component and pass the items as props to the ItemList.

## App component

For the initial setup, we will assume we have a list of items as an array. We will make this dynamic in the next section.

```js
import React from 'react';
import ItemList from './ItemList';

const App = () => {
  const items = ['item 1', 'item 2'];
  return (
    <div className="App">
      <header className="App-header">
        To Do items
        <ItemList items={items} />
      </header>
    </div>
  );
};
```

These should complete our initial setup of the application. If we run yarn start at this point, we will see an unordered list containing item 1 and item 2 on the screen. The App component is where we will have all our state-related information. It is our smart component with information about the application state. Now that we have the boilerplate ready, it is time to hook things in!

## First React hook: useState

The first hook that we will be looking into is the useState hook. As the name suggests, it allows us to hook state into our functional component.

As opposed to state in class components, useState does not work with object values. We can use primitives directly and create multiple react hooks for multiple variables if needed.

For using this hook, we need to import it from the react library. We can then initialize it like this:

```js
const [state, setState] = useState(initialState);
```

The initial value is what we want to assign to the variable and passing it to the useState function will return an array. The first value in this array will be the current value of the state object that will get created (initialState in this case). And the second value in the array will be a setter function that allows us to modify this state variable. We can technically name this anything we want since these are just variables, but following conventions is always a good practice.

**Note:** React hooks always need to be declared at the top of a function. This also helps preserve state in between all rendering that is happening for the component.

Now that we have an understanding of the useState React hook, we can use it in our ToDo application. We will replace the initialization of items to:

```js
const [items, setItems] = useState(['item 1', 'item 2']);
```

This will have no impact in terms of what we would see on the application, but we now have the items in a state variable instead of it being a plain variable before. We also have a setter function that we will use to update these items as we build on this functionality.

The code until this point can be found [here](https://github.com/saranshkataria/todo-list-react-hooks-demo/tree/9dd440ba53f5aa8c14933968b38ae65487a8e55d).

## Add a to-do item functionality

Now that we have the items as a state variable, we can modify it in functions using the setter function. So let us create the second section of our application which allows creating to-do list items.

We will create a different component for this. This component will have a form with an input field which will allow the user to enter the content of the new item that is to be added. It will also have a button which will add the item to the list. Since we want all state to be in the parent component, we will let the parent component pass in a function as a prop. This function will be invoked whenever the user clicks on the add item button. We will also clear out the text box after adding the item to the list.

We also need a variable to store the item as the user types in the input text box. For this, we will create an item state which will be local to this component. And what better than using our new friend useState?

The AddItemForm component will then look like:

```js
import React, { useState } from 'react';

const AddItemForm = ({ addItem }) => {
  const [item, setItem] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    addItem(item);
    setItem('');
  };

  return (
    <div>
      <p>Add item</p>
      <form onSubmit={handleSubmit}>
        <input value={item} onChange={(e) => setItem(e.target.value)} />
        <button>Add Item</button>
      </form>
    </div>
  );
};

export { AddItemForm as default };
```

Now that we have the functionality to add an item, we can modify our App component to declare the addItem function and also import the AddItemForm function that we just created.

Ideally, the function to add an item should check for duplicates, but for the sake of simplicity, we will assume that there are no duplicates and just add the newly created item to our items array.

```js
const addItem = (item) => {
  // assuming no duplicates for demo purposes
  setItems([...items, item]);
};
```

We have destructured the existing items array, added the newly created item at the end of the array, and passed this new array to our setItems function that we learned about earlier. People experienced with React should find this syntax familiar. There is not a lot of React hooks related stuff happening here apart from the setter function that we already know about by now.

We can also remove the default values from the useState initialization since we have everything in place to add new items using the application now.

If you want to take a peek at the code until this point, you can browse it [here](https://github.com/saranshkataria/todo-list-react-hooks-demo/tree/9f754bfa006dbae17e4bdf7ce46fc1acd7d2f172).

## Removing an item

Similar to the adding an item functionality, we will pass a remove item function from the parent component to the item list component which will be passed down to the item component. The item component will have a delete button denoted by an “X” and whenever the user clicks it, we will invoke this function to delete the item. The updated item component will now be:

```js
import React from 'react';

const Item = ({ item, removeItem }) => {
  return (
    <div>
      <span>{item}</span>
      <button onClick={() => removeItem(item)}>X</button>
    </div>
  );
};
```

The ItemList component will have a similar change of taking the remove item prop in and passing it to the Item component. Then, we will define the remove item function in the parent component as:

```js
const removeItem = (itemToBeDeleted) => {
  setItems(items.filter((item) => itemToBeDeleted !== item));
};
```

Since we assumed items to be unique, we can use the filter method to remove that item and then pass the filtered array to setItems. Again, not much new react hooks magic happening here. Pretty standard react code.

This brings us to the end of our introduction to useState and we have a working to-do application now. We can add items, remove them, and the application UI displays all of them accordingly.

The code until this point is available [here](https://github.com/saranshkataria/todo-list-react-hooks-demo/commit/c8eaa34b48a5ac399b58c8f1cea659b1d9e23daa).

We will learn about another hook next. We will add the functionality to persist these items to local storage so that they persist after the browser is refreshed.

# The useEffect hook

The useEffect hook is somewhat similar to the life-cycle methods that we are aware of for class components. It runs after every render of the component including the initial render. Hence it can be thought of as a combination of componentDidMount, componentDidUpdate, and componentWillUnmount.

If we want to control the behavior of when the effect should run (only on initial render, or only when a particular state variable changes), we can pass in dependencies to the effect to do so. This hook also provides a clean-up option to allow cleaning up of resources before the component is destroyed.

Before getting into the details, let us see the basic syntax of the effect:

```js
useEffect(didUpdate);
```

Here, didUpdate is a function that performs effects (mutations, subscriptions, timers, logging, etc.). It will get triggered after the component is rendered to the screen as well as on every subsequently completed render.

Getting back to our to-do application, let us put this in use to set the to-do data to local storage whenever we update the items in the ToDo list. In the App component, we will add:

```js
useEffect(() => {
  localStorage.setItem('items', JSON.stringify(items));
});
```

This sets a key-value pair in our local storage with the key being items and the value being a JSON representation of our items.

Next, when we are initializing the items in the app component, we will first check if there are any items in local storage or not. If there are, we will pass those to the setState function, else we will default to an empty array.

So the following piece of code:

```js
const [items, setItems] = useState([]);
```

becomes:

```js
const saveditems = JSON.parse(localStorage.getItem('items'));
const [items, setItems] = useState(saveditems || []);
```

We passed the same key (items) to localStorage.getItem as the one we had used before when we were storing them into local storage. Now, the application will check local storage first whenever the application is loaded (App component is rendered). If it finds some items in local storage, we will initialize them in our state, else we will initialize it with an empty array.

This should get our application up and running.

Since loading from local storage is a synchronous process, our code works fine. But if we were to use an asynchronous method of loading the items, we would have had to use the useEffect hook for that as well. So let us do that next.

```js
const [items, setItems] = useState([]);
useEffect(() => {
  const items = JSON.parse(localStorage.getItem('items'));
  if (items) {
    setItems(items);
  }
});
```

The above transformation seems like it should work, but it does not.

Can we think of the reason?

Since useEffect runs every time a component is rendered, and we are calling set items inside the effect, it will trigger another render of the component, which triggers useEffect again. And the cycle goes on. We have a circular reference issue here. This is where the dependencies that we discussed previously come into play.

## Conditionally firing useEffect

The useEffect hook takes in a second argument which is an array of values that the effect depends on. This way, useEffect is only triggered again if one of these values changes.

### Calling useEffect only when a specific value changes

Using the dependency array, we can pass in the state variable to useEffect to make it fire useEffect only when the variable’s value changes.

```js
useEffect(() => {
  const items = JSON.parse(localStorage.getItem('items'));
  if (items) {
    setItems(items);
  }
}, [items]);
```

Thus, this will solve our problem of circular reference and we are good to go. But we can do better!

Since we only want the values to be loaded from local storage on the initial application render, we need something like componentDidMount.

### Calling useEffect only once on component render

We can pass an empty array to useEffect as the second parameter to tell React to only run that effect only once. This tells React that the effect does not render on any values from prop or state and thus it gets executed only once on the initial render and never again after that.

```js
useEffect(() => {
  const items = JSON.parse(localStorage.getItem('items'));
  if (items) {
    setItems(items);
  }
}, []);
```

Thus we get to the desired result of loading from local storage only once on application load and this solves all our optimization problems too.

Now that we know about the dependency array, it will be a good practice to add it to the effect that we were using to save the items to save to local storage as well.

```js
useEffect(() => {
  localStorage.setItem('items', JSON.stringify(items));
}, [items]);
```

This also helps us understand why we did not merge the two calls in one single useEffect function. Since we have different conditions to trigger these effects on, we create multiple useEffect react hooks for them. This also helps keep all related code in a single function block and makes it more readable and maintainable.

Our App component will finally look like this:

```js
import React, { useEffect, useState } from 'react';
import AddItemForm from './AddItemForm';
import './App.css';
import ItemList from './ItemList';

function App() {
  const [items, setItems] = useState([]);
  const addItem = (item) => {
    // assuming no duplicates for demo purposes
    setItems([...items, item]);
  };

  const removeItem = (itemToBeDeleted) => {
    setItems(items.filter((item) => itemToBeDeleted !== item));
  };

  useEffect(() => {
    const items = JSON.parse(localStorage.getItem('items'));
    if (items) {
      setItems(items);
    }
  }, []);

  useEffect(() => {
    localStorage.setItem('items', JSON.stringify(items));
  }, [items]);

  return (
    <div className="App">
      <header className="App-header">
        To Do items
        <ItemList items={items} removeItem={removeItem} />
        <AddItemForm addItem={addItem} />
      </header>
    </div>
  );
}

export default App;
```

## Cleaning up resources used in useEffect

Though this is something we will not be needing for our small application. But it is a common practice to clean up resources when a component is being unmounted. In a class-based scenario, we used the componentDidUnmount method to do so.

useEffect provides a similar functionality too. And since we will be doing all our side effects related computations inside an effect. It makes sense to have the cleanup code inside the same function as well. That makes everything reside inside a single function, increasing readability and maintainability overall.

To do so, we return a function from the function that we pass to useEffect.

```js
useEffect(({ source }) => {
  const subscription = source.subscribe();
  return () => {
    // Clean up the subscription
    subscription.unsubscribe();
  };
});
```

The clean-up function is then executed whenever the component will be removed from the UI. Thus preventing memory likes like we are used to in React applications. This also ensures cleaning up of resources in case of multiple renders of the component.

We will not be using this in our application since there is no need for it, but for completeness’s sake, it was important for us to know about the existence of this cleanup part as well.

You can find the code written until now[here](https://github.com/saranshkataria/todo-list-react-hooks-demo/tree/845eb6b2ad9d12888748efb76ff7b6b08c087f6f) if you want to refer it.

#useReducer

![useReducer]('https://www.wisdomgeek.com/wp-content/uploads/2020/08/usereducer-react-hook.png')

Next, we will look at the useReducer hook. useReducer hook can be an alternative to useState (in fact, useState uses useReducer internally). Before getting into how to use the useReducer hook, we should understand what is meant by a reducer.

## What is a reducer?

If you are coming from a redux background, you can probably skip this section. But for those who are not, let us first understand what a reducer is and what is the need for it. Then we will dive into the useReducer hook.

The first principle that we should remember before getting into a reducer is that the state is represented as a single immutable tree. So whenever we make a change to state, it is an explicit change. Typically, we use the setState function to make changes to the state. When using redux, we will not be making these changes directly to the state. We will instead be using reducers, which are functions that determine how to change the application state. For making changes to the application state, we will call these reducers with an action to specify what happened.

Let us consider a simple counter example:

```js
function Counter({ initialCount }) {
  const [count, setCount] = useState(initialCount);
  return (
    <>
      Count: {count}
      <button onClick={() => setCount(initialCount)}>Reset</button>
      <button onClick={() => setCount((prevCount) => prevCount - 1)}>-</button>
      <button onClick={() => setCount((prevCount) => prevCount + 1)}>+</button>
    </>
  );
}
```

There are two actions that are happening here: increment and decrement. So we can abstract them out into a reducer function that takes an action as an argument:

```js
function reducer(count, action) {
  switch (action) {
    case 'increment':
      return count + 1;
    case 'decrement':
      return count - 1;
  }
}
```

An action is the minimal representation of the change to application data (or state).

## Why do we need reducers?

The first question that comes to mind is, why do we need reducers or actions? Even though it is redundant in our counter example, but for larger applications, there can be a lot of state-related operations happening everywhere. So, instead of having these spread out all across our application, and inside different components, we move it all into a reducer function. The reducer function then becomes a single source of truth for all application state-related changes. Thus a reducer takes in two arguments, state, and action, and returns the new state of the application.

```js
(state, action) => newState;
```

And all the different actions across the application are now in a single place, and the reducer function updates state according to the action it receives. The reducer is also a pure function, that is it does not have any side-effects.

Overall, all these properties of the reducer function make it perfect for testing state changes independently and in isolation. The same input should always return the same output.

## The action in a reducer function

Even though we touched on the action above, it was a simplified version of what action looks like. Sometimes, we want to pass in a value along with the action as well. If we were to increment by 5 instead of 1, our previous example would require a different action altogether.

Instead, a standard has been laid out for actions. The only requirement is that the action is an object that has a type property defining what the action is. Also, the value of the type property should not be undefined. It can be an object as well, but the best practice is to use a string because strings are serializable. Any additional information can be passed in as different properties.

Putting all of this together, our updated reducer would now look like:

```js
const initialState = { count: 0 };

function countReducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}
const newState = countReducer(initialState, 'increment'); // returns {count: 1}
countReducer(newState, 'decrement'); // returns {count: 0}
```

With all this in place, the component does not need to know anything about updating the state. All the components need to know is that they will dispatch an action with the type of what happened, and the reducer will take care of everything else. Thus we achieve more maintainable code that adheres to the single responsibility principle.

## React’s useReducer hook

Now that we have an understanding of reducer functions, we can dive into the useReducer hook implementation. As we have already seen, the useReducer hook is useful for managing complex state and state transitions.

Another benefit of the reducer is to not have to pass props manually around from parent components to child components. This is possible because all the state-related logic is defined inside the reducer function. And the child component only needs to call the reducer function with the appropriate action.

Before we start using the useReducer hook, we need to have the reducer defined. We already did that above for our counter example. Next, we can reduce the useState call with useReducer and pass the reducer to it and the initial state that we want to assign.

```js
const initialState = { count: 0 };
const [state, dispatch] = useReducer(reducer, initialState);
```

Like useState, useReducer returns an array of two variables. The first one refers to the current state of the application, and the second is a dispatch function that we can use to send actions to the reducer. Invoking the dispatch function would change the state of the application, depending on the action that we invoke it with. Thus our counter example would get converted into the following code using the useReducer hook:

```js
const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    case 'reset':
      return initialState;
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'reset' })}>Reset</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
    </>
  );
}
```

It is also important to note that React guarantees that the calls to the dispatch function are stable and will not change on re-renders. Therefore we do not need to put it in the useEffect dependency list.

## Applying useReducer hook to our To-Do list application

Let us now apply the hook to our ToDo list application that we had built in the previous blog post.

We will define an items reducer as follows:

```js
const itemsReducer = (state, action) => {
  switch (action.type) {
    case 'POPULATE_ITEMS':
      return action.items;
    case 'ADD_ITEM':
      return [...state, action.item];
    case 'REMOVE_ITEM':
      return state.filter((item) => item !== action.itemToBeDeleted);
    default:
      return state;
  }
};
```

The three actions correspond to fetching of data, adding an item, and removing an item. These are self-explanatory in what we are trying to do here with respect to the action type that we receive. Next, we will start making use of this reducer in our App component. We will replace useState with our useReducer hook

```js
const [items, itemsDispatch] = useReducer(itemsReducer, []);
```

We can name the first (state) variable whatever we want it to be. It is better to be more explicit about what it refers o since there might be multiple reducers in an application. So we did not name it state as we did in our example before.

Now that we have access to our state variable and dispatch function, we can use them in our component. Getting into our first useEffect call:

```js
useEffect(() => {
  const items = JSON.parse(localStorage.getItem('items'));
  if (items) {
    setItems(items);
  }
}, []);
```

We no longer have access to setItems. But we created an action POPULATE_ITEMS in our reducer to populate the items which can be used here. So we will invoke our dispatch function here instead:

```js
useEffect(() => {
  const items = JSON.parse(localStorage.getItem('items'));
  if (items) {
    itemsDispatch({ type: 'POPULATE_ITEMS', items });
  }
}, []);
```

When we invoke this dispatch function, it will invoke our reducer with action type POPULATE_ITEMS. And since we passed in the items (using the shorthand notation), the items reducer returns those items and saves them in the application state.

For the other useEffect where we were saving items to state, we do not need to do anything since we were not doing any state manipulation.

Next, we will do the same thing for the other actions that we have, that is adding an item and removing an item.

```js
const addItem = (item) => {
  // setItems([...items, item]);
  // becomes:
  itemsDispatch({ type: 'ADD_ITEM', item });
};

const removeItem = (itemToBeDeleted) => {
  // setItems(items.filter((item) => itemToBeDeleted !== item));
  // becomes
  itemsDispatch({ type: 'REMOVE_ITEM', itemToBeDeleted });
};
```

And that concludes our refactoring to use useReducer hook in our code.

You can find the code changes [here](https://github.com/saranshkataria/todo-list-react-hooks-demo/commit/d2f2c98c1b9918eac8a909a3d8153497ff289156) and the code at this point [here](https://github.com/saranshkataria/todo-list-react-hooks-demo/tree/d2f2c98c1b9918eac8a909a3d8153497ff289156).

# Context API and the useContext React hook 

![useContext](https://www.wisdomgeek.com/wp-content/uploads/2020/09/useContext-react-hook.jpg)

The next react hook we will look into is the useContext hook. As we saw above, we had to lift the state up to our root component in order to be able to share it among multiple children components. We then passed in the corresponding state objects to the children components as props to have shared state and a single source of truth at the parent level.

This problem was initially solved by using third party libraries for state management such as Redux. But in React 16.3, the React team introduced an official context API.

## The React context API

Now that context API is built directly into the react library, we can use this context API to avoid the problem of passing data down through multiple layers of components. The context API makes it possible to pass data through components without having the need to pass props down manually through every level of the component tree.

Thus context is useful when the same data is needed by many components at different levels of a React application. It is worth noting that it makes component reuse a bit more difficult, so it should be used sparingly.

Context lets you broadcast data and changes to that data to components that have subscribed to it.

## Context Object

To start using the API, we need to create a context object:

```js
const MyContext = React.createContext(defaultValue);
```

This object will be our source of values to components that subscribe to it. Whenever React is going to render a component that has subscribed to a context object, it will read it’s value from the nearest Provider above it in the tree.

## Context Provider

Every context object comes with a Provider component which ,as the name suggests, provides the value of the context object to all children components. It also allows the consuming components to subscribe to changes in the context value. It can be declared as:

A provider can be connected to more than one consumers and providers can be nested as well. They might override values deeper in the tree, since the subscribers consume values from the nearest provider.

## The useContext react hook

Now that we have a provider, we can start consuming the value that was assigned.

```js
const value = useContext(MyContext);
```

Making use of the useContext hook, we subscribe to the context object and any changes that are made to it. The value that we receive from the useContext hook will always be equal to the value being passed from the nearest provider in the tree. If there is no provider above the component, the value will be equal to the default value that was passed to createContext().

All subscribers/consumers of a provider are re-rendered whenever the value prop of the provider is changed.

Now that we understand the context API, let us get into using it with hooks.

## Putting it all into place in our sample To-Do list application

If you have not been following along our sample to-do list application, you can find the code that we have built [here](https://github.com/saranshkataria/todo-list-react-hooks-demo/tree/d2f2c98c1b9918eac8a909a3d8153497ff289156). We will now start hooking in the context API to pass the items using the context API instead of passing them as props from the App component. We will use the useContext hook for doing so, but we first need to create the context object.

So, we will create a new context folder and in it, we will add a items context file:

```js
import React from 'react';
const ItemsContext = React.createContext();
export { ItemsContext as default };
```

Next, we will hop into our App component to create a provider for the context object that we created.

The render function part of the component changes from:

```js
return (
  <div className="App">
    {' '}
    <header className="App-header">
      {' '}
      To Do items <ItemList items={items} removeItem={removeItem} /> <AddItemForm
        addItem={addItem}
      />{' '}
    </header>{' '}
  </div>
);
```

to:

```js
return (
  <ItemsContext.Provider>
    {' '}
    <div className="App">
      {' '}
      <header className="App-header">
        {' '}
        To Do items <ItemList items={items} removeItem={removeItem} /> <AddItemForm
          addItem={addItem}
        />{' '}
      </header>{' '}
    </div>{' '}
  </ItemsContext.Provider>
);
```

## What do we want to share using the context API?

We need to provide the value to the provider now. But what do we want to share among components? That is up to us. In this instance, we want to share the items so that components that need it can access it. And we also want to share the items dispatch function as well, so that components that need to change the components data can do so.

Doing so will remove the need for all the props that we are passing from the App component right now.

Thus, we will be creating an object with those two properties on it, and that will also be the default value for our context object. Thus the initialization of the context object changes to:

```js
const ItemsContext = React.createContext({
  items: [],
  itemsDispatch: () => {},
});
```

and the provider gets the value as:

With this in place, we do not need to pass in any props to the ItemList and AddItem components. It is now upto them to extract what they need from the context API using the useContext hook.

## Refactoring the ItemList component

The item list component no longer needs items as a prop. We can refactor it to get the items using the useContext react hook. We will refactor this first, and then see if we can also remove the removeItem prop later.

Since we will be using the useContext hook, we will need to import that in the ItemList component as well as the items context that we had created since useContext needs that too.

```js
const ItemList = ({ items, removeItem }) => {
  return (
    <div className="items-container">
      {' '}
      <ul>
        {' '}
        {items.map((item) => (
          <li>
            {' '}
            <Item key={item} item={item} removeItem={removeItem} />{' '}
          </li>
        ))}{' '}
      </ul>{' '}
    </div>
  );
};
```

will be changed to:

```js
const ItemList = ({ removeItem }) => {
  const { items } = useContext(ItemsContext);
  return (
    <div className="items-container">
      {' '}
      <ul>
        {' '}
        {items.map((item) => (
          <li>
            {' '}
            <Item key={item} item={item} removeItem={removeItem} />{' '}
          </li>
        ))}{' '}
      </ul>{' '}
    </div>
  );
};
```

## Removing the remove item prop

Notice that the remove item function is eventually calling the dispatch function in our application. And we have already added that function to the context object. So we can remove that prop as well, and refactor our Item component too.

```js
const Item = ({ item, removeItem }) => {
  return (
    <div>
      {' '}
      <span>{item}</span> <button onClick={() => removeItem(item)}>
        X
      </button>{' '}
    </div>
  );
};
```

can now be changed to:

```js
const Item = ({ item }) => {
  const { itemsDispatch } = useContext(ItemsContext);
  return (
    <div>
      {' '}
      <span>{item}</span> <button
        onClick={() =>
          itemsDispatch({ type: 'REMOVE_ITEM', itemToBeDeleted: item })
        }
      >
        {' '}
        X{' '}
      </button>{' '}
    </div>
  );
};
```

You can see all the changes until now [here](https://github.com/saranshkataria/todo-list-react-hooks-demo/commit/8a0eee1191050fa1754f96d0a283d21b282a6bc8) and the code until now [here](https://github.com/saranshkataria/todo-list-react-hooks-demo/tree/8a0eee1191050fa1754f96d0a283d21b282a6bc8).

Now that we have combined our useContext with useReducer, we have created our own mini version of Redux. We are sharing our dispatch function and the application data in our store just like we do with Redux. This does not make Redux obselete and there are use cases for using it. But for smaller applications, useContext and useReducer are sufficient to get the job done without relying on any third party packages.

## Refactoring the AddItemForm component

This will be fairly similar to what we did above for the ItemList component. If you are interested, you can see the changes for this component [here](https://github.com/saranshkataria/todo-list-react-hooks-demo/commit/8a0eee1191050fa1754f96d0a283d21b282a6bc8).

And once we are done, we will see that we are no longer passing any props to our children components at all. We are relying on useContext to get state from the context API and solve our initial problem of passing props down.

The final code for this application can be found on [GitHub](https://github.com/saranshkataria/todo-list-react-hooks-demo). And if you have any questions, feel free to drop a comment below. The react hooks documentation has a great [reference page](https://reactjs.org/docs/hooks-reference.html) that comes in handy while working with hooks. Do let us know if you have any questions.
