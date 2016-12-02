# The `var ` keyword and the variable's scope

In JavaScript, scope determines where your program can see and use certain variables. There are two kinds of scope: global and local.

**Global** : variables defined *outside* a function are global and accessible from anywhere in your code.

**Local** : variables defined *inside* of a function are considered local to that function only. It can't be accessed or used by any of your code outside that function.



That being said, there is a big gotcha we need to be aware of when we declare a variable: **if a local variable is used without being declared with the var keyword, it becomes a global variable.**



Consider the following code : 

```javascript
var myVariable = "Hi, I am a global variable";

function sayHello() {
  myVariable = "Hi, I am locally produced!";
  console.log(myVariable);
}

console.log(myVariable);    // Prints "Hi, I am a global variable"
sayHello();                 // Prints "Hi, I am locally produced!"
console.log(myVariable);    // Prints "Hi, I am locally produced!"
```

[→ Try it yourself](https://jsbin.com/mowudafeju/1/edit?js,console)



`myVariable` is defined *inside* a function, and should therefore be available only in the *local scope* of the `sayHello` function. In other words, it should not be accessible from *outside* of the `sayHello   ` function.

However, once `sayHello` has been invoked, the global variable `myVariable` is overwritten by the local variable and now has the value that was defined locally. Why ?

Notice in this example that `myVariable` in our `sayHello()` function doesn't have the `var` keyword as a prefix. We stated before that **a local variable (inside a function) declared without the `var`  keyword becomes a global variable (accessible in the global scope)**.

Following this rule, the gobal variable was reassigned a new value when the `sayHello()` function ran. As a result, `myVariable` has a different value before and after `sayHello` is run.



This also means that a variable in your local scope can have the same name as a variable in your global scope: if they are both declared with the `var` keyword, they are in fact two distinct variables, independent from one another. By adding `var` before the variable inside the function, we create a *new* variable, that exists *only in the scope* of that function.



As a rule of thumb, to avoid getting unexpected behaviours of your code : **always declare your local variables with the var keyword before using them**.


---------------
*Léna Faure - 11/27/2016 - Career Path 3: Modern Front-End Developer*

