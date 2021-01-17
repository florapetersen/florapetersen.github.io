---
layout: post
title:      "Fun and Frustration with JS"
date:       2021-01-17 20:05:51 +0000
permalink:  fun_and_frustration_with_js
---


I was excited to learn Javascript, but it ended up being much more complicated and confusing to me than I thought it would be. In this module, we learned to use Javascript as our front-end language, incorporate HTML and manipulate the DOM to actually get something on the browser screen, and use Ruby/Rails to handle our back-end, complete with a Fast JSON API. It sounds like a lot, and it is! I was overwhelmed at first but, as usual, applying the material in a practical way through this project ended up solidfying the concepts for me. 

I decided to create an app called Favorite Things. This app allows users to upload products that they love and want to recommend to their followers. People are more likely to buy things if they have been recommended by someone whose opinion they trust (a friend, fashion influencer, etc.) This app is a way for people to share their favorite things all in one place, with pictures, a description of the products, and links to buy. Users can associate their products with a category, so that other users can filter their products by category depending on what they're looking for. I'm still working on this part. In fact, I will go ahead and talk through it with you, right now. Maybe it will help me work through this error I'm getting!

Filtering by category seems like an easy thing to do. If I were just working with Ruby/Rails, it would be. However, I have a lot more to think about now! 

To start, I already had my categories seeded to the database like this:

```loungewear = Category.find_or_create_by(name: "Loungewear")```

The front-end of my program has access to the seeds by sending a fetch request to the index action of the CategoriesController ("/categories"). Here's the fetch request in ```Category.all()```...

```static all() {
        console.log(this);
        return fetch("http://localhost:3000/categories", {
            headers: {
                "Accept": "application/json",
                "Content-Type": "application/json"
            }
        })```
				
...and here's how that fetch request gets handled in the CategoriesController:

```class CategoriesController < ApplicationController
  before_action :set_category, only: [:show, :update, :destroy]

  def index
    @categories = Category.all
    render json: CategorySerializer.new(@categories).serializable_hash[:data].map{ |hash| hash[:attributes] }
  end```
	
Using a CategorySerializer, the categories are sent back to the browser as serialized objects. Now our front-end Javascript can grab them!
	
I made sure that all of my categories would load when a user navigates to the page:
	
	```document.addEventListener('DOMContentLoaded', function(e) {
    Category.all();
})```

Now, we have to save each category to our array of categories, and render them so that they will actually show up on the browser screen!

```.then(categoryArray => {
      this.collection = categoryArray.map(attrs => new Category(attrs))
      let renderedCategories = this.collection.map(category => category.render())
      this.container().append(...renderedCategories);
      this.collection.map(group => group.categoryOptions());
      return this.collection
 })```
 
This takes each category and adds them to our collection of categories. Then, it creates a new array that contains the rendered categories (this is, categories that have had render() called on them). The new array of rendered categories is added to the Category container. 

I won't go through render() step by step. Rendering is how we take objects and display them dynamically with HTML. 

After rendering the categories and appending them to the Category container, all of the categories are showing up on the page in a nice, orderly list! However, that's just part of what we want. What we really want is for a user to be able to click on one of those categories to see only the products associated with that category.

Okay, first step! The categories have to be clickable. That means that they need to be rendered with inner HTML. Here's where I render the actual name of the category:

```this.categoryLink ||= document.createElement('a');
this.categoryLink.classList.add(..."no-underline hover:underline py-1 col-span-10 sm:col-span-4 selectCategory".split(" "))
this.categoryLink.dataset.categoryId = this.id;
this.categoryLink.innerHTML = `<i class="filterCategory" data-product-id="${this.id}">${this.name}</i>`;```

Let's take a look at that last line. I gave it a class of "filterCategory"; assigned the data-product-id to the object's own id, and made the actual text the name of the category. Now that the category element is associated with some actual data about the category, we need a click event so that something can happen when the user clicks on the category!

```
document.addEventListener('click', function(e) {
    let target = e.target;```
		
Here's our event listener and here's the part that pertains to our category click:

```    } else if(target.matches(".filterCategory")) {
        e.preventDefault();
        let selected_category = Category.findById(target.dataset.categoryId);
        console.log(selected_category)
    }```
		
Why is that console.log() in there, you ask? Well, it's because I was trying to test if that ```selected_category``` variable was actually being turned into something! And surprise... it's not! Undefined! This has me ripping my hair out because I used the exact same code earlier in the event listener to be able to click on a product name. 

At first, I thought, "maybe the target isn't working." I tested out the target like so:

``` } else if (target.matches(".filterCategory")) {
      e.preventDefault();
			console.log("hello")
			}```

Click on a category and... yep! "Hello" was logged to the console. The target is working just fine. 

So the problem is that ```selected_category``` is not being defined by that line of code.

Is there something wrong with ```Category.findById()``` ? Check the code. Looks good!

Should I try just ```target.dataset```? ```target.dataset.category```? ```target.dataset.category.id```? Trial and error says, "nope". 

Time to enlist some friends. After Zoom sessions with three classmates... none of us could figure it out. 

My project will have to be submitted without this particular feature. Maybe I will come and update this blog post when I figure out what the problem was! I know you're just dying to know.













