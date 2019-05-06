## Chapter 5: Scope Closure

**Closure is all around you in JavaScript, you just have to recognize and embrace it.**

Closures happen as a result of writing code that relies on lexical scope. Closures are created and used for you all over your code.

Understanding closures is like when Neo sees the Matrix for the first time.

### Nitty Gritty

*Closure is when a function is able to remember and access its lexical scope even when that function is executing outside its lexical scope.*

```javascript
function foo() {
    var a = 2;
    
    function bar() {
        console.log( a );  // 2
    }
    
    bar();
}

foo();
```



From a purely academic perspective, what is said of the above snippet is that the function `bar()` has a *closure* over the scope of `foo()`.

More simple, `bar()` closes over the scope of `foo()`. This is because `bar()` appears nested inside of `foo()`.

```javascript
function foo() {
    var a = 2;
    
    function bar() {
        console.log( a );
    }
    
    return bar;
}

var baz = foo();

baz(); // 2 -- closure was just observed
```



By virtue of where it was declared, `bar()` has a lexical scope closure over that inner scope of `foo()`, which keeps that scope alive for `bar()` to reference at any later time.

**`bar()` still has a reference to that scope, and that reference is called closure.**

So, a few microseconds later, when the variable `baz` is invoked (invoking the inner function we initially labeled `bar`), it duly has *access* to author-time lexical scope, so it can access the variable `a` just as we'd expect.

The function is being invoked well outside of its author-time lexical scope. **Closure** lets the function continue to access the lexical scope it was defined in at author-time.

These passing around of functions can be indirect too:

```javascript
var fn;

function foo() {
    var a = 2;
    
    function baz() {
        console.log( a );
    }
    
    fn = baz; // assign `baz` to global variable
}

function bar() {
    fn(); // I saw closure!
}

foo();

bar(); // 2

```



### Now I Can See

### Loops + Closure

```javascript
for (var i=1; i<=5; i++) {
  setTimeout( function timer(){
    console.log( i );
  }, i*1000 );
}
```

This actually prints out `6` 5 times rather than `1..5`.

The way scope works, all 5 of those functions, though they are defined separately in each loop iteration, all **are closed over the same shared global scope**, which has, in fact, only one `i` in it.

Let's try:

```javascript
for (var i=1; i<=5; i++) {
  (function(){
    setTimeout( function timer(){
      console.log( i );
    }, i*1000);
  })();
}
```

This still does not work. Each timeout function callback is indeed closing over its own per-iteration scope created respectively by each IIFE.

It's not enough to have a scope to close over **if that scope is empty**. Our IIFE is just an empty do-nothing scope. It needs *something* in it to be useful to use.

It needs its own variable, with a copy of the `i` value at each iteration.

```javascript
for (var i=1; i<=5; i++) {
  (function(){
    var j = i;
    setTimeout( function timer(){
      console.log( j );
    }, j*1000);
  })();
}
```

**This works**.

Slight variation some prefer:

```javascript
for (var i=1; i<=5; i++) {
  (function(j){
    setTimeout( function timer(){
      console.log( j );
    }, j*1000);
  })(i);
}
```

The use of an IIFE inside each iteration created a new scope for each iteration, which gave our timeout function callbacks the opportunity to close over a new scope for each iteration, one which had a variable with the right per-iteration value in it for us to access.

#### Block Scoping Revisited

