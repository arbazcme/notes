# I/O, Async, Library & Framework Notes

## I/O-bound vs CPU-bound

### I/O-bound

Mostly spending time **waiting for external operations**.

### Examples

- database
- network
- file system

### Example

```text
Request
   ↓
Database query
   ↓
WAIT
   ↓
Database response
```

The CPU isn't doing heavy computation during most of that waiting.

---

### CPU-bound

Mostly spending time **doing computation**.

### Examples

- huge image processing
- complex mathematical computation
- large encryption workload

### Example

```text
Request
   ↓
Heavy computation
   ↓
CPU keeps working
   ↓
Result
```

---

## Callback → Promise → async/await

The progression is roughly:

```text
Callback
   ↓
basic way of handling async completion

Promise
   ↓
structured abstraction for an async result

async/await
   ↓
cleaner syntax for working with Promises
```

---

## Callback

A **callback** is a function passed to another function so it can be called later.

### Example

```js
getUser((user) => {
    console.log(user);
});
```

Here:

```text
getUser()
   ↓
starts operation
   ↓
operation finishes
   ↓
callback runs
```

So:

> **Callback = "Call this function when you're done."**

---

## Promise

A **Promise** represents the eventual result of an asynchronous operation.

It can be:

```text
pending
   ↓
fulfilled

or

pending
   ↓
rejected
```

### Example

```js
getUser()
    .then((user) => {
        console.log(user);
    })
    .catch((err) => {
        console.log(err);
    });
```

So:

> **Promise = "I'll give you the result later."**

---

## async/await

`async/await` provides cleaner syntax for working with Promises.

### Example

```js
async function getData() {
    const user = await getUser();
    console.log(user);
}
```

Instead of chaining:

```js
getUser()
    .then((user) => {
        console.log(user);
    });
```

You can write:

```js
const user = await getUser();
console.log(user);
```

It looks more like normal sequential code, but the operation is still asynchronous.

---

# Library vs Framework

## 1. What is a Library?

A **library** is code someone else wrote that **YOU choose to use**.

Think:

> "I am writing my program. I need something → I'll call the library."

### Example

```cpp
vector<int> v;
sort(v.begin(), v.end());
```

`sort()` is library code.

You decide when to call it.

So:

> **Library = your code controls the flow, and your code calls the library.**

### Simple flow

```text
Your program
     ↓
Library
     ↓
Result
```

Examples:

- C++ STL
- Lodash
- Axios

---

# 2. What is a Framework?

A **framework** is a structure that **runs/organizes your application**, and your code plugs into it.

Think:

> "The framework is running the application. At certain points, it calls my code."

### Example

In a web framework:

```text
Framework starts
       ↓
Receives request
       ↓
Calls your function
       ↓
Your function handles it
       ↓
Framework continues
```

So:

> **Framework = framework controls the overall flow, and it calls your code at the appropriate points.**

---

# Key Difference

```text
Library:
your code → library

Framework:
framework → your code
```

This is the key idea behind **inversion of control**.

---

# Inversion of Control

Normally:

```text
Your code
   ↓
calls something
```

With a framework:

```text
Framework
   ↓
calls your code
```

So the **control of the application flow is inverted**.

### Easy memory trick

> **Library: You call it.**

> **Framework: It calls you.**
