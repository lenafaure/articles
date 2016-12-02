# JavaScript Hoisting In-depth



*Hoisting* in JavaScript means that variable and function declarations are moved to the top of their containing scope.

What does that translate to in my code? 

![Declarations are moved to the top](img/hoisting.jpeg)



### The Declaration Comes Before the Assignment

Let's consider the following snippet of code to understand how hoisting works : 

```javaScript
rockStar = "Mick Jagger";
var rockStar;

console.log(rockStar); // Prints "Mick Jagger"
```

Maybe you expected the console.log to print `undefined`, since the `var` statement (the declaration ) comes after  `rockStar = "Mick Jagger"` (the assignment ). We assigned a value to the `rockStar` variable *before* it was created in the code. 

After all it is logical to think that all the lines of code written in a JavaScript program are read, interpreted, and executed from top to bottom. There is a truth to that assumption, but the behaviour we just demonstrated  tells us otherwise.

In reality, just before the code is executed, it goes through a compiler : during this phase called *lexing*, scope gets defined and the **variable and function declarations are moved to the top of their scope**. If they are defined inside a function, they are moved to the top of this function, and if they are outside a function, to the top of the global scope.



Consider another piece of code : 

```javaScript
console.log(rockStar); 	// Prints "undefined"
var rockStar = "Mick Jagger";
```

This time, although we have the `rockStar` variable declared and assigned, we get an `undefined` response when we try to print the variable. Why is that ?

The best way to get your head around it is to remember that **all declarations are processed first**, and this is equally true for variables and functions. Let's decode what it means by reading the code just as JavaScript does it.

When you see `var rockStar = "Mick Jagger"`, you see it as one statement, but JavaScript sees it otherwise. It actually interprets it as two distinct statements:

```javaScript
var rockStar;
rockStar = "Mick Jagger";
```

In our example, JavaScript first interprets the code, sees that the `rockStar` variable exists and moves it to the top of the scope: this is what is called *hoisting*. 

But the second statement, the assignment, is *left in place*. Then, the code is executed by JavaScript as if it were written like this: 

```javaScript
var rockStar;
console.log(rockStar);
rockStar = "Mick Jagger";
```



In other words, **the declaration comes before the assignment** : only variable an function declarations are hoisted, while the assignements are left in place, just wher they were written in the code.



### Function Declarations Are Hoisted, but Function Expressions Are Not

We have seen examples of variable hoisting, now let's focus on the behavior of functions .

See the following piece of code : 

```javaScript
movieStar();
function movieStar() {
  var name = "Meryl Streep";
  console.log(name);
}
```



From what we understand now of hoisting, how can we rewrite this code as if we were JavaScript interpreting it ?

First recall that **hoisting is per-scope**, and that here the scope of the `name`  variable is the *local scope* inside the function `movieStar` . The variable declaration wil then be hoisted to the top *of the function, not the global scope*.

However, we also *declared* the `movieStar` function in the *global scope*, and hoisting works the same for variable and function declarations : our function declaration will be moved to the top, before the *execution* of the function.

The code will be interpreted by JavaScript as follows:

```javaScript
function movieStar() { // function declaration is moved to the top of the global scope
  var name; // variable declaration is moved to the top of the local scope 
  name = "Meryl Streep"; // variable assignement is left in place
  console.log(name);
}
movieStar(); // Prints "Meryl Streep"
```

In this example, the `movieStar` function call works because the function declaration is hoisted, and interpreted as being declared *before* it was called.



Let's take another example :

```javaScript
movieStar(); // Prints "TypeError : movieStar is not a function"

var movieStar = function () {
	// This is a function expression and therefore not hoisted
	var name = "Philip Seymour Hoffman";
  	console.log(name); 
}
```

What happened here ? The function I assigned to the variable `movieStar`, a process that is called *function expression*, is not hoisted this time : when I call the function, JavaScript throws a TypeError.

That is because **functions that are assigned to variables are not hoisted.**

As a demonstration,  if I now write the code as follows it is now printing the expected result : 

```javaScript
var movieStar = function () {
	var name = "Philip Seymour Hoffman";
  	console.log(name); 
}

movieStar(); // Prints "Philipp Seymour Hoffman"
```

 

### Functions Before Variables

We now know that both function declarations  and variable declarations are hoisted. But there is another thing to be ware of : **functions are hoisted first, and then variables.**



Consider this code where a variable and a function are declared with the same name: 

```javaScript
var businessStar;
function businessStar(){
  console.log("Gary Vaynerchuck");
}

console.log(businessStar); // Prints "function businessStar(){ console.log("Gary Vaynerchuck");}"
```

The function has been hoisted before the variable, so when I print the value of `businessStar` it outputs the function itself.



And another example where the variable is assigned a value : 

```javaScript
var businessStar = "Gary Vaynerchuck";
function businessStar(){
  console.log("Gary Vaynerchuck");
}

console.log(businessStar); // Prints "Gary Vaynerchuck"
```

This time the output is the value of the variable, because the variable has been assigned a value, which overrides the function declaration. If you now try to call the function `businessStar()`, you will get a TypeError: `businessStar` has been assigned the string "Gary Vaynerchuck" and is no longer a function.



### Conclusion

Hoisting is Javascript's default behaviour of moving declarations to the top. Rather than being available right after their declaration, variables and functions might actually be available beforehand in the code, which might lead to certain behaviours that you wouldn't expect if you knew nothing about hoisting.

That is why as a rule, it is important to make sure, as often as you can, that:

- You declare all of your variables at the top of their scope (at the top of the global scope or at the top of the function scope)
- You make sure you put all your functions, if you can, also at the top of their scope.

---------------
*Léna Faure - 11/18/2016 - Career Path 3: Modern Front-End Developer*
