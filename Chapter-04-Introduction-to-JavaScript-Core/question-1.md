# JavaScript’s `apply` and `call`  Methods



Functions in JavaScript are object, and as objects they have several methods. **`call()`** and **`apply()`** are part of these predefined JavaScript function methods. 

When do we use `call`  and `apply` in our code ? We use these two methods when we want full control over what the keyword `this` refers to: if we have a function that refers to the keyword `this`, we need to make sure that `this` will be what we expect it to be when we invoke the function. Both methods can be used to invoke a function, and both methods must have the owner object as first parameter. 

Consider this code:

```javascript
var customer = {
  firstName: "John",
  lastName: "Doe",
  greetCustomer: function(){
    console.log("Hello again " + this.firstName + " " + this.lastName + "!");
  }
}

var customer2 = customer.greetCustomer;
customer2(); // Prints "Hello again undefined undefined!"
```

[→ Try it yourself](https://jsbin.com/bomobipahe/edit?js,console) 

Here we have an object `customer` with properties `firstName`, `lastName` and `greetCustomer`.  `greetCustomer` is assigned to a function that uses the `this ` keyword to get the object's properties `firstName` and `lastName`. 



We then defined a new variable `customer2` and assigned it the same function that `customer.greetCustomer` refers to. But if we invoke our new function `customer2` as it is, we cannot access the properties of the object, because `this` now contains the `customer2` object, which has no `firstName` or `lastName` variable.

We can fix this problem by using either the `call` or `apply`  methods.



#### Using `call()` and `apply()`

By using the `call `  or `apply` methods, we can change what we want the value of `this  ` to be : here we are going to change the value of `this` to be the `customer2`object: 

```javascript
customer2.call(customer); // Prints "Hello again John Doe!"
customer2.apply(customer); // Prints "Hello again John Doe!"
```

[→ Try it yourself](https://jsbin.com/qubuyebaya/edit?js,console) 

It worked ! Note that we are not *invoking* `customer2` , we just *attach* the method `call` or `apply` onto it, so there are no parentheses after the `customer2` method when we use `call`  or `apply`.

The parameter to the `call`  and `apply`  method is what we want the keyword `this` to refer to, in this case the `customer` object.



#### The difference between `call()` and `apply()`

The only difference between `call` and `apply` is when we have arguments to the function we want to use `call ` or `apply` on.

```javascript
var customer = {
  firstName: "John",
  lastName: "Doe",
  greetCustomer: function(){
    console.log("Hello again " + this.firstName + " " + this.lastName + "!");
  },
  calculateAge: function(currentYear, birthDate){
       console.log(this.firstName + " is " + (currentYear - birthDate) + " years old.");
  }
}

customer.calculateAge(2016, 1956); // Prints "John is 60 years old."
```

We have created a new method `calculateAge`, which takes two arguments `currentYear` and `birthDate`.



Now we would like to borrow this method `calculateAge` from `customer` and set the keyword `this` to refer to a new object `customer2`: 

```javascript
var customer2 = {
  firstName: "Jane",
  lastName: "Smith"
}

customer.calculateAge.call(customer2, 2016, 1984); // Prints "Jane is 32 years old"
customer.calculateAge.apply(customer2, [2016, 1984]); // Prints "Jane is 32 years old"
```

[→ Try it yourself](https://jsbin.com/zakawamada/edit?js,console) 



Both solutions are working, the only difference will be : 

\- Using the `call`  function , we  pass arguments separated by **a coma**, 

\- Using the `apply` function, we pass arguments as **values in an array** 



#### In brief:

- `call()`  and `apply()`  allow you to indirectly invoke a function as if it were a method of some other object.  With their help, you can *borrow* functions from other objects.
- `call()`  and `apply()` are methods that **all** functions have (remember that functions are objects and therefore have methods).
- The first argument to both `call()` and `apply()` is the object on which the function is to be invoked; this argument becomes **the value of the `this`  keyword** within the body of the function.

-------
*Léna Faure - 11/26/2016 - Career Path 3: Modern Front-End Developer*

