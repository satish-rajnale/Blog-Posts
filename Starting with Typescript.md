# Question 3: What is anti-shake and throttling? What's the difference? How to achieve?
## _What is throttling and anti shake?_

##### Throttling:
for example, click the button to make the box move 50px. The first time you click the button, the box starts to move, and then you keep clicking the button before the box moves 50 Px, but the box will not move more than 50 px. After moving to the position of 50px, you can click the button again to make the box move to the next 50px. Summary: at the first operation, start to execute. Before the end of the first execution, no matter how you operate it, it will be invalid. It's like when you want to drink water, turn on the tap of the water dispenser and get a cup of hot water. Just turn off the tap. When you want to pour a glass of water, turn on the tap to let it go. It won't be on all the time. This saves water, so it's called throttling.

##### Anti shake:
also take sending verification code as an example. Up on the non-stop point button, the result it does not send verification code. When you stop clicking, it starts sending the captcha. Summary: the operation is invalid all the time. At the last operation, the execution starts. Continuous operation for many times, will only start in the last operation. To prevent hand shaking operation, so called anti shaking.


## difference:

- Throttling is the first click valid in a period of time, other clicks are invalid.
- Anti shake is the last click in a period of time is valid, other clicks are invalid.


##### Thinking:

 `Throttling idea` add a throttle valve to the operation function.
   - Open the throttle valve before executing the function.
   - At the beginning of the function, the throttle is closed.
   - When the function is finished, open the throttle valve.
   - 
`Anti shake idea`:
- I first set a delay time (timer), in this time, if you operate five times, 
- I will clear the previous four operations (clear timer triggered function), do not let the previous four operations to execute. 
- When the delay time is up, you can perform your fifth operation.



## achieve

### Anti-shake realization
```js
 // Anti-shake
      function debounce(fn, wait) {
          var timeout = null;
          return function() {
              if(timeout !== null)   clearTimeout(timeout);
              timeout = setTimeout(fn, wait);
          }
      }
      // Processing function
      function handle() {
          console.log(Math.random());
      }
      // scroll event
      window.addEventListener('scroll', debounce(handle, 1000));

```
#### Realization of throttling
 > Throttle code (time stamp)


```js
 //Throttle code (time stamp)
      var throttle = function(func, delay) {
        var prev = Date.now();
        return function() {
          var context = this;
          var args = arguments;
          var now = Date.now();
          if (now - prev >= delay) {
            func.apply(context, args);
            prev = Date.now();
          }
        }
      }
      //Processing function
      function handle() {
      　　console.log(Math.random());
      }
      //Scroll event
      window.addEventListener('scroll', throttle(handle, 1000));
```

> Throttle code (timer):

```sh
 // Throttle code (timer):
     var throttle = function(func, delay) {
         var timer = null;
         return function() {
             var context = this;
             var args = arguments;
             if (!timer) {
                 timer = setTimeout(function() {
                     func.apply(context, args);
                     timer = null;
                 }, delay);
             }
         }
     }
     //Processing function
     function handle() {
         console.log(Math.random());
     }
     //Scroll event
     window.addEventListener('scroll', throttle(handle, 1000));
```


`<> By Satish Rajnale </>`
