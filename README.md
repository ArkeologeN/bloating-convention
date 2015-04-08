# bloating-convention
The convention that I usually follow in my code.

# Variable Declaration;

```javascript
// Scope variables.
var varA = 'A',
    varO = {},
    varC = [];
    
  
// Property variables.
function thisIsClass() {
  this._propertyVar = 'I am property, sorry!';
}

// Constants (Capitlize variable)
var FLEX_MODE = 0;

```

# Convert to Float

This will type-cast from String(float) to `Float` type in JavaScript.

```javascript
var input = '3.3';

// No
var result = parseFloat(input);

// YES
var result = +input;

```

# Convert to Integer

This will case from String(int) to `Integer` type in JavaScript.

```javascript
var input = '3.3';

// NO
var result = parseInt(value);

// YES
var result = ~~value;

// YES
var result = value << 0;
```

# Function Declaration

```javascript

// If definiting in context
function eatElephant() {

}

// If function is declared inside another function as `private`
function Carnivores() {
  // `eatElephant` is only available inside Carnivores class.
  var eatElephant = function eatElephant() {
  // I actually named the anonymous function with the variable name because it will help in stack trace later instead of showing `anonymous()`.
  }
}

// If function is defined a property of a class (function)
function Parser() {
  this.initialize = function() {
    // It would only accessible after creating `new` instance of `Parser` class
  }
}
```

# IIFE at module.
Using IIFE at the start of every module (in node.js) helps in preventing the global context been polluted.

```javascript
;(function() {
  // ...
})();

// Leading ; (semi-colon) prevents from previously executed statement been terminated.
```

The best practice is to propose the strict statement at the file (module) level or function level. As IIFE is added in every module, so I believe, making it strict would tackle the convention of inner-code as well.

```javascript
;(function() {
  'use strict';
})();
```

# Type comparision

```javascript
function Animal() {
  // ...
}

var animal = new Animal();
var numbers = [1, 2, 3, 4, ..., 100];
var obj = {};
var str = 'boo!';

// NO
typeof animal === 'function';

// NO
typeof animal === 'object';

// YES
animal instanceof Animal

// NO
typeof numbers === 'object';

// YES
numbers instanceof Array

// YES (Recommended)
typeof obj === 'object';

// YES
obj instanceof Object.

// YES
typeof str === 'string';

// NO
str instanceof String

// NO
str == 'boo!';

// YES
str === 'boo!';
'boo!' === str;

// NO
if (str === 'boo!') {
  callback(null, 'I am boo!');
} else {
  callback(null, 'I am not boo!');
}

// YES
if (str !== 'boo!') {
  return callback(null, 'I am not boo!');
}

return callback(null, 'I am boo!');

``` 

# Error handling.

In Node.js, error handling is important due to async i/o nature. If we have async code, try to handle errors via callback, however, if its async snippet, handle with try-catch.

The following snippet is wrong one and not recommended.
```javascript
var lib = require('/path/to/lib');

// Assuming, `callback` is available.

lib.getDataFromSomewhere(url, function(err, data) {
  callback(JSON.parse(data));
});
```

However, the following is the recommended approach.

```javascript
var lib = require('/path/to/lib');

lib.getDataFromSomewhere(url, function(err, data) {
  if (err) {
    return callback(err);
  }
  
  var result = {};
  
  try {
    result = JSON.parse(data);
  } catch(e) {
    return callback(e);
  }
  
  return callback(null, result);
});
```

As you can see, first we're checking for `err` variable to see if service raised any error. Note that we're passing two arguments to our callback function with the order of `err` and `data`. The first argument looks for error and the second for data. If first argument found, it will overlook the second one and process with the first (err) one.

So, try-catch block doesn't work over async code and it will bubble it up doing nothing. JSON.parse() is a synchoronous process and that could be handled through it.

# Arguments augmentation

Perhaps, never trust over the input of the function as javascript is type-safe language and it always try to convert everything in function at runtime which makes it easy for it to execute string referenced functions.

Before putting any logic, always try to check for inputs, prepare the default ones to avoid any exception and proceed. An example could be as followed:

```javascript

// Do a binary search to find number in input
function bsearch(input, find) {
  input = input || [];
  // OR
  (input || input = []);
  
  if (!find) {
    // Raise error because we cannot do anything with `find` value.
  }
  
}
```

# Iteration

Though, JavaScript comes up with `forEach` over Array prototype but its not that rock fast. The good alternatives could be as followed:

```javascript
var arr = [1,2,3,4,....,100];

// NO
arr.forEach(function(num) {
  var square = num * num;
  // ....
});

// YES
for (var k = 0; k < arr.length; k++) {
  var num = arr[k];
  var square = num * num;
  // ...
}

// YES
var _ = require('underscore');
_.each(function(num) {
  var square = num * num;
  // ...
});
```

Note that counter loop (for) and underscore.js based iteration (each) are highly optimized and designed to be executed on any input (string or array). However, `forEach` native is declared over `Array.prototype`.
