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


## `forEach`
Iterate over an input `list`, calling a provided function, `fn` for each element in the list.
>>>Syntax 
```js
Array.forEach( callback( value, key, collection ) [this] )
```
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
Not quite sure how this is assigning values to anything. I'll come back to this...

___
___
___

## `map()`
* Iterates over a collection, calling a function on each item and projecting the result into a new aray.  
* Takes one argument, a `callback` (function), with the option to include an argument to specify the `this` keyword (object).  
* Returns a new array. 

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

## learnRx
This is the way Jafar teaches it in his course on Frontend Masters (and the learnRx online exercises)

```js
Array.prototype.map = function( projectionFunction ) {
  var results = [];
  this.forEach( function( itemInArray ) {
    results.push( projectionFunction( itemInArray ));
  });
  return results;
};
```
* Declare `results`, an empty variable.
* call `forEach()` on `this`
  - `itemInArray` as the `value` argument - The current element being processed in the array.
  - On each item in the array, 
  - push into `results` array
    + the return value created by callng your `projectionFunction` passing in the current value 
* Return `results`

___

## Underscore


```js
_.map = _.collect = function(obj, iteratee, context) {
  iteratee = cb(iteratee, context);
  var keys = !isArrayLike(obj) && _.keys(obj),
      length = (keys || obj).length,
      results = Array(length);
  for (var index = 0; index < length; index++) {
    var currentKey = keys ? keys[index] : index;
    results[index] = iteratee(obj[currentKey], currentKey, obj);
  }
  return results;
};
```

___
## Lodash

```js
  _.map = function(collection, iterator) {
    if (Array.isArray(collection)) {
      var result = [];
      for (var i = 0; i < collection.length; i++){
        result.push(iterator(collection[i], i, collection));
      }
      return result;
    } else {
      var result = {};
      for (var k in collection) {
        result[k] = iterator(collection[k], k, collection);
      }
      return result;  
    }
```


___

## fn

```js
fn.map = function (handler, collection, params) {
  return fn.reduce(function (accumulator, value, index) {
    accumulator.push( fn.apply(handler, fn.concat([ value, index, collection ], params)) );
    return accumulator;
  }, [ ], collection);
};
```


___
___
___

# `filter()`
Returns an array of only the values that pass a test.  
Takes one argument, a `callback` (function) that expects to return a Boolean, and an option to specify the `this` keyword.  
The callback takes the standard `value`, `key`, `collection` arguments.  


## learnRx

```js
Array.prototype.filter = function( predicateFunction ) {
  var results = [];
  this.forEach( function( itemInArray )){
    if ( predicateFunction( itemInArray )) {
      results.push( itemInArray );
    }
  });
    return results;
};
```
___
## Underscore

```js
_.filter = _.select = function(obj, predicate, context) {
  var results = [];
  predicate = cb( predicate, context );
  _.each( obj, function( value, index, list) {
    if ( predicate( value, index, list )) results.push( value );
  });
  return results;
};
```
___
## Lodash

```js
_.filter = function( collection, test ) {
  var result = [];

  _.each( collection, function( item ) {
    if (test( item )) {
      result.push( item );
    }
  });

  return result;
};
```
___
## Ramda
Oh goodness. I'll get back to this one... 

```js
    var filter = _curry2(_dispatchable('filter', _xfilter, function ( pred, filterable) {
        return _isObject( filterable) ? _reduce( function ( acc, key) {
            if ( pred( filterable[key])) {
                acc[key] = filterable[key];
            }
            return acc;
        }, {}, keys(filterable)) : // else
        _filter(pred, filterable);
    }));

```

___

## fn

```js
fn.filter = function ( expression, collection ) {
  return fn.reduce( function ( accumulator, item, index ) {
    expression( item, index ) && accumulator.push( item );
    return accumulator;
  }, [ ], collection);
};
```


___
___
___
# `reduce()`
Takes one argument `callback` - an aggregation function. This callback takes the same paramaters as the others `value`, `key`, `collection`, except they come AFTER an `accumulator`. The `accumulator` is the aggrigate value as reduce moves through a collection. The is also the option of providing an `initialValue` - where in the array to start - if none is given, start at the begining and reduce the entire array into a single value. 

>>>Syntax 
```js
arr.reduce( callback( accumulator, value, key, collection ) [initialValue] )
```

___
## learnRx

```js
Array.prototype.reduce = function( combiner, initialValue ) {
  var counter, accumulatedValue;
  if (this.length === 0) {
    return this;
  }
  else {
    if (arguments.length === 1) {
      counter = 1;
      accumulatedValue = this[0];
      else if {
        (arguments.length >= 2) {
          counter = 0;
          accumulatedValue = initialValue;
      } 
      else {
          throw "Invalid arguments.";
        }
  while (counter < this.length) {
    accumulatedValue = combiner(accumulatedValue, this[counter] )
    counter++;
  }
  return [accumulatedValue];
      }
    }
  }
}
```

* If the array is empty, do nothing
* If the user didn't pass an initial value, use the first item.
* If there are two arguments (the first is `combiner` and second is `initalValue`)
  - set `counter` to 0, and
  - set `accumulatedValue` to the value of the the `initialValue`
* The first argument `combiner` is a function that takes in the `accumulated value`  and the current item in the array. 
* Loop through the array, feeding the current value and the result of the previous computation back into the `combiner` function until  we've exhausted the entire array and are left with only one item.
* Return the final `accumulatedValue` inside an array (one item long)


___
## Lodash

```js
_.reduce = function(collection, iterator, accumulator) {
  var result = accumulator === undefined ? collection[0] : accumulator;
  _.each(collection, function(item) {
    result = iterator(result, item);
  });
  return result;
};
```

___
## fn

```js
fn.reduce = function (handler, accumulator, collection, params) {
  fn.each(function (value, index) {
    accumulator = fn.apply(handler, fn.concat([ accumulator, value, index ], params));
  }, collection);

  return accumulator;
};
```

___




