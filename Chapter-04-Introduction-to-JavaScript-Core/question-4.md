# Access individual characters in a string

When working with strings in JavaScript, you sometimes will need to access an individual character of a string.

Let's say for example that you want to order a list of strings alphabetically and need to access the first character of each string to implement it. There are two ways you can do it: 

- The bracket notation `[]`
- The `charAt` built-in method



### The `charAt`  method

```javascript
var myName = "Anna";
console.log(myName.charAt(0)); // Prints "A"
```

The `charAt` method returns the character at the specified index in the string. So the index of the first character is 0, the second character is 1, and so on. 



### The bracket notation `[]`

```javascript
var myName = "Anna";
console.log(myName[0]); // Prints "A"
```

The bracket notation treats the string as an array, where each index corresponds to an individual character.

You can only *access* characters with the square brackets notation, trying to *modify* or *delete* a character this way will not work.



### Which one should I use ?

Some older browsers don't support the array-like access to characters via square brackets notation, therefore it is a better idea to use the `charAt` method in your code.

------

*Léna Faure - 12/01/2016 - Career Path 3: Modern Front-End Developer*

