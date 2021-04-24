* JavaScript function add(1)(2)(3)(4) to achieve infinite accumulation-step by step principle analysis


**Question:** We have a requirement to implement an infinitely cumulative function with js, like 
> add(1) //=> 1; 
> add(1)(2) //=> 2; 
     add(1)(2)( 3) //=> 6; 
add(1)(2)(3)(4) //=> 10; 
and so on... 


At first glance, it looks amazing. Now I will implement such an add() function step by step.

1. we now define a `toSting()` method for the function sum
```js
function sum(a){
    return  a+1;
}
sum.toString = function(){return 2;}
console.log(sum);
typeof(sum)
```

<img width="100%" src="https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6ytog28qwnmg5gc2uq8o.PNG"/>


After defining the `sum.toString` method, if you directly console.log(sum), this function will be printed out. If you directly alert(sum), we can see that "2" will pop up. Let's check the type of sum (typeof(sum)),

Obviously sum is a function. 

2. Okay, now let's wrap this s function with a "coat" -:)
```js
function add(a){
 function sum(a){
    return  a+1;
 }
 sum.toString = function(){return a;}
 return sum;
}
console.log(add(3));
```
<img width="100%" src="https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vmc4nit0ae3ukc1fvk35.PNG"/>
 
In the above code, we have wrapped the previous code with a layer, and also modified the `sum.toString()` method so that it returns the parameter a passed in from the outside, instead of the previously fixed 2.
After wrapping a layer, the return value is a function, thus forming a closure.  In this way, when we call `add(3)`, the return value is actually a function, the function `sum` inside.

However, when we alert(sum(3)), 3 will pop up.

4. Last step
```js
function add(a){
 function sum(b){
    a =   a+b;
    return sum;
 }
 sum.toString = function(){return a;}
 return sum;
}
console.log(add(1)(2)(3)(4));
```
<img width="100%" src="https://dev-to-uploads.s3.amazonaws.com/uploads/articles/lofds8mnsw46fadmid1t.PNG"/>

At this point, we can see that the above console.log(add(1)(2)(3)(4)); this sentence prints out a function, function 10, in fact, when alert(add(1)(2 )(3)(4));, it will pop up 10.
This is the implementation process of add(1)(2)(3)(4);, obviously, our accumulating function can be called infinitely. -:) 

#### The whole realization process is two key points.
1. Use closures and have a deep understanding of JavaScript's scope chain (prototype chain);

2. Rewrite the toSting() method of the function;

let's analyze add(1)(2)(3);step by step:

a) Execute add(1);   
What is returned is the sum function inside. Through the closure, the variable a=1 in the sum function can be accessed; so when we alert(add(1));, the toSting() method called will change the scope (prototype chain ) The inside a = 1 pops up.

b) Execute add(1)(2);
<=== is equivalent to ===> sum(2); 
 This is equivalent to passing 2 to b in the sum() function, so that a in the scope (prototype chain) = a+b, at this time a = 3, continue to be saved in the scope. Then it returns to the sum function.


c) Execute add(1)(2)(3); 
<=== is equivalent to ===> sum(3); 
 Same as the analysis in (step b) above, except that a = 6 in the scope is updated, and then the sum function is also returned.
 