https://fireship.io/lessons/typescript-design-patterns/

# Design Patterns

## Singleton

Share a single global instance throughout our application
https://javascriptpatterns.vercel.app/patterns/design-patterns/singleton-pattern

```js
/* Method1 - using js objects:
ES2015 Modules are singletons by default. We no longer need to explicitly create singletons to achieve this global, non-modifiable behavior.*/
// counter-singleton.js:
let counter = 0;

export default Object.freeze({
  getCount: () => counter,
  increment: () => ++counter,
  decrement: () => --counter,
});
```

```js
// method2 - using js es6 class:
// counter-singleton.js:
let instance;

class Counter {
  constructor() {
    if (instance) return instance;

    this.counter = 0;
    instance = this;
  }

  getCount() {
    return this.counter;
  }

  increment() {
    return ++this.counter;
  }

  decrement() {
    return --this.counter;
  }
}
const singletonCounter = Object.freeze(new Counter());
export default singletonCounter;
```

## Optional Parameters + Builder

```js
class User {
  constructor(name, age = 18, address = "") {
    this.name = name;
    this.age = age;
    this.address = address;
  }
}
class UserBuilder {
  constructor(name) {
    this.user = new User(name);
  }
  setAge(age) {
    this.user.age = age;
    return this;
  }
  setPhone(phone) {
    this.user.phone = phone;
    return this;
  }
  build() {
    return this.user;
  }
}
const user = new UserBuilder("Seyyed").setAge(32).build();
```

## Facade

Provide a higher-level interface that abstracts and simplifies the usage of the underlying system. As you can see `getFetch` is a facade for fetching data from internet, and we can freely use it everywhere and when ever we decide to change it's implementation e.g. change `browser's fetch` to `axios`, we just need to update `getFetch`'s implementation, there will be no need to change other parts of the code!
https://github.com/WebDevSimplified/Design-Patterns/blob/master/Facade%20Pattern/after.js

```js
function getUsers() {
  return getFetch("https://jsonplaceholder.typicode.com/users");
}

function getUserPosts(userId) {
  return getFetch("https://jsonplaceholder.typicode.com/posts", {
    userId: userId,
  });
}

getUsers().then((users) => {
  users.forEach((user) => {
    getUserPosts(user.id).then((posts) => {
      console.log(user.name);
      console.log(posts.length);
    });
  });
});

// function getFetch(url, params = {}) {
//   const queryString = Object.entries(params).map(param => {
//     return `${param[0]}=${param[1]}`
//   }).join('&')
//   return fetch(`${url}?${queryString}`, {
//     method: "GET",
//     headers: { "Content-Type": "application/json" }
//   }).then(res => res.json())
// }

function getFetch(url, params = {}) {
  return axios({
    url: url,
    method: "GET",
    params: params,
  }).then((res) => res.data);
}
```

# SOLID Principle

# Single Responsibility Principle

The SRP states that a class should have only one reason to change. It promotes keeping each class focused on a single responsibility(based on it's type):

1. Violation: User Service class with multiple responsibilities:

````javascript
class UserService {
  getUser(id) {
    // Code to fetch user from the database
  }

  saveUser(user) {
    // Code to save user to the database
  }

  sendEmail(user, message) {
    // Code to send an email to the user
  }
}```
In this example, the `UserService` class has multiple responsibilities: fetching users, saving users, and sending emails. This violates the SRP because the class has more than one reason to change(_The type is User Service, so it's job has to include only services about User. So sending email is not a service restricted for User Type, although it is related to user!_).

Solution:
One solution is to separate the responsibilities into individual classes:

```javascript
class UserService {
  getUser(id) {
    // Code to fetch user from the database
  }

  saveUser(user) {
    // Code to save user to the database
  }
}

class EmailService {
  sendEmail(user, message) {
    // Code to send an email to the user
  }
}```
By separating the responsibilities, the `UserService` now focuses solely on user-related operations, while the `EmailService` is responsible for sending emails. Each class has a single responsibility, adhering to the SRP(_The type is Log, so we need to extract email and database from Logger_).

2. Violation: Logger class with multiple responsibilities:
```javascript
class Logger {
  log(message) {
    // Code to log the message to a file
  }

  sendEmailAlert(message) {
    // Code to send an email alert
  }

  saveToDatabase(message) {
    // Code to save the log to a database
  }
}```
In this example, the Logger class is responsible for logging messages, sending email alerts, and saving logs to a database, violating the SRP.

Solution:
A possible solution is to separate the responsibilities into distinct classes:

```javascript
class FileLogger {
  log(message) {
    // Code to log the message to a file
  }
}

class EmailAlert {
  sendEmailAlert(message) {
    // Code to send an email alert
  }
}

class DatabaseLogger {
  saveToDatabase(message) {
    // Code to save the log to a database
  }
}```
By creating separate classes for each responsibility, such as `FileLogger`, `EmailAlert`, and `DatabaseLogger`, we adhere to the SRP. Each class now has a single responsibility and can be modified independently if needed.

Splitting responsibilities into separate classes not only adheres to the SRP but also improves code organization, maintainability, and reusability. Each class can focus on a specific task, making the code easier to understand and modify.


# Open/Closed Principle (OCP):
The OCP states that software entities (classes, modules, functions) should be open for extension but closed for modification. It encourages designing code that can be easily extended without modifying existing code.
```js
class Shape {
  // Common shape properties and methods
  constructor() {
    this.color = 'black';
  }

  area() {
    throw new Error('Method not implemented');
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  area() {
    return this.width * this.height;
  }
}

class Circle extends Shape {
  constructor(radius) {
    super();
    this.radius = radius;
  }

  area() {
    return Math.PI * Math.pow(this.radius, 2);
  }
}
````

In this example, the `Shape` class is open for extension by allowing new shapes (like `Rectangle` and `Circle`) to be added without modifying the `Shape` class itself. Each shape subclass overrides the `area()` method to provide its specific implementation.

Here's a complete example in JavaScript illustrating the violation of the Open/Closed Principle (OCP) and its solution:

1. Violation: Modifying Existing Code Directly

```javascript
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  calculateArea() {
    return this.width * this.height;
  }
}
```

If we need to introduce a new shape, such as a circle, directly modifying the existing `Rectangle` class violates the OCP.

Solution:

```javascript
class Shape {
  calculateArea() {
    throw new Error("calculateArea() method must be implemented.");
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  calculateArea() {
    return this.width * this.height;
  }
}

class Circle extends Shape {
  constructor(radius) {
    super();
    this.radius = radius;
  }

  calculateArea() {
    return Math.PI * this.radius * this.radius;
  }
}

// Usage
const rectangle = new Rectangle(5, 10);
console.log(rectangle.calculateArea()); // Output: 50

const circle = new Circle(7);
console.log(circle.calculateArea()); // Output: 153.93804002589985
```

By introducing an abstract `Shape` class and having specific shapes like `Rectangle` and `Circle` inherit from it, we can add new shapes without modifying existing code. The `calculateArea()` method is implemented differently in each shape based on their specific logic.

This solution allows for extending the code with new shapes by creating additional classes that inherit from the `Shape` abstract class and implement the `calculateArea()` method accordingly. The existing code remains unchanged, adhering to the Open/Closed Principle (OCP).

## Liskov Substitution Principle (LSP):

The LSP states that objects of a superclass should be replaceable with objects of its subclasses without affecting the correctness of the program. It ensures that inheritance is used correctly and doesn't introduce unexpected behavior.

1. violation of LSP:

```js
class Bird {
  fly() {
    console.log("Flying...");
  }
}

class Penguin extends Bird {
  fly() {
    throw new Error("Penguins can't fly");
  }
}

function makeBirdFly(bird) {
  bird.fly();
}

const bird = new Bird();
const penguin = new Penguin();

makeBirdFly(bird); // Outputs: Flying...
makeBirdFly(penguin); // Throws an error: Penguins can't fly
```

In this example, the `Bird` class has a `fly()` method that allows it to fly. However, the `Penguin` class, which is a subclass of `Bird`, overrides the `fly()` method with an exception since penguins cannot fly. Despite being a subclass, the `Penguin` object cannot be substituted for a `Bird` object in all situations, highlighting a violation of the LSP.

## Interface Segregation Principle (ISP):

The ISP states that clients should not be forced to depend on interfaces they don't use. It promotes designing smaller, focused interfaces instead of large, monolithic interfaces.

```js
class Printer {
  print() {
    throw new Error("Method not implemented");
  }

  scan() {
    throw new Error("Method not implemented");
  }
}

class InkjetPrinter extends Printer {
  print() {
    // Code to perform printing
  }
}

class LaserPrinter extends Printer {
  print() {
    // Code to perform printing
  }

  scan() {
    // Code to perform scanning
  }
}
```

In this example, the `Printer` class defines both `print()` and `scan()` methods. However, the `InkjetPrinter` subclass only provides an implementation for the `print()` method, while the `LaserPrinter` subclass implements both `print()` and `scan()`. This way, clients can depend on specific interfaces (`InkjetPrinter` or `LaserPrinter`) without being forced to implement methods they don't need.

## Dependency Inversion Principle (DIP):

The DIP states that high-level modules should not depend on low-level modules. Instead, both should depend on abstractions. It promotes loose coupling and the use of dependency injection.

```js
class UserRepository {
  // Code to interact with the user data storage
}

class UserService {
  constructor(userRepository) {
    this.userRepository = userRepository;
  }

  getUser(id) {
    return this.userRepository.getUser(id);
  }

  saveUser(user) {
    return this.userRepository.saveUser(user);
  }
```

In this example, the `UserService` depends on the `UserRepository` interface rather than a specific implementation. This allows for flexibility and easier testing. The concrete implementation of `UserRepository` can be injected into `UserService` at runtime.

These examples demonstrate how the SOLID principles can be applied in JavaScript code. By following these principles, code becomes more modular, maintainable, and flexible, making it easier to extend and adapt to changing requirements.

# Functional Programming Concepts

https://observablehq.com/collection/@anjana/functional-javascript-first-steps
https://www.youtube.com/watch?v=BMUiFMZr7vk&list=PL0zVEGEvSaeEd9hlmCXrk5yUyqUag-n84
https://www.youtube.com/watch?v=nnwD5Lwwqdo
