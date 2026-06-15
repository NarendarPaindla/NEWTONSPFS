# Error Handling in JavaScript (Complete Beginner to Advanced)

Before learning error handling, understand:

An **Error** is something that stops your program from working correctly.

Example:

```javascript
console.log(name);
```

Output:

```javascript
ReferenceError: name is not defined
```

Because `name` variable does not exist.

---

# Why Error Handling?

Without error handling:

```javascript
console.log("Start");

let result = 10 / 0;

console.log(result);

console.log("End");
```

Output:

```javascript
Start
Infinity
End
```

No issue here.

But:

```javascript
console.log("Start");

console.log(user.name);

console.log("End");
```

Output:

```javascript
Start

ReferenceError

Program Stops
```

The remaining code does not execute.

To prevent application crashes, JavaScript provides:

```javascript
try
catch
finally
throw
```

---

# try...catch

Basic Syntax:

```javascript
try {

    // risky code

} catch(error) {

    // handle error

}
```

---

# Example 1

Without try-catch:

```javascript
console.log(user.name);
```

Program crashes.

---

With try-catch:

```javascript
try {

    console.log(user.name);

} catch(error) {

    console.log("Something went wrong");

}
```

Output:

```javascript
Something went wrong
```

Program continues.

---

# Execution Flow

```text
try block
    ↓
Error Found
    ↓
catch block
    ↓
Program Continues
```

---

# Example 2

```javascript
try {

    console.log("Start");

    console.log(user.name);

    console.log("Middle");

} catch(error) {

    console.log("Error Handled");

}

console.log("End");
```

Output:

```javascript
Start
Error Handled
End
```

Notice:

```javascript
console.log("Middle");
```

never executes because the error occurs before it.

---

# Understanding the error Object

```javascript
try {

    console.log(user.name);

} catch(error) {

    console.log(error);

}
```

Output:

```javascript
ReferenceError: user is not defined
```

`error` is an object containing details.

---

# Useful Error Properties

## error.name

```javascript
try {

    console.log(user.name);

} catch(error) {

    console.log(error.name);

}
```

Output:

```javascript
ReferenceError
```

---

## error.message

```javascript
try {

    console.log(user.name);

} catch(error) {

    console.log(error.message);

}
```

Output:

```javascript
user is not defined
```

---

# Example

```javascript
try {

    console.log(user.name);

} catch(error) {

    console.log(error.name);
    console.log(error.message);

}
```

Output:

```javascript
ReferenceError
user is not defined
```

---

# finally Block

Sometimes you want code to run whether an error occurs or not.

Syntax:

```javascript
try {

}
catch(error) {

}
finally {

}
```

---

# Example

```javascript
try {

    console.log("Inside Try");

} catch(error) {

    console.log("Inside Catch");

} finally {

    console.log("Always Runs");

}
```

Output:

```javascript
Inside Try
Always Runs
```

---

# Error Case

```javascript
try {

    console.log(user.name);

} catch(error) {

    console.log("Error Handled");

} finally {

    console.log("Always Runs");

}
```

Output:

```javascript
Error Handled
Always Runs
```

---

# Why finally is Useful?

Real World Example:

```javascript
showLoader();

fetchData();

hideLoader();
```

What if fetch fails?

Loader remains visible.

Better:

```javascript
try {

    fetchData();

} catch(error) {

    console.log(error);

} finally {

    hideLoader();

}
```

Now loader always disappears.

---

# throw Keyword

JavaScript lets you create your own errors.

Syntax:

```javascript
throw "Error Message";
```

or

```javascript
throw new Error("Error Message");
```

---

# Example 1

```javascript
let age = 15;

if(age < 18){
    throw new Error("Age must be 18 or above");
}
```

Output:

```javascript
Error: Age must be 18 or above
```

---

# Example 2

```javascript
try {

    let age = 15;

    if(age < 18){
        throw new Error("Age must be 18+");
    }

} catch(error) {

    console.log(error.message);

}
```

Output:

```javascript
Age must be 18+
```

---

# Real World Validation Example

```javascript
function login(username){

    if(!username){
        throw new Error("Username Required");
    }

    return "Login Success";
}
```

Usage:

```javascript
try {

    console.log(login(""));

} catch(error){

    console.log(error.message);

}
```

Output:

```javascript
Username Required
```

---

# Common JavaScript Errors

## 1. ReferenceError

Occurs when variable doesn't exist.

```javascript
console.log(user);
```

Output:

```javascript
ReferenceError
```

---

## 2. TypeError

Using something incorrectly.

```javascript
let num = 10;

num();
```

Output:

```javascript
TypeError
```

Because numbers are not functions.

---

## 3. SyntaxError

Wrong syntax.

```javascript
if(true {
    console.log("Hello");
}
```

Output:

```javascript
SyntaxError
```

---

## 4. RangeError

Invalid range.

```javascript
let arr = new Array(-1);
```

Output:

```javascript
RangeError
```

---

# Custom Error Class

Advanced Topic

```javascript
class ValidationError extends Error {

    constructor(message){

        super(message);

        this.name = "ValidationError";

    }

}
```

Usage:

```javascript
throw new ValidationError("Invalid Email");
```

Output:

```javascript
ValidationError: Invalid Email
```

---

# Error Handling in Promises

Very Important

---

## Using catch()

```javascript
Promise.reject("Server Error")
.catch((error)=>{

    console.log(error);

});
```

Output:

```javascript
Server Error
```

---

# Throwing Error in then()

```javascript
Promise.resolve()
.then(()=>{

    throw new Error("Something Failed");

})
.catch((error)=>{

    console.log(error.message);

});
```

Output:

```javascript
Something Failed
```

---

# Error Propagation

```javascript
Promise.resolve()
.then(()=>{

    throw new Error("Error");

})
.then(()=>{

    console.log("A");

})
.then(()=>{

    console.log("B");

})
.catch((error)=>{

    console.log(error.message);

});
```

Output:

```javascript
Error
```

After error:

```text
Skip Remaining then()
↓
Go To catch()
```

---

# Error Handling with async/await

We haven't fully learned async/await yet, but here's the preview:

```javascript
async function getData(){

    try {

        let response = await fetch(url);

        console.log(response);

    } catch(error){

        console.log(error);

    }

}
```

This is the modern way used in React and Node.js applications.

---

# Interview Questions

### Q1

Output?

```javascript
try {

    console.log("A");

    throw new Error("Oops");

    console.log("B");

} catch(error){

    console.log("C");

}

console.log("D");
```

Answer:

```javascript
A
C
D
```

---

### Q2

Output?

```javascript
try {

    console.log("Hello");

} finally {

    console.log("Done");

}
```

Answer:

```javascript
Hello
Done
```

---

### Q3

Output?

```javascript
try {

    throw new Error("Failed");

} catch(error){

    console.log(error.message);

}
```

Answer:

```javascript
Failed
```

---

# Summary

### Synchronous Error Handling

```javascript
try {

}
catch(error){

}
finally{

}
```

### Creating Errors

```javascript
throw new Error("Message");
```

### Promise Error Handling

```javascript
promise.catch(...)
```

### Async/Await Error Handling

```javascript
try {
    await something();
}
catch(error){
}
```

