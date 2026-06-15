# async / await in JavaScript (From Scratch)

This is one of the most important topics in modern JavaScript.

Before async/await, developers used:

```javascript
Callbacks
```

Then:

```javascript
Promises
```

Then JavaScript introduced:

```javascript
async / await
```

to make asynchronous code look like normal synchronous code.

---

# Why Was async/await Introduced?

Let's see a Promise example.

```javascript
function fetchUser() {
    return Promise.resolve({
        id: 1,
        name: "Narendar"
    });
}

fetchUser()
.then((user) => {
    console.log(user);
})
.catch((error) => {
    console.log(error);
});
```

This works.

But imagine:

```javascript
getUser()
.then(...)
.then(...)
.then(...)
.then(...)
.then(...)
.catch(...)
```

Large applications become difficult to read.

So JavaScript introduced:

```javascript
async
await
```

---

# What is async?

The `async` keyword is placed before a function.

Syntax:

```javascript
async function myFunction() {

}
```

Example:

```javascript
async function greet() {
    return "Hello";
}
```

---

# Important Interview Question

What does an async function always return?

Answer:

> An async function always returns a Promise.

Example:

```javascript
async function greet() {
    return "Hello";
}

console.log(greet());
```

Output:

```javascript
Promise { "Hello" }
```

Even though we returned a string.

JavaScript automatically converts it into:

```javascript
Promise.resolve("Hello");
```

---

# Example

```javascript
async function greet() {
    return "Welcome";
}

greet().then((data) => {
    console.log(data);
});
```

Output:

```javascript
Welcome
```

---

# Equivalent Promise Version

These two are the same:

### Version 1

```javascript
async function greet() {
    return "Hello";
}
```

### Version 2

```javascript
function greet() {
    return Promise.resolve("Hello");
}
```

---

# What is await?

`await` tells JavaScript:

> Wait until Promise completes and give me its result.

Syntax:

```javascript
let result = await promise;
```

Important:

```javascript
await
```

can only be used inside an async function.

---

# First await Example

```javascript
function getData() {

    return Promise.resolve("Data Received");

}
```

Using Promises:

```javascript
getData()
.then((data) => {
    console.log(data);
});
```

Using async/await:

```javascript
async function showData() {

    const data = await getData();

    console.log(data);

}

showData();
```

Output:

```javascript
Data Received
```

---

# Understanding await

Example:

```javascript
function getNumber() {
    return Promise.resolve(100);
}
```

Using await:

```javascript
async function test() {

    const number = await getNumber();

    console.log(number);

}

test();
```

Output:

```javascript
100
```

---

# Real Async Example

Simulate API call:

```javascript
function fetchUser() {

    return new Promise((resolve) => {

        setTimeout(() => {

            resolve({
                id: 1,
                name: "Narendar"
            });

        }, 3000);

    });

}
```

Using async/await:

```javascript
async function getUser() {

    console.log("Loading...");

    const user = await fetchUser();

    console.log(user);

}

getUser();
```

Output:

```javascript
Loading...

(wait 3 seconds)

{
 id:1,
 name:"Narendar"
}
```

---

# Visualization

```text
getUser()

↓

await fetchUser()

↓

Wait 3 seconds

↓

Promise Resolved

↓

Continue Execution
```

---

# Multiple await Statements

Example:

```javascript
function getUser() {

    return Promise.resolve("User");

}

function getOrders() {

    return Promise.resolve("Orders");

}
```

Using await:

```javascript
async function loadData() {

    const user = await getUser();

    console.log(user);

    const orders = await getOrders();

    console.log(orders);

}

loadData();
```

Output:

```javascript
User
Orders
```

---

# Converting Promise Chains to async/await

Promise Version:

```javascript
getUser()
.then((user) => {

    return getOrders(user.id);

})
.then((orders) => {

    return getPayment(orders[0]);

})
.then((payment) => {

    console.log(payment);

});
```

---

Async/Await Version:

```javascript
async function loadData() {

    const user = await getUser();

    const orders = await getOrders(user.id);

    const payment = await getPayment(orders[0]);

    console.log(payment);

}
```

Much cleaner.

---

# Error Handling with async/await

One of the biggest advantages.

Suppose:

```javascript
function getUser() {

    return Promise.reject("Server Error");

}
```

Handle using:

```javascript
try
catch
```

---

# Example

```javascript
async function loadUser() {

    try {

        const user = await getUser();

        console.log(user);

    }
    catch(error) {

        console.log(error);

    }

}

loadUser();
```

Output:

```javascript
Server Error
```

---

# Execution Flow

```text
await Promise

↓

Success ?
     ↓
  Yes → Continue

  No
     ↓
catch()
```

---

# Example with throw

```javascript
async function test() {

    try {

        throw new Error("Something Failed");

    }
    catch(error) {

        console.log(error.message);

    }

}

test();
```

Output:

```javascript
Something Failed
```

---

# Sequential Execution Problem

Consider:

```javascript
function task1() {

    return new Promise(resolve => {

        setTimeout(() => {
            resolve("Task 1");
        },3000);

    });

}

function task2() {

    return new Promise(resolve => {

        setTimeout(() => {
            resolve("Task 2");
        },3000);

    });

}
```

Now:

```javascript
async function execute() {

    const result1 = await task1();

    const result2 = await task2();

    console.log(result1);

    console.log(result2);

}

execute();
```

Time:

```text
Task1 → 3 sec

Task2 → 3 sec

Total = 6 sec
```

Because second task waits for first.

---

# Better Solution: Promise.all()

Run both together.

```javascript
async function execute() {

    const [result1,result2] =
        await Promise.all([
            task1(),
            task2()
        ]);

    console.log(result1);

    console.log(result2);

}
```

Now:

```text
Task1 → 3 sec

Task2 → 3 sec

Total = 3 sec
```

Huge performance improvement.

---

# Common Mistake

Wrong:

```javascript
function test() {

    const data = await fetchUser();

}
```

Output:

```javascript
SyntaxError
```

Because:

```javascript
await
```

must be inside:

```javascript
async function
```

---

# Correct

```javascript
async function test() {

    const data = await fetchUser();

}
```

---

# Real World Example

Imagine Login API.

```javascript
function login() {

    return Promise.resolve({
        username:"Narendar"
    });

}
```

Using async/await:

```javascript
async function performLogin() {

    try {

        const user = await login();

        console.log(
            "Welcome " + user.username
        );

    }
    catch(error) {

        console.log(error);

    }

}
```

Output:

```javascript
Welcome Narendar
```

---

# Interview Questions

### Q1

Output?

```javascript
async function test() {
    return 10;
}

test().then(console.log);
```

Output:

```javascript
10
```

Because async returns:

```javascript
Promise.resolve(10)
```

---

### Q2

Output?

```javascript
async function test() {

    const value =
        await Promise.resolve(20);

    console.log(value);

}

test();
```

Output:

```javascript
20
```

---

### Q3

Which is cleaner?

```javascript
.then()
```

or

```javascript
async/await
```

Answer:

```javascript
async/await
```

for most modern applications.

---

# Summary

### async

Makes a function return a Promise.

```javascript
async function demo() {

}
```

---

### await

Waits for Promise completion.

```javascript
const data = await promise;
```

---

### Error Handling

```javascript
try {

    const data = await api();

}
catch(error) {

    console.log(error);

}
```

---

### Multiple Promises

```javascript
await Promise.all([...]);
```

---



This is where all the concepts you've learned (Promises, async/await, error handling) come together in real web applications.
