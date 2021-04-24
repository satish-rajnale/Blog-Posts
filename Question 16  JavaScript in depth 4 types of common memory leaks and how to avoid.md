
# Question 16 : JavaScript in-depth 4 types of common memory leaks and how to avoid


## _What are possible causes of memory leaks?_
Advance JavaScript concepts
#### Garbage collection algorithm:
The commonly used garbage collection algorithm is called **Mark-and-sweep**, and the algorithm consists of the following steps:
1. The garbage collector creates a " roots " list. Roots are usually references to global variables in the code. In JavaScript, the "window" object is a global variable and is treated as root. The window object always exists, so the garbage collector can check whether it and all its child objects exist (that is, it is not garbage);

2. All roots are checked and marked as active (ie not junk). All child objects are also checked recursively. If all objects starting from root are reachable, they are not treated as garbage.

3. All unmarked memory will be treated as garbage, and the collector can now release the memory and return it to the operating system.

> Modern garbage collectors have improved the algorithm, but the essence is the same: reachable memory is marked, and the rest is treated as garbage.

### Four common JS memory leaks

1. Unexpected global variables
Undefined variables will create a new variable in the global object, as follows.
```js
function  myfunc( arg )  { 
    bar  =  "this is a hidden global variable" ; 
}
```
Function myfunc internal forget to use `var`, in fact JS would assume bar mounted to the global object and accidentally create a global variable

```js
function  foo ( arg )  { 
    window . bar  =  "this is an explicit global variable" ; 
}
```
Another surprise may be provided by global variables `this` created.

```js
function  foo ( )  { 
    this.variable  =  "potential accidental global" ; 
}
// Foo calls itself, this points to the global object (window) 
// instead of undefined 
foo ( ) ;
```
#### Solution :
Add to the head of the JavaScript file to `'use strict'` use strict mode to avoid unexpected global variables. At this time, the this in the above example points to `undefined`. If you must use a global variable to store a large amount of data, make sure to set it to null or redefine it after use.

2. The forgotten timer or callback function
Timer `setInterval` codes are very common

```js
var  local =  getData ( ) ; 
setInterval ( function ( )  { 
    var  node  =  document . getElementById ( 'Node' ) ; 
    if ( node )  { 
  // Process node and data
  node.innerHTML  =  JSON.stringify ( local ) ; 
    } 
} ,  1000 ) ;
```
The above example shows that when the node or data is no longer needed, the timer still points to the data. So even when the node is removed, the interval is still alive and the garbage collector cannot reclaim it, and its dependencies cannot be reclaimed unless the timer is terminated.
```js
var  element  =  document . getElementById ( 'button' ) ; 
function  onClick( event ){ 
    element.innerHTML  =  'text' ; 
}

element.addEventListener ( 'click' ,  onClick ) ;
```
For the observer example above, once they are no longer needed (or the associated object becomes unreachable), it is important to explicitly remove them. Old IE 6 cannot handle circular references. Because the old version of IE cannot detect circular references between DOM nodes and JavaScript code, it will cause memory leaks.
**However** , modern browsers (including IE and Microsoft Edge) use more advanced garbage collection algorithms (mark removal), which can already detect and process circular references correctly. That is recovered node memory, you do not have to call `removeEventListener` up.

3. References away from the DOM
If you save the DOM as a dictionary (JSON key-value pair) or an array, at this time, there are two references to the same DOM element: one in the DOM tree and the other in the dictionary. Then both references will need to be cleared in the future.

```js
var  elements  =  { 
    button : document.getElementById ( 'button' ) , 
    image : document.getElementById ( 'image' ) , 
    text : document.getElementById ( 'text' ) 
} ; 
function  doStuff ( )  { 
    image.src ='http://some.url/image' ; 
    button.click ( ) ; 
    console.log ( text . innerHTML ) ; 
    // More logic 
} 
function  removeButton ( )  { 
    // Button is a descendant element of 
    document.body.removeChild ( document.getElementById( 'button' ) ) ; 
    // At this point, it still exists A global #button reference 
    // elements dictionary. The button element is still in memory and cannot be recycled by GC. 
}
```
If the code is saved in a certain form `<td>` references.And in future decided to delete the entire table when the GC will intuitively think in addition to recycling saved `<td>` other nodes outside.
 Is not the case: this `<td>` is a form of child nodes, child element of the parent element is a reference to the relationship. Since the code retains `<td>` references , yet to cause the entire table in memory. So be careful when saving DOM element references.

4. Closure
The key to closures is that anonymous functions can access variables in the parent scope.

```js
var  football =  null ; 
var  replaceBall =  function( ) { 
  var  firstBall =  football ; 
  var  unused  =  function( ) { 
    if  ( firstBall ) 
      console.log ( "Got it!" ) ; 
  } ;
    
  football =  { 
    reallyLongArr : new Array(1000000).join ( '*' ) , 
    goal : function( ) { 
      console.log( message ) ; 
    } 
  } ; 
} ;

setInterval ( replaceBall  ,  1000 ) ;
```
Each time it is called `replaceBall`, 
`football` a `goal` new object containing a large array and a new closure ( ) is obtained. Meanwhile, the variable unused is a reference to the `firstBall` closure (previously `replaceBall` they call `football `). `goal` can be `football` used, `goal` and unused shared closure scope, although unused never used, it refers to `firstBall` force it to remain in memory (to prevent recovery).

#### Solution :
In `replaceBall` the final addition `firstBall` = null.
`<> By Satish Rajnale </>`