# Classes in ES6

JavaScript is a prototype-based language, and every object in JavaScript has a hidden internal property called `[[Prototype]]` that can be used to extend object properties and methods.

Until recently, developers used constructor functions to mimic an object-oriented design pattern in JavaScript. The language specification ECMAScript 2015 introduced classes to the JavaScript language. Classes in JavaScript do not actually offer additional functionality, and are often described as providing "[syntactical sugar](https://en.wikipedia.org/wiki/Syntactic_sugar)" over prototypes and inheritance in that they offer a cleaner and more elegant syntax. Because other programming languages use classes, the class syntax in JavaScript makes it more straightforward for developers to move between languages.

Both classes and constructors imitate an object-oriented inheritance model to JavaScript, which is a prototype-based inheritance language. Being familiar with classes is extremely helpful, as popular JavaScript libraries make frequent use of the `class` syntax. 

 ```javascript
// Initializing a function with a function expression
const x = function() {}

// Initializing a class with a class expression
const y = class {}
 ```

## Defining a class

Classes are not hoisted like function declaration. The reason for this limitation is because of the ability to extend the class into a new object. It must be evaluated in the proper “location”.

 Also, all code inside the class construct is automatically in strict mode.

### Constructors

The `constructor` method is a special method for creating and initializing an object created using classes. This shouldn't confuse you with the constructor pattern that developers used to follow before the introduction of classes. 

```javascript
// Initializing a constructor function
function Person(name, age) {
    this.name = name;
    this.age = age;
}

// Initializing a class definition
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
}
```

Developers used to follow the convention of capitailzing the first character in the constructor function. Now the class function does exactly the same as the constructor pattern except it uses the `constructor` function.

A `constructor` can use the function `super` to call the constructor of the super class. More details about that in the *Extending class* section.

Note that classes have a default `constructor() {}`. If there’s no `constructor` in the `class` construct, then an empty function is generated, same as if we had written `constructor() {}`. Not just that, it will also call the `super` by default. ESLint have a rule to [disallow unnecessary contstructor](https://eslint.org/docs/rules/no-useless-constructor) `no-useless-constructor` which commonly confuses beginners to React:

```javascript
import React, { Component } from 'react';

class SomeComponent extends Component {
    constructor(props) {
		super(props);
    }
}
```

### Prototype Methods

Method are added directly to the class body like the following

 ```javascript
// Initializing a constructor definition
function Person(name, age) {
    this.name = name;
    this.age = age;
}
// Adding a method to the constructor
Person.prototype.greet = function() {
    return `${this.name} says hello.`;
}

// Initializing a class definition
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    // Adding a method to the constructor
    greet() {
        return `${this.name} says hello.`;
    }
}
 ```

Class methods are non-enumerable. A class definition sets `enumerable` flag to `false` for all methods in the `"prototype"`. That’s good, because if we `for..in` over an object, we usually don’t want its class methods.

### Static methods

Static methods are methods that are called without instantiating their class and cannot be called through a class instance. They are meant to provide class-specific methods for object-oriented programming in Javascript.

One difference between static methods and class methods is that the static method doesn't go in the prototype chain. Also another difference is the reference of `this` keyword. In the static method, `this` keyword will refer to the class itself.

Static methods arre used to create functions that belongs to the class and not the instance. For example, in our `Person` class we can create a static method `getOldest` that can take an array of instances and return the oldest person in the list.

```javascript
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    static getOldest(people) {
    	return people.reduce((previous, current) => {
    		return (current.age > previous.age) ? current : previous;
		});
    }
}
const people = [
    new Person("John", 24),
    new Person("Peter", 31),
    new Person("Will", 25),
];
Person.getOldest(people); // Person {name: "Peter", age: 31}
```

### Getters and Setters

Classes may include getters/setters. Here’s an example with `user.name` implemented using them:

```javascript
// Initializing a class definition
class Person {
    constructor(name, age) {
        // invokes the setter
        this.name = name;
        this.age = age;
    }
    get name() {
    	return this._name;
  	}
  	set name(value) {
        if (value.length < 4) {
        	alert("Name is too short.");
            return;
        }
    	this._name = value;
	}
}

const john = new Person("", 24); // Name too short.
```

Getters can call anything we define not just the properties we use. For example we can define a getter method called `yearOfBirth` that calculate the year of birth of the person and return the date. We saw how to use setters in the example above to validate the paramteres and ensure that the function works as expected.

## Extending class

An advantageous feature of constructor functions and classes is that they can be extended into new object blueprints based off of the parent. This prevents repetition of code for objects that are similar but need some additional or more specific features.

New constructor functions can be created from the parent using the [`call()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) method.

```javascript
// Creating a new constructor from the parent
function Student(name, age, grade) {
    // Chain constructor with call
    Person.call(this, name, age);
    this.grade = grade;
}

const john = new Student('John', 14, '8th grade');
```

 With ES6 classes, the [`super`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/super) keyword is used in place of `call` to access the parent functions. We will use `extends` to refer to the parent class.

 ```javascript
// Creating a new class from the parent
class Student extends Person {
    constructor(name, age, grade) {
        // Chain constructor with super
        super(name, age);
        // Add a new property
        this.grade = grade;
    }
}
const john = new Student('John', 14, '8th grade');
 ```

The output is nearly exactly the same, except that in the class construction the `[[Prototype]]` is linked to the parent, in this case `Person` unlike the using the constructor which will be linked to `Student`.