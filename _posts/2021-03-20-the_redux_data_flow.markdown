---
layout: post
title:      "The Redux Data Flow"
date:       2021-03-20 19:35:32 +0000
permalink:  the_redux_data_flow
---

If you are learning how to work with Redux, and you find yourself desperately confused about what does what and where things go: you are not alone. However, I can assure you that all is not lost! Allow me to see if I can help. 

I am going to break down the Redux data flow and what functions like ```mapDispatchToProps()``` and ```mapStateToProps()``` actually do. 

To start off, you should know that Redux is a system that will help us manage our state.

The ```store``` is where we save our state.

```getState()``` is the method that gives us our state.

```action``` and ```reducer``` are the methods that update our state. ```action``` is what gives us the data that we're going to use to update our state. ```reducer``` takes the data from the ```action``` and updates state. 

Let's use an easy example and say that you are working on a shopping list app. Your user, when they're ready to go shopping, is going to want to pull up their grocery list so they know what to buy. Your app is going to need to pull that grocery list from the database. Luckily, we can use an ```action creator``` that does just that.

We can create an action creator called ```fetchShoppingList``` that will send a fetch `'get'` request to our database. An action creator is a function that returns a plain JS object called an action. So, after your action creator sends that fetch call to get what you need from the database, it's going to return that data in the form of an object (the action). We call this "dispatching an action".

So, now you have the data you want in the form of an action. What next?

In comes the reducer. The reducer will update our state by taking in two arguments: the current state and the action that was passed from the action creator. The reducer will return a new state result. The reducer will choose how to update the state based on the type of action that we sent. For example, our action type might be ```FETCH_SHOPPING_LIST```. Based on this type, the reducer will know to update the ```shoppingLists``` part of our state with the shopping list that we fetched from our database.

Once our reducer returns the new state result, we know that we have our grocery shopping list right where we want it. But... we still need to be able to access it and do something with it! We can't just say, "display grocery list!" Not yet, anyway. Back in our component, ```mapStateToProps()``` is the function that will allow us to actually do something with our current state.

Here is the basic structure of ```mapStateToProps()```, using our shopping list example:

```const mapStateToProps = (state) => {
    return {
        shoppingList: state.shoppingLists
    }
};```

Our current state is such that ```shoppingLists``` involves only one shopping list: the one that we fetched from the database. This function is retrieving that shopping list in the form of ```state.shoppingLists``` and setting it to a prop: ```shoppingList```. Now, we can access that prop and do things with it, including passing it down to a child component. We would probably use that child component to render the shopping list on the page. 

At this point, we have an action creator that will send our data to the reducer, which updates our state. We have used our state to create a prop that we can use in our components. Something is missing, though. How are we going to actually call that action creator? We need something to start this entire process. That something is a function:  ```mapDispatchToProps()```.

We can use ```mapDispatchToProps()``` to create a function that, when called, will start the process of updating state by dispatching an action. Here's what ```mapDispatchToProps()``` might look like, using our shopping list example.

```const mapDispatchToProps = (dispatch) => {
    return {
        dispatchFetchShoppingList: (shoppingListId) => dispatch(fetchShoppingList(shoppingListId))
    }
};```

Here, ```mapDispatchToProps()``` is returning a function called ```dispatchFetchShoppingList()```. What does this function do? Take a look. It takes in a ```shoppingListId``` and calls the appropriate action creator (```fetchShoppingList```). This means that any time we call ```dispatchFetchShoppingList(shoppingListId)``` in our component, it will call the action creator to start that process of updating state. Cool!

Where would we put ```dispatchFetchShoppingList(shoppingListId)```? In this case, probably in a ```componentDidMount()``` function. That way, as soon as our component mounts it will update the state to reflect that we want access to just one specific shopping list. You love to see it. 

But wait! We almost forgot one VERY important thing! ```connect()```! If we don't connect our component, none of this is going to work! 

Okay. ```connect()``` is a function that connects a React component to a Redux store. We could probably write a whole blog post on ```connect()``` alone, but I'll save that for another time. You can read more about ```connect()``` here: https://react-redux.js.org/api/connect

```connect()``` takes ```mapDispatchToProps()``` and ```mapStateToProps()``` as arguments. Here's what it might look like for us:

```export default connect(mapStateToProps, mapDispatchToProps)(ShoppingListShowContainer);```

Don't forget to import ```connect()``` at the top of your component!

```import { connect } from "react-redux";```

That was a brief explanation of the Redux data flow and what it might actually look like for you as you're building a simple web app. Of course, if you're needing more information about any of these pieces, check out the docs: https://react-redux.js.org/

Good luck and have fun!











