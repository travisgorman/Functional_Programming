# Implementing Higher-Order Functions
Different implementations of higher order functions. Refering mostly to:
* [Standard ECMA-626 Language Specification](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
* [Underscore](http://underscorejs.org/)
* [Lodash](https://lodash.com/docs)
* [Underbar]
* [Ramda](http://ramdajs.com/0.18.0/docs/)
* [functional](http://functionaljs.com/)

>>> "**Iteratee:** a composable abstraction for processing sequentially presented chunks of input data in a purely functional fashion".  
>>> When refering to a function that is passed into another function, I will say "callback"


This is purely for educational purposes - just to have a look under the hood at how some libraries are writing implementations of native JavaScript higher-order functions. 


## `forEach`
Iterate over an input `list`, calling a provided function, `fn` for each element in the list.

### Ramda

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
