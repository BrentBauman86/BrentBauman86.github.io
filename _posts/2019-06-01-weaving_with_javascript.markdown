---
layout: post
title:      "Weaving with JavaScript"
date:       2019-06-01 17:22:31 +0000
permalink:  weaving_with_javascript
---


I found out real quick once beginning this project that javascript was going to be more versatile and nuanced then I initally thought.  I had to scratch my head for awhile trying to conceptualize how this was all going to work together but once I got going it all started to fall into place.  

> Top Tip:  When you begin, understand that your rails project is essentially just along for the ride, you will be interacting very little with it so try and stay focused on how you're going to paint the DOM with your new js code.  

With that being said, allow me to walk you through my process for checking off some of the requirments needed for this rails + js craft project.

So to start with, as I mentioned above, you've got to understand that this is a test of your frontend prowess and that the role rails is playing is simply as your backend database which you'll need in order to grab your yummy data and present it to the user.

Once you mentally settle in with what needs to be done, you can begin weaving together your rails backend and your js frontend features.  First order of business is to set up your serializers, serializers act as the glue that binds your rails attributes for a specific object and allows you to access those attributes via js.  I will detail the steps needed that will connect your rails backend to your js frontend.  These next steps are what will allow you to satisfy the translation of JSON responses to your js model objects.

> Top Tip:  The next part of this weaving process will show you what you need in order to make this all work in harmony.  Feel free to take the next steps in whatever order you see fit, this is just the way I chose to put them together and it seemed to work pretty nicely.   

First, create a new folder in ruby: 

```
toy_serializer.rb
```

Next, inside that folder write your serializer, mine is obviously a toy but inject whatever object you're going to use like this ToySerializer class which takes in all of the toy attributes and associations:

```
class ToySerializer < ActiveModel::Serializer
  attributes :id, :name, :quantity, :rating, :user_id, :category_id 

  belongs_to :user
  belongs_to :category
end
```

Next, you're going to need to add a new gem into your gemfile: 

```
gem 'active_model_serializers'
``` 

This gem works together with your ToySerializer class to allow js to understand the data that is being passed to it via rails.  

Lastly, you will need to add some code to your controllers to make the json data readable on the frontend.  Check out the following index method which shows you how to render json instead of using your previous render or re-directs:

> Top Tip: Remeber, you are just needing the data from your backend and all of the routing will be handled in your js functions with either fetch, ajax, or jquery.

```
  def index
       if @category 
            @toys = @category.toys
        else 
           @toys = Toy.all 
        end

        respond_to do |f|
            f.html {render :index}
            f.json {render json: @toys}
        end
    end 
```

So once you've created all of that, the next steps will detail how to grab data from your backend and display it to the user using js.

> Top Tip: This next part is what the project is alllll about.  The previous stuff was just setting up for the data to be deliverable. 

Now that we have our connections setup, lets actually use those connections to start passing data to our new frontend.  

I 'am just going to go over the steps of how I went about presenting my index page, this will help clarify what steps need to be taken to use your backend data and show it in js form which is essentially what this project is all about.  Going over this requirement will give you a solid foundation of how this whole rails into js weaving technique works.   

Ok, so lets dive in.  I wanted to make this as clean as possible so I created one file in my js folder and called it index.js which essentially houses all of my js code.  I really only reached into my rails views for one little bit of information which I needed to dynamically display my show page and my form submit page.  So like I said earlier, I'd get into why we didn't need our redirects or renders in rails because this is about api calls to our backend.  For brevity's sake, grabbing the data from our backend is as simple as using a fetch, ajax, or jquery call and these give us the freedom to do as we please with the data we have, pretty sweet stuff if you ask me.  I chose to use jquery mostly but I did play around with some ajax calls as well but found jquery to work the best.

So let me take you through how I got my index page working with js.  

First thing to do is begin with building a function that takes a get request to your index page.  Along with this step you will need to make use of a click event in order to call upon this function when a user clicks on it.  That click event should look something like this: 

```
function toyListClick() {
    $('button#toy-data').on("click", function(e) {
        e.preventDefault()
        getToyList();
    })
}
```

An easy way to make sure you're calling the right route is to simply bring up your index page and take a peek at the url you're on and then copy that into your jquery, ajax, or fetch request.  Once you have that set up you need to make sure you're able to get that data back from rails which is where callback functions or promises come into play. 

> Top Top: Essentially when you make an api request you need to have a way to call upon the data when the user requests it which is why callback functions and promises are so important.

Here is what step one should look like: 

```
function getToyList() {
    $.getJSON(`/toys`, function(data) {
        let toyList = "";
      
        data.forEach(function(toys){
            let toy = new Toy(toys);
            let toyHTML = toy.showHTML();
            toyList += toyHTML 
        });
    });
}
```

Here we have our jquery get request to the toys page which has a callback function with an argument which represents our json data which is exactly what we need in the following step where we iterate over that data with the forEach method.  This next part is where it gets groovy, as you can see I am now taking that data and crafting it to be in neat ole javascript form by runnung it through a series of alterations.  To start, we need to have made a constructor for this object which looks like this:

```
class Toy {
    constructor(obj) {
        this.id = obj.id;
        this.name = obj.name; 
        this.quantity = obj.quantity;
        this.rating = obj.rating; 
        this.user_id = obj.user_id;
        this.category_id = obj.category_id
    }
```

so that we can now have an instance of those new toys and take all of their attributes and represent them with js.  Next we need take that ugly looking json data and organize it so that when its brought to the DOM its represented neatly with all the headers and columns which it had back in rails.  In order to do that we build a prototype called showHTML which looks like this: 

```
Toy.prototype.showHTML = function() {
    return (`
    <style>
        .left  
        .center 
        .right  
    </style>

    <ul>
    <table class="table">
         <tr>
             <td class="left"><li>${this.name}</li></td>             
             <td class="center">${this.quantity}</td>
             <td class="right">${this.rating}</td>
        </tr>
    </table>
    </ul>
    `)
}
```

so now all of those toys get passed through this tableizer so they appear neatly in the DOM.  And the last part of the forEach method has the toys put back into an open slot that I made called toyList.

The last part to this function is now presenting this newly formed data to the page and the last part of the getToyList function looks like this:

```
function getToyList() {
    $.getJSON(`/toys`, function(data) {
        console.log(data)
        let toyList = "";
      
        data.forEach(function(toys){
            let toy = new Toy(toys);
            let toyHTML = toy.showHTML();
            toyList += toyHTML 
        });

        let table = document.getElementById('toy-table');
        table.innerHTML = toyTable();
        let toyData = document.getElementById('toys');
        toyData.innerHTML = toyList;
        $('#js-app').html('')
    });
}
```

So down here we need to use the document.getElementById method so that we can park our neat tabled data into an open spot we created on the DOM.  So here I've made an open spot in my views/layouts/application.html.erb file which holds my parking spot which I've named toy-table:

```
<div id='toy-table'>[toy header...]</div>
```

Now that I have this open spot I grab it and place it in a variable called table and then append innerHTML to that which allows me to set the HTML contained in that element which we then set equal to the table we created.  To clarify, this toyTable function is meant to represent the header of my table which includes the name, quantity, and rating of my toy, the next function is where we actually place the data of all the toys into the table itself.  With that being said you can see how I finished this function off and included the data to the table.  Oh, and one last fun addition, the 

```
 $('#js-app').html("")
```

line of code is wrapped around my yield block in my views/layouts/application.html.erb file like this: 

```
 <div id="js-app">
    <%= yield %>
    <div>
```

this little wonder completely clears out my DOM when I go to represent my newly created js toy table, makes for an easy way to show your new stuff with little hassle.  

I hope that gave you a little insight into how this project should be approached, I really enjoyed putting this together and getting the chance to further understand the intricacies of how languages can work togther, it was a neat experience.  

> Final Top Tip:  When you create your functions I found it invaluable to console log each step to ensure the data I was receiving was what I needed.  I also was keeping a close eye on my terminal as I was testing out my get and post requests, it acted as a guide when things weren't routing properly. 




 
