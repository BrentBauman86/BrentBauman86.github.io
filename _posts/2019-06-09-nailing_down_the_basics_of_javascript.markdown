---
layout: post
title:      "Nailing down the basics of JavaScript"
date:       2019-06-09 18:27:01 +0000
permalink:  nailing_down_the_basics_of_javascript
---

So if you're new to JavaScript I hope these concepts that I am about to detail will aid you in your mastery.  I am going to go over how hoisting, scope, and ES6 syntax work together in thier efforts to create working and reliable code.

***So how exactly does hoisting work?***

Hoisting can be a little tricky to understand initially but once you see it in action and play around with examples of it in your console it will soon become clear how it behaves.  Hoisting deals with how function and variable declarations are raised to the top of our scope before any of our code is actually executed.  When JavaScript functions are interpreted by the machine the code that is written inside of those functions goes through a two phase process and clearly identifying and naming these two phases is key to understanging what JavaScript does when it is given code to interpret.  Upon the first phase, JavaScript is ***Compiling*** all the data and placing it in memory.  Upon the second phase, it is ***Executing***  the invoked functions and the assignments of variables.

Lets break down what a variable declaration and variable assignment look like first:

```
var golf;  //your variable declaration:

golf = "Tiger";  //your variable assignment:
```


Now lets look at what a function with this variable inside of it looks like:

```
function sportStar() {
	console.log(golf);
	var golf = “Tiger”;
}

sportStar();  //prints undefined
```

The above code snippet shows us how hoisting our variables can be a little tricky if not done in the proper order.  So lets break this function down and describe what is happening here and why we are getting undefined as our return value.  Once the function is interpreted, it will take the variable declaration:

```
var golf;  //your variable declaration
```

but will not reach down into the assignment of that variable.  In order to remedy this it is good practice to set your variables at the top of your functions and to either console.log or return your values at the bottom in order to make sure your data is being both declared **AND** assigned properly:

```
function sportStar() {
	var golf = “Tiger”;
	console.log(golf);
}

sportStar();  //prints Tiger
```

Now before we wrap up how hoisting works, we should understand how using **var** for our variable declarations instead of **let** and **const** can drastically change what prints out once we hit our console.  For brevity's sake, you could create a function and declare a global variable and a var variable like this: 

```
function letters() {
    a = "a";
    var b = "b";
	a = "second a";
	b = "second b";
		
	console.log(a, b);
}

letters(); //second a second b
```

notice that you would receive the second value of those variables.  That is becuase global variables and declaring a variable with **var** will attach themselves to a variable that is named the same and this would be true if you had 5, 10, or 50 declartions of the same name, they would continuously change as they work their way down through the function and will give you the last assigned value in the console.

with that being said take the **let** and **const** declarations for example:

```
function letters() {
    let a = "a"
	const b = "b"
	let a = "second a"
	const b = "second b"
		
	console.log(a, b)
}

letters() //Identifier 'a' has already been declared
```

This essentially means that you should **ALWAYS** use **let** and **const** when declaring variables inside of your functions.  If you look at that error message, it should tell you that once you assign a value to a variable its set for good and that means your values are safe from manipulation which is what we want.  Things could get really hairy if you had data changing all the time because you thought it would be cool to declare a bunch of variables using **var** and just going with the flow like a hippie and allowing your variables to be whatever they felt like being.  No offense to all you hippies out there, just saying functions are no place for tomfoolery because in order for functions to work properly, they require proper ordering.   

**How do we identify various scopes?**

As we write code in JavaScript we need to be vigilant of identifying where we place our variables and which declarative statements we use.  There are a couple types of scopes that we need to understand in order to make sure our code is accessible where and when we need it.     

Lets discuss how **Global Scope** works.  Global scope refers to variables and functions that are declared outside of functions and you have access to them throughout your program.  You usually will find these at the top of your editor.
Below is an example of what a global variable looks like (a.k.a., when you create a variable without prepending a var, let or const statement):

```
golf = "Tiger" //a global variable

var golf = "Tiger" //not a global variable
```

Also, you can have variables with no declaration inside of functions that will be accessible through global scope as well:

```
function sportStar(){
	golf = "Tiger";  
}

sportStar();

console.log(golf);  //outputs Tiger
```

**Function scope**

Function scope works just as you'd think, within the function:

```
function sportStar(){
    var golf = "Tiger";
}

console.log(golf);  //ReferenceError: golf is not defined
```

so if you try to access that variable like we did above once we added the var statement we won't have access to that value because it is only available to the sportStar() function.

**Block Scope**

Lets think back to when we discussed how var was different to using let or const...remember that var acts kinda like a hippie and would change its values, but let and const would remain the same no matter what.  Now that we're talking about block scope we need to understand that var can be used anywhere within a function regardless of the block in which it is defined:

```
function car(wheels) {
	if (wheels < 4) {
		var repair = "This car is broke";
	} else if (wheels == 4) {
	   var repair = "This car runs great!"
	}
	
	console.log(repair);
}

car(3);  //prints This car is broke
```

as you can see, when you use the var declaration it can be accessed within that block with no issue.  But when you change that function to use **let** and **const**, the way you build you block statement must change as well: 

```
function car(wheels) {
	let repair;
	
	if (wheels < 4) {
		 repair = "This car is broke";
	} else if (wheels == 4) {
	    repair = "This car runs great!"
	}
	
	console.log(repair);
}

car(3);  //prints This car is broke
```

This way allows you to return the value of repair because by placing your let statement inside the function as opposed to inside the **if** block, you can now print off that value when you hit your console.log.  Also, an important note on const and let, as you see I used let in my function which allows for reassignment, but using const means it must remain constant.  Basically that means when we place a variable with let at the top of our scope we can choose to set it to nothing and assign a value to it later in our function but const does not allow that.  You must always set a value to const right off the bat.    

Well I hope that gave you some insight into the wonky workings of JavaScript.  Be vigilant and remember these rules when you begin building your fantastic functions!




