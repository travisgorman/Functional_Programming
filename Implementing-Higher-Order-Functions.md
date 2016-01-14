# Implementing Higher-Order Functions
Different implementations of higher order functions. Refering mostly to:
* [Standard ECMA-626 Language Specification](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
* [Underscore](http://underscorejs.org/)
* [Lodash](https://lodash.com/docs)
* [Underbar]
* [Ramda](http://ramdajs.com/0.18.0/docs/)
* [functional](http://functionaljs.com/)
* [lazy](http://danieltao.com/lazy.js/docs/)
* [wu](https://fitzgen.github.io/wu.js/)
* [bacon](https://github.com/baconjs/bacon.js/tree/master/src)
* [sloth](https://rfw.name/sloth.js/)

>>> "**Iteratee:** a composable abstraction for processing sequentially presented chunks of input data in a purely functional fashion".  
>>> When refering to a function that is passed into another function, I will say "callback"


This is purely for educational purposes - just to have a look under the hood at how some libraries are writing implementations of native JavaScript higher-order functions. 


# `forEach`
Iterate over an input `list`, calling a provided function, `fn` for each element in the list.

### Ramda.js

```js
function forEach( fn, list ) {
  var len = list.length;
  var idx = 0;
  while ( idx < len) {
      fn( list[idx] );
      idx += 1;
  }
  return list;
}
```

* **arguments:** takes a function, `fn` and an array, `list` as arguments
* declares variables 
  -`len`, the list of the array passed in
  -`idx`, an index used to traverse the array

* **while loop:** while the index, `idx`, is less than `len` the length of the `list`, 
  - call the function passed as `fn` argument
    + function `fn` takes two arguments, `list` and `idx`
    + call `fn` on the `list` item at position `idx`
    + increment `idx` by 1.
    + when `idx` is larger than `len`, break out of the loop.
* return `list`

___

### Underscore

```js
_.forEach = function( obj, iteratee, context ) {
    iteratee = optimizeCb( iteratee, context );

    var i, length;
    if ( isArrayLike( obj ) ) {
      for (i = 0, length = obj.length; i < length; i++) {
        iteratee(obj[i], i, obj);
      }
    } else {
      var keys = _.keys(obj);
      for (i = 0, length = keys.length; i < length; i++) {
        iteratee(obj[keys[i]], keys[i], obj);
      }
    }
    return obj;
  };
```

___

### Lodash
lodash uses an internal function called `iterator()` that takes three arguments, `value`, `key`, and `collection`. These are the same as the parameters the callback on JavaScripts `Array.prototype.forEach` (MDN calls it `currentValue`, `index`, and `array`). Unlike the native `forEach`, this handles non-array objects.

```js
  _.each = function(collection, iterator) {
    if (Array.isArray( collection ) ) {
      for ( var i = 0; i < collection.length; i++ ){
        iterator( collection[i], i, collection );
      }
    } else {
      for (var k in collection) {
        iterator( collection[k], k, collection );
      }  
    }
  };
```
* Takes two arguments, `collection` (an array or object), and `iterator` (a function)
* If it's an array - use a 'for loop'
  - loop length of the array
  - on each iteration, call `iterator` and pass in the `value`, `key` and `collection`
    + `value` = the current value - `collection[i]`
    + `key` = the index - `i` the thing you increment to traverse an array
    + `collection` = the array or object you passed in
    
* If it's an object - use a 'while loop'
  - declare variable `k` as your index
  - call `iterator` and pass in the `value`, `key` and `collection` the same way as above. 

* There is no return statement because ...? (the original array/object is modified)

___

### Underbar
Exactly the same as Lodash

```js
  _.each = function( collection, iterator ) {
    if (Array.isArray( collection )) {
      for ( var i = 0; i < collection.length; i++ ){
        iterator( collection[i], i, collection );
      }
    } else {
      for ( var k in collection ) {
        iterator( collection[k], k, collection );
      }  
    }
  };
```

___

### fn
Okay... this seems like a bit much for something that already comes with JavaScript, but this guy wants to do it his way, and I can get down with that. I'm not at the point yet where I quite understand the whole 'more functional' point of view, but either way, I'm happy to see something different.  

In `fn.each()` he uses `fn.apply()` and `fn.concat()`, which seem to do the same thing the native Array.prototype methods do. 

```js
fn.apply = function ( handler, args) {
  return handler.apply( null, args);
};

fn.concat = function () {
  var args = fn.toArray(arguments);
  var first = args[ 0 ];

  if (!fn.is('array', first) && !fn.is('string', first)) {
    first = args.length ? [ first ] : [ ];
  }

  return first.concat.apply(first, args.slice(1));
};

//*  */*  */*  */*  */ fn.each

fn.each = function ( handler, collection, params ) {
  for ( var index = 0, collectionLength = collection.length; index < collectionLength; index++) {
    fn.apply( handler, fn.concat( [ collection[ index ], index, collection ], params));
  }
};
```

* `fn.each` takes a `handler`, a `collection` and `params`
  - `handler` is a function (???)
  - `collection` is an array (maybe also an object)
  - `params` is an array of arguments (??)
  
* loop through length of the collection
  - and on each iteration, call this...

```js
fn.apply( handler, fn.concat( [ collection[ index ], index, collection ], params));
```
* Apply takes a handler and an array of arguments - so everything after handler is building an array or arguments
* The handler function is called passing in the right arguments. 

___

### lazy

```js
function forEach(array, fn) {
    var i = -1,
        len = array.length;

    while ( ++i < len ) {
      if ( fn( array[i], i) === false) {
        return false;
      }
    }

    return true;
  }
```
Not quite sure how is assigning values to anything. More on this later... 

___

## `map()`
iterates over a collection, calling a function on each item and projecting the result into a new aray.  
Returns a new array. Takes one argument, a `callback` (function), with the option to include an argument to specify the `this` keyword (object).

>>> Syntax
```js
Array.prototype.map( callback( value, key, collection ) [optional: this] );
```

### Ramda

```js
function _map( fn, functor ){
  var idx = 0;
  var len = functor.length;
  var result = Array( len );
  while ( idx < len ){
    result [idx] = fn( functor [idx] );
    idx += 1;
  }
  return result;
};
```
* Takes two arguments - fn, a `function`, and `functor`, an array
* Create an empty array the same length as `functor` and assign it to variable `results`
  - if i'm mapping an array 4 items long, i create an array called `result` that looks like

```js
result = [ undefined, undefined, undefined, undefined ]
```
  - Instead of creating an empty array and then later pushing items into it, we're assigning values this list of `undefined`'s

* use a while loop, and on each element in the `functor` array, change the value of one of my `undefined`s to the return value of `fn` called on the next item in the `functor` array

```js
result [idx] = fn( functor [idx] );
```

___

