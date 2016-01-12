# Closure
Any function that keeps reference to variables from its parent's scope, even after the parent has returned.  

A function can refer to, or have access to
* any variables and parameters in its own scope
* any variables an parameters of outer functions
* any variables in the global scope

___

## Point #1.
You can refer to variables outside of the current function

```js
function setLocation( city ) {
  var country = "France";

  function printLocation(){
    console.log("You are in " + city + ", " + country );
  }

  printLocation();
}

setLocation( "Paris");
```
`printLocation()` refers to the `country` variable and the `city` parameter of the enclosing `setLocation()` function. The result is that, when `setLocation()` is called, `printLocation()` uses the variables and parameters in `setLocation()`.  

___

## Point # 2.
Inner functions can refer to variables defined in outer functions even after the latter have returned.  

```js
function setLocation( city ) {
  var country = "France";

  function printLocation(){
    console.log("You are in " + city + ", " + country );
  }

  return printLocation;
}

var currentLocation = setLocation("Paris");

currentLocation(); 
```
This is almost the same as the first example, except this time, `printLocation()` is returned inside the outer function instead of being immediately called. So, the value of `currentLocation` is the inner `printLocation()` function.




___

## Point # 3.
Inner functions store their outer function's variables by reference, not by value. 

```js
function cityLocation(){
  var city = "Paris";

  return {
    get: function() { console.log( city); },
    set: function( newCity ) { city = newCity; }
  };

}

var myLocation = cityLocation();  
myLocation.get();                 // Paris
myLocation.set('Sydney');
myLocation.get();                 // Sydney
```

Here `cityLocation()` returns an object containing two closures - `get()` and `set()` - that both refer to the outer variable, `city`.  

`get()` obtains the current value of city, while `set()` updates it.   

One interesting feature of closures is that the variables are automatically hidden. Closures store data in their enclosed variables without providing direct access to them. 
### The only way to alter those variables is by providing access to them indirectly.  

You cannot access `get()` or `set()` directly. 

```js
cityLocation.get();
```

*returns* ERROR
>>>Uncaught TypeError: cityLocation.get is not a function

All `cityLocation()` can do is store the variable `city` and return an object. 

```js
cityLocation();
```
*returns* -->

```js
    { // object
      get: function()
      set: function( newCity )
    }
```

`cityLocation()` is a function, not an object, so trying to get at those methods won't work. You need to assign the returned object to a variable in order to access its methods `get()` and `set()`.  

```js
var myLocation = cityLocation();  
```
As we saw, calling `cityLocation()` returns an object. By assigning the function call to a variable, `myLocation` now holds the *object itself*, giving us the ability to access its methods.

```js
myLocation.get();
```
>>>"Paris"

```js
myLocation.set("Austin"); 
```
>>> (changes the outer variable, `city`)

```js
myLocation.get();
```
>>>'Austin'
