# Question 4: In-depth analysis of call and apply principles, usage scenarios and implementation
## _Call() and apply()_

> The call() method calls a function that has a specified `this` Value and separately supplied `parameters` (List of parameters)。

 ```js
 //The difference is thatcall()The method accepted isa list of several parameters,andapply()The method accepted isAn array of multiple parameters
 
 var func = function(arg1, arg2) {
     ...
};
 
func.call(this, arg1, arg2); / / Use call, parameter list
func.apply(this, [arg1, arg2]) / / Use apply, parameter array
 ```
 
#### Scenes to be used:
Here are some common usages
 ##### 1. merge two arrays
 ```js
 var vegetables = ['parsnip', 'potato'];
var moreVegs = ['celery', 'beetroot'];
 
// merge the second array into the first array
// is equivalent to vegetables.push('celery', 'beetroot');
Array.prototype.push.apply(vegetables, moreVegs);
// 4
 
vegetables;
// ['parsnip', 'potato', 'celery', 'beetroot']
 ```
 When the second array (as in the example moreVegs) **Don't use this method to merge arrays when they are too large**, becauseThe number of parameters a function can accept is limited. Different engines have different limits. The JS core is limited to 65535. Some engines throw exceptions, some don't throw exceptions but lose redundant parameters.
 
 ##### How to solve it? The method isLoop the parameter array into a target method after it is diced
 ```js
 function concatOfArray(arr1, arr2) {
    var QUANTUM = 32768;
    for (var i = 0, len = arr2.length; i < len; i += QUANTUM) {
        Array.prototype.push.apply(
            arr1, 
            arr2.slice(i, Math.min(i + QUANTUM, len) )
        );
    }
    return arr1;
}
 
// verification code
var arr1 = [-3, -2, -1];
var arr2 = [];
for(var i = 0; i < 1000000; i++) {
    arr2.push(i);
}
 
Array.prototype.push.apply(arr1, arr2);
// Uncaught RangeError: Maximum call stack size exceeded
 
concatOfArray(arr1, arr2);
// (1000003) [-3, -2, -1, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, ...]
 ```

 ##### 2. merge two arrays
```js
var numbers = [5, 458 , 120 , -215 ]; 
Math.max.apply(Math, numbers);   //458    
Math.max.call(Math, 5, 458 , 120 , -215); //458
 
// ES6
Math.max.call(Math, ...numbers); // 458
```
  ##### 3. verify whether it is an array
  
 ```js
 function isArray(obj){ 
    return Object.prototype.toString.call(obj) === '[object Array]';
}
isArray([1, 2, 3]);
// true
 
// Use toString() directly
[1, 2, 3].toString(); 	// "1,2,3"
"123".toString(); 		// "123"
123.toString(); 		// SyntaxError: Invalid or unexpected token
Number(123).toString(); // "123"
Object(123).toString(); // "123"
 ```
 **Another verify Array Metthod**
 ```js
 var toStr = Function.prototype.call.bind(Object.prototype.toString);
function isArray(obj){ 
    return toStr(obj) === '[object Array]';
}
isArray([1, 2, 3]);
// true
 
// Use the modified toStr
toStr([1, 2, 3]); 	// "[object Array]"
toStr("123"); 		// "[object String]"
toStr(123); 		// "[object Number]"
toStr(Object(123)); // "[object Number]"
 ```
  - In the above method first `Function.prototype.call(Function)` specifies a `this` Value, then `.bind` Return a new function that will always be `Object.prototype.toString` Set to pass in parameters. Actually equivalent to `Object.prototype.toString.call() `
 
##### 4. the class array object (Array-like Object) using the array method
```js
var domNodes = document.getElementsByTagName("*");
domNodes.unshift("h1");
// TypeError: domNodes.unshift is not a function
 
var domNodeArrays = Array.prototype.slice.call(domNodes);
domNodeArrays.unshift("h1"); // 505 data is different in different environments
// (505) ["h1", html.gr__hujiang_com, head, meta, ...] 
Copy code
```
**Class array objects have the following two properties**
1. with: a numeric index subscript to the object element and length Attributes
2. does not have: `push`、`shift`、`forEach` as well as `indexOf` Methods such as array objects

To be explained, the class array object is a `Object`. There is a name in JS Class array-Object structure, such as `arguments` Objects, as well as returned by the DOM API NodeList Objects **belong to** `class array objects`, class array objects cannot be used **push/pop/shift/unshift** Array method, pass `Array.prototype.slice.call` to convert to a real array and you can use the methods.
##### Class array object to arrayOther methods:
```js
// The above code is equivalent to
var arr = [].slice.call(arguments)；
 
ES6:
let arr = Array.from(arguments);
let arr = [...arguments];
Copy code
```
Array.from() You can turn two types of objects into real arrays: Class array Object and Traversable(iterable) objects (including ES6's new data structures `Set and Map`).

**Question :** Why do you have class array objects? Or why the class array object is what solves the problem?

>JavaScript typed arrays are an array-like Object And provides a mechanism for accessing raw binary data. Array-stored objects can be dynamically increased and decreased, and any JavaScript value can be stored. The JavaScript engine does some internal optimizations so that operations on arrays can be fast. However, as web applications become more powerful, especially with new additions such as audio and video editing, access to raw data from WebSockets, etc., it's clear that sometimes using JavaScript code can quickly and easily pass typed arrays. It would be very helpful to manipulate the raw binary data.

In a word, you can manipulate complex data faster.

#### 5. call the parent constructor to achieve inheritance
```js
function  SuperType(){
    this.color=["red", "green", "blue"];
}
function  SubType(){
    // core code, inherited from SuperType
    SuperType.call(this);
}
 
var instance1 = new SubType();
instance1.color.push("black");
console.log(instance1.color);
// ["red", "green", "blue", "black"]
 
var instance2 = new SubType();
console.log(instance2.color);
// ["red", "green", "blue"]
Copy code
```

 `Disadvantages:` add a throttle valve to the operation function.
   - Can only inherit the parent class Instance Properties and methods, cannot inherit prototype properties/methods
   - Unable to implement reuse, each subclass has a copy of the parent class instance function, affecting performance

##### Call simulation implementation
> The following content refers to **JavaScript deep call and apply simulation implementation**

```js
var value = 1;
var foo = {
    value: 1
};
 
function bar() {
    console.log(this.value);
}
 
bar.call(foo); // 1
Copy code
```
Through the above introduction we know that call() There are two main points
 1. call() Changed the direction of this
 2. function bar Executed
 
##### Simulation implementation first step
```js
var foo = {
    value: 1,
    bar: function() {
        console.log(this.value);
    }
};
 
foo.bar(); // 1
Copy code
```
This change can be implemented: change the pointer to this and execute the function bar. But writing this has `side effects`, that is, giving foo Added an additional attribute, how to solve it?
The solution is simple, use `delete` Just delete it.

So as long as the following 3 steps are implemented, the simulation can be implemented.
1. Set the function to the properties of the object: **foo.fn = bar**
2. the execution function: **foo.fn()**
3. delete the function: **delete foo.fn**
4. 
The code is implemented as follows:
```js
// first edition
Function.prototype.call2 = function(context) {
    // First get the function that calls call, use this to get
    context.fn = this; 		// foo.fn = bar
    context.fn();			// foo.fn()
    delete context.fn;		// delete foo.fn
}
 
// have a test
var foo = {
    value: 1
};
 
function bar() {
    console.log(this.value);
}
 
bar.call2(foo); // 1
Copy code
```
`perfect!`
##### Simulation implementation of the second step
The first version has a problem, that is the function bar Can't receive parameters, so we can get from arguments Get the parameters, take the second to the last parameter and put it in the array, why should we discard the first parameter, because the first parameter is `this`。

The method of converting an array object into an array has already been introduced above, but this is done using the ES3 scheme.



`Anti shake idea`:
- I first set a delay time (timer), in this time, if you operate five times, 
- I will clear the previous four operations (clear timer triggered function), do not let the previous four operations to execute. 
- When the delay time is up, you can perform your fifth operation.
```js

var args = [];
for(var i = 1, len = arguments.length; i < len; i++) {
    args.push('arguments[' + i + ']');
}
Copy code
```
The parameter array is fixed, the next thing to do is to execute the function.context.fn()。
```js
context.fn( args.join(',') ); // That does not work
Copy code
```
The above direct call will definitely not work,args.join(',')Will return a string and will not execute.
Adopted here `eval` The method is implemented to form a function.
```js
eval('context.fn(' + args +')')
Copy code
```
In the above code args Will be called automatically args.toString() Method because'context.fn(' + args +')' Essentially string concatenation, it will be called automatically toString() Method, the following code:

```js
var args = ["a1", "b2", "c3"];
console.log(args);
// ["a1", "b2", "c3"]
 
console.log(args.toString());
// a1,b2,c3
 
console.log("" + args);
// a1,b2,c3
Copy code
```
So the second version is implemented, the code is as follows:
```js
// second edition
Function.prototype.call2 = function(context) {
    context.fn = this;
    var args = [];
    for(var i = 1, len = arguments.length; i < len; i++) {
        args.push('arguments[' + i + ']');
    }
    eval('context.fn(' + args +')');
    delete context.fn;
}
 
// have a test
var foo = {
    value: 1
};
 
function bar(name, age) {
    console.log(name)
    console.log(age)
    console.log(this.value);
}
 
bar.call2(foo, 'kevin', 18); 
// kevin
// 18
// 1
Copy code
```

##### Simulation implementation of the third step
There are 2 more details to note:
 1. this parameter can be passednull OrundefinedAt this time this points to the window
 2. this parameter can pass basic type data, the original call will automatically use Object () conversion
 3. the function can have a return value
```js
// Third edition
Function.prototype.call2 = function (context) {
    context = context ? Object(context) : window; // implementation details 1 and 2
    context.fn = this;
 
    var args = [];
    for(var i = 1, len = arguments.length; i < len; i++) {
        args.push('arguments[' + i + ']');
    }
 
    var result = eval('context.fn(' + args +')');
 
    delete context.fn
    return result; // implementation details 2
}
 
// have a test
var value = 2;
 
var obj = {
    value: 1
}
 
function bar(name, age) {
    console.log(this.value);
    return {
        value: this.value,
        name: name,
        age: age
    }
}
 
function foo() {
    console.log(this);
}
 
bar.call2(null); // 2
foo.call2(123); // Number {123, fn: ƒ}
 
bar.call2(obj, 'kevin', 18);
// 1
// {
//    value: 1,
//    name: 'kevin',
//    age: 18
// }
Copy code
```

## Call and apply simulation implementation summary
##### Call simulation implementation

ES3：
```js
Function.prototype.call = function (context) {
    context = context ? Object(context) : window; 
    context.fn = this;
 
    var args = [];
    for(var i = 1, len = arguments.length; i < len; i++) {
        args.push('arguments[' + i + ']');
    }
    var result = eval('context.fn(' + args +')');
 
    delete context.fn
    return result;
}
Copy code
```
ES6：
```js
Function.prototype.call = function (context) {
  context = context ? Object(context) : window; 
  context.fn = this;
 
  let args = [...arguments].slice(1);
  let result = context.fn(...args);
 
  delete context.fn
  return result;
}
Copy code
```
##### Analog implementation of apply
ES3
```js
Function.prototype.apply = function (context, arr) {
    context = context ? Object(context) : window; 
    context.fn = this;
 
    var result;
    / / Determine whether there is a second parameter
    if (!arr) {
        result = context.fn();
    } else {
        var args = [];
        for (var i = 0, len = arr.length; i < len; i++) {
            args.push('arr[' + i + ']');
        }
        result = eval('context.fn(' + args + ')');
    }
 
    delete context.fn
    return result;
}
Copy code
```
ES6：
```js
Function.prototype.apply = function (context, arr) {
    context = context ? Object(context) : window; 
    context.fn = this;
  
    let result;
    if (!arr) {
        result = context.fn();
    } else {
        result = context.fn(...arr);
    }
      
    delete context.fn
    return result;
}
Copy code
```