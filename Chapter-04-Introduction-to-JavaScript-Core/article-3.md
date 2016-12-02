# The `this` Keyword In-Depth

In JavaScript, the `this` keyword has the same use as when we use it in a plain sentence : it refers to something that we already introduced earlier.

If I say : "I met Gary Vaynerchuck yesterday at a web convention. This guy is amazing.", you immediately understand that "this guy" refers to Gary Vaynerchuck.

In a similar way, when used in JavaScript, **`this` refers to an object, and more specifically to the object that invokes the code.** 



Consider this example:

```javascript
var customer = {
  firstName: "John",
  lastName: "Doe",
  greetCustomer: function(){
    // We use "this" instead of repeating "customer"
    console.log("Hello again " + this.firstName + " " + this.lastName + "!");
  }
}

customer.greetCustomer(); // Prints "Hello again John Doe!" 
```

→ [Try it yourself](https://jsbin.com/hahoxopiru/edit?js,console) 



We created a `customer`  object with properties and a `greetCustomer` method. The method uses the keyword `this`  to refer to the `customer` object. 

Note that `this` is able to access the properties of the object (`firstName` and `lastName`) : it is because not only `this` *refers* to the `customer` object , but it also *contains* the value of the object : it can therefore access the methods and properties of the object.

When the `greetCustomer` method is invoked, `this` inside the method automatically refers to the object that invoked the method : the `customer  ` object . But before the method was called, and this is very important to grasp, *`this` was not assigned a value*. 

What does it mean ?



Take this function for example: 

```javascript
var sayCatName = function(catName){
  	console.log("My cat's name is " + catName);
};
```

What is it going to print ? Well, for the moment, not much, we have *defined*  the function but we don't know the value of `catName` until we *invoke* the function and pass it a cat's name :

```javascript
sayCatName('Alphonse'); // Prints "My cat's name is Alphonse"
```

And that's the exact same idea behind the `this` keyword : we won't know what it refers to until an object invokes a function where `this`  is defined. In other words, **the `this`  keyword is not assigned a value until a function where it is defined is actually called.**

So whenever you are trying to figure out what the `this` keyword refers to, ask yourself the question: *"Where is this function invoked ?"*.  Not where it is **defined**, but where it is **called**.



This was a basic example, and there will be times when it will be more difficult to determine what `this ` refers to in our code. Let's now dive into the four rules that will allow us to  do so :  

- **Implicit Binding** (When `this`  is inside of a declared object)

- **Explicit Binding** (When we explicitly set the value of  `this`  using `call`, `apply` or `bind`) 

- **The global context** (When `this`  is outside of a declared object)

- **The `new` keyword** (When creating a new object)

  ​

### 1. Implicit Binding (When `this` is inside a declared object)

When the keyword `this` is found inside of a declared object, the value of `this` will always be the **closest parent object**.



Consider our customer object with a new method `whatIsMyObject`: 

```javascript
var customer = {
  firstName: "John",
  lastName: "Doe",
  greetCustomer: function(){
    console.log("Hello again " + this.firstName + " " + this.lastName + "!"); 
  },
  whatIsMyObject: function() {
    console.log(this);
  }
}

customer.greetCustomer(); // Prints "Hello again John Doe!" 
customer.whatIsMyObject(); // Prints the object "customer" in the console
```

[→ Try it yourself](https://jsbin.com/jimiyehaju/edit?js,console) 

We find the keyword `this`  first Inside of the function `greetCustomer`. The closest parent object is the `customer` object, which means that the value of the `this` is the `customer`object.

When we print `this` alone inside the function `whatIsMyObject`, whose closest parent is also the `customer`object, we get the whole object printed in the console, so we are now sure that the value of `this` is the `customer` object. 



This is all well, but what happens when we have a nested object ?

```javascript
var customer = {
  firstName: "John",
  lastName: "Doe",
  greetCustomer: function(){
    console.log("Hello again " + this.firstName + " " + this.lastName + "!"); 
  },
  whatIsMyObject: function() {
    console.log(this);
  },
  address: {
    askAddress: function(){
      prompt("Dear " + this.firstName + ", please enter your address");
    },
    whatIsMyObject: function() {
    	console.log(this);
  	}
  }
}

customer.address.askAddress(); // Prints "Dear undefined, please enter your address"
customer.address.whatIsMyObject(); // Prints the object "address" in the console
```

[→ Try it yourself](https://jsbin.com/zecivoneya/edit?js,console) 

We added a key to the `customer ` object called `address`. The value of `address` is another object. Inside this object, we have an `askAddress` function that uses `this` to call the customer by his name.

However, `this` seems to be unable to access the property `name` of the `customer` object and prints "Dear undefined, , please enter your address". Have you guessed why ?

Well let's go back to our rule that says that the value of `this` will always be the closest parent object : here we are in the `customer`  object, but also inside the nested `address` object, which is in fact the closest parent to `this` when it it inside the function `askAdress`.

Since the `address` object has no property `firstName`, `this.firstName` is undefined. We can also see that the `whatIsMyObject` function inside the `address` object doesn't refer to `customer` anymore but to `address`.

 

So how can we fix this ? What if we need our `askAddress` method to return the name of the customer ? We will need to explicitely change the value of `this`  with the `call`,  `apply` or `bind ` methods.



### 2. Explicit Binding (When we explicitly set the value of  `this`  using `call`, `apply` or `bind`)

Functions in JavaScript are object, and as objects they have several methods. We use the  `call`,  `apply` and `bind ` methods of a function when we want full control over what the keyword `this` refers to.



Let's take the code we used in the previous example:

```javascript
var customer = {
  firstName: "John",
  lastName: "Doe",
  greetCustomer: function(){
    console.log("Hello again " + this.firstName + " " + this.lastName + "!");
  },
  whatIsMyObject: function() {
    console.log(this);
  },
  address: {
    askAddress: function(){
      prompt("Dear " + this.firstName + ", please enter your address");
    },
    whatIsMyObject: function() {
    	console.log(this);
  	}
  }
}

customer.address.askAddress(); // Prints "Dear undefined, please enter your address"
```

Previsoulsy, we were calling `customer.address.askAddress`, which was returning *"Dear undefined…"* , because the value of `this` inside the `askAddress` function refers to the `address` object, which does not have a `firstName` property.



##### `call()`

By using the `call`method, we can change what we want the value of `this  ` to be : here we are going to change the value of `this` to be the `customer`object: 

```javascript
customer.address.askAddress.call(customer); // Prints "Dear John, please enter your address"
```

[→ Try it yourself](https://jsbin.com/nireruvoca/edit?js,console) 

It worked ! Note that we are not *invoking* `askAddress` , we just *attach* the method `call` onto it, so there are no parentheses after the `askAddress` method when we use `call`.

The parameter to the `call` method is what we want the keyword `this` to refer to, in this case the `customer` object.



##### `apply()`

The difference between `call` and `apply` is when we have arguments to the function we want to use `call ` or `apply` on.

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



Now we would like to borrow this method `calculateAge` from customer and set the keyword `this` to refer to a new object `customer2`: 

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



##### `bind()`

`bind` works just like `call`, but instead of *calling* the function right away, it returns a function *definition*, with `this` set to the first parameter passed to `bind()`.

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

var customer3 = {
  firstName: "Robert",
  lastName: "Mills"
}

// bind returns a new ageOfCustomer3 with a 'this' that now refers to obj customer3
var ageOfCustomer3 = customer.calculateAge.bind(customer3);
ageOfCustomer3(2016, 1978); // Prints Robert is 38 years old."

```

[→ Try it yourself](https://jsbin.com/nuyajizuyu/edit?js,console) 



`bind` allows us to explicitely set `this` to the object we want. It generates a copy of the `calculateAge` function that we stored in the variable  `ageOfCustomer3`, but it doesn't immediately *call* the function.

We can call the function later on in the code and pass it the arguments it needs to execute. It can be extremely useful to create functions with preset arguments that wil be called later, with `this` already set to the right object.



### 3. The global context (When `this` is outside of a declared object)

`this` is in the global context when you find it "into the wild", outside of a declared object (meaning that there has not been an object defined which contains the keyword `this`).

In this case,  **its value refers to the global object**, which, in the browser, corresponds to the `window` object.

And in fact, every variable declared in the global scope is actually attached to the window object :

```javascript
var cat = "Alphonse";
// The variable cat is actually attached to the global window object : 
console.log(window.cat === cat); // Prints true

// "this" in the global context has the value of the global window object
console.log(this); // Prints the window object
```

[→ Try it yourself](https://jsbin.com/qiyizipujo/edit?js,console) 



What if we find `this` **inside a regular function call** (not an object or an object's method) ? In that case, **the `this` keyword also points at the global object**. 

```javascript
var customer = {
  firstName: "John",
  lastName: "Doe",
  greetCustomer: function(){
    console.log("Hello again " + this.firstName + " " + this.lastName + "!");
   
    function nestedFunction(){
      	console.log(this);
    }
 	nestedFunction();
  }
}

customer.greetCustomer(); // Prints the window object
```

[→ Try it yourself](https://jsbin.com/zubixiluto/edit?js,console) 

Here when I call the `greetCustomer` method, the `nestedFunction` will be invoked, and prints the global  window object, even though we are inside the `customer` object. Why is that ?

What we have here is a regular function call inside a method, not a method of the object. Although it is written inside of the method, it is still a simple function. 

 

### 4. The `new` keyword

One way of creating an object is by using the `new ` keyword.

Consider this function Customer that defines an object:

```javascript
function Customer(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}
```

Inside of this function, we are defining the properties of the object with the keyword `this`,  and they will be equal to the value passed to the function.

Hold on here, we are inside of a function; shouldn't the value of `this` be the global object, as we saw earlier ?

But in this case, we are not going to make a regular function call to create a new object. Instead, we are going to use the `new` keyword, and the rule is that the value of `this` changes when you use the `new ` keyword.

```javascript
var customer1 = new Customer("John", "Smith");

console.log(customer1.firstName + " " + customer1.lastName); // Prints "John Smith"
```

[→ Try it yourself](https://jsbin.com/menitozemi/edit?js,console) 

 `this` now refers to the object that is created when the `new` keyword is used. We are storing that object in a variable called `customer1` , that can now access the `firstName` and `lastName` properties.



### Conclusion

To know what is the value of `this` inside of your code, always ask yourself the question: 

> Where is the function **invoked** ?



There are some very tricky parts to `this` that are reviewed in great detail in [this article](http://javascriptissexy.com/understand-javascripts-this-with-clarity-and-master-it/) : 

> *The `this` keyword is most misunderstood when we borrow a method that uses `this`, when we assign a method that uses `this`  to a variable, when a function that uses  `this`  is passed as a callback function, and when `this`  is used inside a closure - an inner function.* 

These are some real-world problems that you will surely encounter often while using native JavaScript of libraries like JQuery, and an understanding of the basic workings of `this`  will be of great help in those cases.

---------------
*Léna Faure - 11/25/2016 - Career Path 3: Modern Front-End Developer*
