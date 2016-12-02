# Understanding By Value and By Reference

![Twins](img/sf5q7ljjf58-katerina-pavlickova.jpg)

JavaScript is an object-oriented language : this means that most things in JavaScript are objects. For example, functions are objects. But there are exceptions to this rule : the **Primitive Data Types** *string*, *number*, *boolean*, *null* and *undefined*, which are immutable (once created they cannot be modified).



One of the difference between the two is that **Primitive Types are passed By Value and Objects are passed By Reference**.



What does this mean ? You can think of it this way : 

- **By Value means creating a COPY of the original**. Picture it like twins: they are born exactly the same, but the first twin doesn't lose a leg when the second twin loses his in the war.
- **By Reference means creating an ALIAS to the original**. When your Mom calls you "Pumpkin Pie" although your name is Margaret, this doesn't suddenly give birth to a clone of yourself : you are still one, but you can be called by these two very different names.

Let's examine how Primitives and Objects behave, first when we assign them values with the assignment operator (=), and second when we pass them to a function as a parameter.



### 1. Assigning a value with the = operator to Primitives an Objects



##### With Primitive Data Types, **=operator works by value**

Consider the following code:

```javascript
var name = "Carlos";
var firstName = name;
name = "Carla";

console.log(name); // "Carla"
console.log(firstName); // "Carlos"
```

→ [Try it yourself](https://jsbin.com/naloqarajo/edit?js,console)



The result is pretty straightforward : that's the **= operator** working **by value**. What really happens here can be simplified as follows : 

- A variable `name` is created and given the value of "Carlos". JavaScript allocates a memory spot for it.
- A variable `firstName` is created and is given a *copy* of `name`'s value. `firstName` has its own memory spot and is independent of `name` . At this moment in the code, `firstName` also has a value of "Carlos".
- We then change the value of `name` to "Carla". But `firstName` still holds its original value, because it lives in a different memory spot.

When working with primitives, the =operator creates a copy of the original variable. That's what **by value means.**



##### With Objects, **=operator works by reference**

Consider the following code :

```javascript
var myName = {
  firstName: "Carlos"
};
var identity = myName;
myName.firstName = "Carla";

console.log(myName.firstName); // "Carla"
console.log(identity.firstName); // "Carla"
```

→ [Try it yourself](https://jsbin.com/zozaxayexi/1/edit?js,console)



Here the output is the same for the variables containing objects. That happens because, when dealing with **objects**, the =operator works **by reference**. What is really happening can be described as follows:

- A variable `myName` is created and is given the value of an **object** which has a property called `firstName`.  `firstName` has the value of "Carlos". JavaScript allocates a memory spot for `myName` and the object it contains.
- A variable `identity` is created and is **pointed to** `myName`. *There is no dedicated memory space to `identity`'s value'*.  It only *points to* `myName`'s value.
- We change the value of `myName`'s `firstName` property to "Carla" instead of "Carlos".



When we log `myName.firstName` it displays the new value, which is pretty straightforward. But when we log `identity.firstName` its *also* displays `myName.firstName`'s new value "Carla". This happens because `identity.firstName` only *points to* `myName.firstName` place in the memory.

When working with objects, the =operator creates an alias to the original object, it doesn't create a new object. That's what **by reference means.**



### 2. Passing Primitives and Objects to a function



##### Primitive Data Types are passed to a function by value

If you change the value of a Primitive Data Type inside a function, this change won't affect the variable in the outer scope:

```javascript
var myName = "Carlos";
function myNameIs(aName){
  aName = "Carla";
}
myNameIs(myName);

console.log(myName); // "Carlos"
```

→ [Try it yourself](https://jsbin.com/jiciyunaso/edit?js,console)

Even if we are changing the `myName` variable inside of the function `myNameIs`, when we print it after calling the function it still has the value "Carlos". That is beacsue when primitive types are passed, they are passed **by value**.

We are passing a copy of `myName`: anything you do to `myName` inside the body of the function won't affect the `myName` variable in the global scope because you are passing a copy of `myName`, and not the original `myName` variable.



##### Objects are passed to a function by reference

When you are passing something by reference, you are passing something that points to something else, not a copy of the object. So since JavaScript passes objects by reference, when you change a property of that object within the function, the change will be reflected in the outer scope:

```javascript
var myName = {};
function myNameIs(aName){
  aName.firstName = "Carla";
}
myNameIs(myName);

console.log(myName); // Object {firstName: "Carla"}
```

→ [Try it yourself](https://jsbin.com/mubahowate/edit?js,console)

Now if I log the `myName` variable after having invoked the function `myNameIs`, it logs an object with a key of `firstName` with a value equal to "Carla". The object did change in the global scope when we passed it to the function.

This is because when you pass an object into the function, you are not passing a copy you are passing something that points to the `myName` object. So when you change a property of that object in the function you are changing the property of the object in the outer scope.



But there is a subtlety you should be aware of: 

```javascript
var myName = {
  firstName: "Carla"
};

function myNameIs(aName){
  aName = {
    nickName: "Carlita"
  };
}

myNameIs(myName);
console.log(myName); // Object {firstName: "Carla"}
```

→ [Try it yourself](https://jsbin.com/zifayokotu/edit?js,console)

Here it prints the value of the variable `myName` in the outer scope and didn't add a  `nickName` property to the object this time. Why is that ? If you look carefully, what we are doing in the function is trying to reassign the `myName` object a new value.  

But you can't change what `myName` *points to*, you can only change a *property* of `myName` to something else, like this:

```javascript
var myName = {
  firstName: "Carla"
};

function myNameIs(aName){
  aName.nickName = "Carlita";
}

myNameIs(myName);
console.log(myName); // Object {firstName: "Carla", nickName: "Carlita"}
```



------

*Léna Faure - 11/29/2016 - Career Path 3: Modern Front-End Developer*
