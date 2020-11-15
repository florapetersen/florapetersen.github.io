---
layout: post
title:      "users_as_many_things"
date:       2020-11-15 00:28:43 -0500
permalink:  users_as_many_things
---


For my Rails project, I created a web app called DanceApp. DanceApp is meant to be a place for dancers to find and register for dance classes in their area. Users can interact with the app as students, teachers, studio owners, or any combination of the three. That means that this domain has the potential to be super complex. Because I'm a beginner and only had two weeks to do this project, my version of it is pretty simple, but it felt pretty complicated to me!

The app has only one user class, called User. However, we refer to the user by different names depending on what role they're taking on at any given time. 

```
class User < ApplicationRecord

   has_many :class_registrations
   has_many :dance_classes_as_student, through: :class_registrations, source: :dance_class
   has_many :dance_classes_as_teacher, class_name: "DanceClass"
   has_many :studios_as_studio_owner, class_name: "Studio"
```
	
Here is our User class and its associations. You can see that users have two associations to dance classes, instead of just one. A user can be associated to a dance class `as_student` or `as_studio_owner`. Prior to this project, I didn't even know you could do that! If you want to have different types of users, this is one way to do that. 

Because `:dance_classes_as_student` and `:dance_classes_as_studio_owner` are not actually names of any of our classes, your machine will be very confused by this unless you tell it which classes you're actually referring to! When you have a `has_many, through: association`, you can identify the class you're referring to with `source: :actual_name_of_the_class`. With a regular `has_many` association, you will use `class_name: "ActualNameOfTheClass"`. Trial and error (and some help from my cohort lead) showed me that when using `class_name:` you must put the name of the class in quotation marks (`"Class"`), as opposed to as a symbol (`:class`). 
	
```
class DanceClass < ApplicationRecord
	
  has_many :class_registrations 
	belongs_to :style
  belongs_to :studio
  belongs_to :day
  has_many :dancers, through: :class_registrations, source: :user 
  belongs_to :teacher, class_name: "User", foreign_key: :user_id
```

Here is our DanceClass class. You can see that we reference the User class twice, but it's a bit hidden! Dance classes have two associations to the User class: they have dancers and they have teachers. There's that `source: :user` again, because of the `has_many, through:` relationship with dancers. For the `belongs_to` association with teachers, we have a `class_name: "User"` and, of course, a foreign key! Don't try to use `foreign_key: :teacher_id`; that won't work! You must reference the actual name of the class in the foreign key. 
	
```
class Studio < ApplicationRecord
	
	has_many :dance_classes
  belongs_to :studio_owner, class_name: "User", foreign_key: :user_id
```

A studio `belongs_to` a studio owner, so you will see that this code is very similar to the association between dance classes and teachers.

Building the classes was complicated enough! Now, let's take a look at how we see this teacher-studio-owner-dancer business throughout the rest of our code! 

```
class DanceClassesController < ApplicationController
   def create 
           @dance_class = current_user.dance_classes_as_teacher.build(dance_class_params)
           if @dance_class.save 
               redirect_to dance_class_path(@dance_class)
           else 
               render :new 
           end 
    end
```
		
Here's a snippet from the `DanceClassesController`. Typically, if we wanted to build a new dance class that would associate with a user, we would simply write `@dance_class = current_user.dance_classes.build(dance_class_params)`. However, because users can associate to dance classes in multiple ways, we want to be very specific. It is not always necessarily going to be the teacher who's creating a dance class, and we want to be able to allow different users to interact with dance classes in different ways. When we call `dance_classes_as_teacher` on the user, our machine knows that this user should interact with the dance class as a teacher. 

In another controller action, we call the teacher method on a dance class:

```
def update 
        if current_user == @dance_class.teacher
            if @dance_class.update(dance_class_params)
                redirect_to dance_class_path(@dance_class)
            else
                render :edit   
            end
        else
            flash[:error] = "You can't edit someone else's class!"
            redirect_to dance_class_path(@dance_class)
        end 
    end
```

Pretty cool! Imagine all the fun things we could do with this functionality? We could have a user be able to interact with dance classes as a student, a studio owner, or an administrator at a studio, and allow them to interact in different ways. Maybe a student can see who else is signed up for the class so far, but only teachers and admins can edit the time and date of the class. Sometimes, an admin signs up for a class! Then, they interact with the class not as an admin, but as a student. We'll see what I can come up with when I return to this app and improve it!





