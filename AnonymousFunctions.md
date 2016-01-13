# Anonymous Functions

An anonymous function is a function definition not bound to an identifier. Also called 'function literal'.

They are functions that are dynamically declared at runtime.

They are often used:
* As the arguments being passed to higher-order functions
* Used for constructing the result of a higher-order function that needs to return a function

### What are they used for?
Anonymous functions can be used for containing functionality for short-term use that doesn't need to be named.
Notable examples are **closures** and **currying**. 

Closures are functions evaluated in an enviornment containing bound variables. 

Some programmers use anonymous functions to encapsulate specific, non-reusable code without litering the code with a lot of little one-line normal functions. 

#### WHEN do you use anonymous functions?
Anonymous functions are typically used as callbacks.  

When a function is only used once or a limited number of times, it might be syntactically lighter. 
I have been using them mostly for inside functions like `map()`, `forEach()`, and `reduce()`.  

#### When should you NOT use anonymous function?


#### HOW do you use anonymous functions?
Anonymous functions are declared using the **function operator** instead of the function declaration.

Anonymous functions are a form of **nested function**, in allowing access to variables in the scope of the containing function (non-local variables). This means anonymous functions need to be implemented using closures.

Unlike named nested functions, they cannot be recursive without the assistance of a 'fixpoint operator' binding them to a name. 

___
```js
function hello(){
  alert( 'Hello worls' );
}
```
This is a normal *function decalration*. When the `function` keyword is the first thing, that is how you know it is a function declaration. 

Invocation operator is a pair of parentheses that follow any expression that produces a function value

## Javascript Anonymous Functions
Declarations can never be anonymous. Expressions can. Function expressions are created with the function operator. All functions are objects. 

___
##### 1. Function Declaration
This function is created using a function declaration. 
Whenever you see the `function` keyword followed by the name, it is being created by a **function declaration**.  
Functions made with a function declaration are **hoisted** and therefore are created before the rest of the function is run.
The function declaration syntax also automatically adds the new function object to the current scope.

```js
function flyToTheMoon(){
  alert( "Zoom! Zoom! Zoom!" );
}

flyToTheMoon();
```
All functions created with the function declaration must be given a name. They can't be anonymous. If they didn't have a name, you couldn't add it to the scope, and it would be impossible to call. 

___
##### 2. Function Operator
Here is the same example created using the function operator. as an **anonymous function:**

```js
var flyToTheMoon = function(){
  alert("Zoom! Zoom! Zoom!");
}

flyToTheMoon();
```
Function operators **aren’t affected by hoisting** so they are evaluated **where they occur** as the code is run.


```js
var destroyDeathStar;

if(pilot === "Luke Skywalker" ){
  destroyDeathStar = function() {
    alert( "May the force be with you" );
  } 
}
else {
  destroyDeathStar = function(){
    alert( "Gold Five to Red leader, lost tiree, lost Dutch" );
  }
}

destroyDeathStar();
```
The usual thing is to assign the return value from the function operator to a variable that you can use to invoke it:


```js
function destroyDeathStar(){
  alert("Stay on target, stay on target");
}

destroyDeathStar();

var destroyDeathStar2 = function(){
  alert("Stay on target, stay on target!");
}

destroyDeathStar2();
```

If you don't do anything with the return value from the function operator, ther is no way to invoke the function. 


```js
    function(){
      alert( "Stay on target, stay on target!" );
    }
// wut? there's no way to invoke the function. 
```

Since functions made with the function operator aren't automatically added to the scope, they don't have to have a a name. They can be anonymous. Functions created with the function operator are usually created as anonymous functions since there isn't much benefit giving the function a name since it's not automatically added to the scope. 


```js
    var destroyDeathStar = function (){
      alert( "Stay on target, stay on target!" );
    }

// `destroyDeathStar` is a variable containing a reference to the function, not the function's name
```
In fact, it is an anonymous function. It has no name. Don't believe me? Look

```js
    var destroyDeathStar = function /* see. no name here */ (){
      alert( "Stay on target, stay on target!" );
    }
```
### The function operator is useful because it's an expression 
This is why I've been calling it a "Function Expression"
The **function operator** is a lot more flexible than a function declaration because it can be used wherever it is valid to use an expression. You can use the function operator to declare a function when you are creating an object:


```js
    var jabbaTheHut = {
      laugh: function (){
        alert( "ho ho ho ho" ); }
    }
    jabbaTheHut.laugh();
```
When you're creating a list:

```js
    var toDoToday = [
        function(){ alert( "Aren't you a little short for a storm trooper?" ) },
        function(){ alert( "Boring conversation anyway" ) },
    ];
    for( var x = 0; x < toDoToday.length; x++){
      toDoToday[x]();
    }
```
To declare a function as a parameter when calling another function:

```js
    $(document).ready( function(){
      alert( "page has loaded." );
      });
```
You can also use the function operator inside of an if condition to actually control whether a function is created or not using programming logic

```js
    var chooseSite;
    id( skywalker === "Luke Skywalker") {
      chooseSite = function(){
        return "jedi";
      }
    }
    else {
      chooseSide = function(){
        return "sith";
      }
    }
    alert( chooseSide() );
```

Or inside of any type of loop:

```js
  for( var x =0; x<= 900; x++ ){
    var myFunction = function(){
      alert( "When " + x + " years old you reach, look as good you will not." );
    }
    myFunction();
  }
```

### When should you use the Function Operator?

* inside a loop or an if statement — always use function operator
  - The function declaration will not have the effect that you intended because it will be hoisted to the top of the code.
  
* If you are only using it once straight away

* It is ideal for single line jQuery event handlers that toggle some CSS class. 

* If you're working in the global scope and want to avoid creating a lot of variables that might conflict with other code. The function operator can be used with patterns such as **namespacing** to keep your code’s footprint as light as possible.

### When should you stick to Function Declarations?

* If you'll be using the function a number of times. 
* The declaration is more concise and looks more like how you'd create a function in most other languages. If you're using the function operator everywhere else and want to make sure no one makes the mistake of putting a function declaration inside a conditional statement or loop, it might be worth mandating the function operator in your standards. 

## Javascript Anonymous Functions
Declarations can never be anonymous. Expressions can. Function expressions are created with the function operator. All functions are objects. 

___
##### 1. Function Declaration
This function is created using a function declaration. 
Whenever you see the `function` keyword followed by the name, it is being created by a **function declaration**.  
Functions made with a function declaration are **hoisted** and therefore are created before the rest of the function is run.
The function declaration syntax also automatically adds the new function object to the current scope.

```js
function flyToTheMoon(){
  alert( "Zoom! Zoom! Zoom!" );
}

flyToTheMoon();
```
All functions created with the function declaration must be given a name. They can't be anonymous. If they didn't have a name, you couldn't add it to the scope, and it would be impossible to call. 

___
##### 2. Function Operator
Here is the same example created using the function operator. as an **anonymous function:**

```js
var flyToTheMoon = function(){
  alert("Zoom! Zoom! Zoom!");
}

flyToTheMoon();
```
Function operators **aren’t affected by hoisting** so they are evaluated **where they occur** as the code is run.


```js
var destroyDeathStar;

if(pilot === "Luke Skywalker" ){
  destroyDeathStar = function() {
    alert( "May the force be with you" );
  } 
}
else {
  destroyDeathStar = function(){
    alert( "Gold Five to Red leader, lost tiree, lost Dutch" );
  }
}

destroyDeathStar();
```
The usual thing is to assign the return value from the function operator to a variable that you can use to invoke it:


```js
function destroyDeathStar(){
  alert("Stay on target, stay on target");
}

destroyDeathStar();

var destroyDeathStar2 = function(){
  alert("Stay on target, stay on target!");
}

destroyDeathStar2();
```

If you don't do anything with the return value from the function operator, ther is no way to invoke the function. 


```js
    function(){
      alert( "Stay on target, stay on target!" );
    }
// wut? there's no way to invoke the function. 
```

Since functions made with the function operator aren't automatically added to the scope, they don't have to have a a name. They can be anonymous. Functions created with the function operator are usually created as anonymous functions since there isn't much benefit giving the function a name since it's not automatically added to the scope. 


```js
    var destroyDeathStar = function (){
      alert( "Stay on target, stay on target!" );
    }

// `destroyDeathStar` is a variable containing a reference to the function, not the function's name
```
In fact, it is an anonymous function. It has no name. Don't believe me? Look

```js
    var destroyDeathStar = function /* see. no name here */ (){
      alert( "Stay on target, stay on target!" );
    }
```
### The function operator is useful because it's an expression 
This is why I've been calling it a "Function Expression"
The **function operator** is a lot more flexible than a function declaration because it can be used wherever it is valid to use an expression. You can use the function operator to declare a function when you are creating an object:


```js
    var jabbaTheHut = {
      laugh: function (){
        alert( "ho ho ho ho" ); }
    }
    jabbaTheHut.laugh();
```
When you're creating a list:

```js
    var toDoToday = [
        function(){ alert( "Aren't you a little short for a storm trooper?" ) },
        function(){ alert( "Boring conversation anyway" ) },
    ];
    for( var x = 0; x < toDoToday.length; x++){
      toDoToday[x]();
    }
```
To declare a function as a parameter when calling another function:

```js
    $(document).ready( function(){
      alert( "page has loaded." );
      });
```
You can also use the function operator inside of an if condition to actually control whether a function is created or not using programming logic

```js
    var chooseSite;
    id( skywalker === "Luke Skywalker") {
      chooseSite = function(){
        return "jedi";
      }
    }
    else {
      chooseSide = function(){
        return "sith";
      }
    }
    alert( chooseSide() );
```

Or inside of any type of loop:

```js
  for( var x =0; x<= 900; x++ ){
    var myFunction = function(){
      alert( "When " + x + " years old you reach, look as good you will not." );
    }
    myFunction();
  }
```

### When should you use the Function Operator?

* inside a loop or an if statement — always use function operator
  - The function declaration will not have the effect that you intended because it will be hoisted to the top of the code.
  
* If you are only using it once straight away

* It is ideal for single line jQuery event handlers that toggle some CSS class. 

* If you're working in the global scope and want to avoid creating a lot of variables that might conflict with other code. The function operator can be used with patterns such as **namespacing** to keep your code’s footprint as light as possible.

### When should you stick to Function Declarations?

* If you'll be using the function a number of times. 
* The declaration is more concise and looks more like how you'd create a function in most other languages. If you're using the function operator everywhere else and want to make sure no one makes the mistake of putting a function declaration inside a conditional statement or loop, it might be worth mandating the function operator in your standards. 








