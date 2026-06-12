**Destructuring in JavaScript is a concise way to unpack values from arrays or properties from objects into distinct variables. It simplifies code, reduces repetition, and makes working with complex data structures easier.**

---

## 🔹 Array Destructuring
Extract values directly from arrays:
```javascript
const numbers = [10, 20, 30];

// Basic
const [a, b, c] = numbers;
console.log(a, b, c); // 10 20 30

// Skipping values
const [x, , y] = numbers;
console.log(x, y); // 10 30

// With rest operator
const [first, ...rest] = numbers;
console.log(first); // 10
console.log(rest);  // [20, 30]
```

👉 Useful for handling ordered data like lists or tuples.

---

## 🔹 Object Destructuring
Extract properties from objects:
```javascript
const player = { name: "Virat", age: 35, team: "India" };

// Basic
const { name, age } = player;
console.log(name, age); // Virat 35

// Aliases
const { team: country } = player;
console.log(country); // India

// Default values
const { role = "Batsman" } = player;
console.log(role); // Batsman
```

👉 Great for working with structured data like JSON responses.

---

## 🔹 Nested Destructuring
Works with nested objects/arrays:
```javascript
const person = {
  name: "Alice",
  address: { city: "Delhi", pin: 110001 }
};

const { address: { city } } = person;
console.log(city); // Delhi
```

---

## 🔹 Function Parameters
Destructuring can simplify function arguments:
```javascript
function showPlayer({ name, score }) {
  console.log(`${name} scored ${score}`);
}

showPlayer({ name: "Rohit", score: 125 });
```

👉 Makes functions cleaner when dealing with objects.

---

## 🔹 Swapping Variables
Quick swap without a temp variable:
```javascript
let a = 1, b = 2;
[a, b] = [b, a];
console.log(a, b); // 2 1
```

---

## ⚡ Comparison Table

| Feature | **Array Destructuring** | **Object Destructuring** |
|---------|---------------------------------|--------------------------------|
| Syntax | `[a, b] = arr` | `{a, b} = obj` |
| Order matters? | ✅ Yes | ❌ No |
| Default values | ✅ Supported | ✅ Supported |
| Aliases | ❌ Not needed | ✅ Supported |
| Nested support | ✅ Arrays | ✅ Objects |

---

## ⚠️ Best Practices
- Always provide **default values** for potentially missing properties.  
- Use **aliases** when property names clash or are unclear.  
- Avoid over-destructuring — keep it readable.  

---
