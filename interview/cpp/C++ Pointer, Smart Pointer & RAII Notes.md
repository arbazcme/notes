# C++ Pointer, Smart Pointer & RAII Notes

## Now the C++ pointer stuff

These aren't really "different kinds of pointers" in the same sense.

They're mostly about **WHO controls the lifetime/ownership of an object**.

### Imagine

```cpp
Person* p;
```

You have a `Person` somewhere in memory.

The important question is:

> Who is responsible for deleting that `Person`?

---

## Raw Pointer

```cpp
Person* p = new Person();
```

You created the object.

Now you manually have to manage it:

```cpp
delete p;
```

So a raw pointer basically means:

> "Here's an address. You manage what happens to the object."

It does **NOT** automatically mean ownership.

That's important.

---

## `unique_ptr`

```cpp
unique_ptr<Person> p = make_unique<Person>();
```

Means:

> "This `Person` belongs to exactly one owner."

When `p` dies:

```text
p dies
  ↓
Person automatically deleted
```

So `unique_ptr` is basically:

> **Pointer + automatic ownership**

Only one `unique_ptr` can own the object at a time.

Ownership can be transferred using move semantics.

---

## `shared_ptr`

```cpp
auto a = make_shared<Person>();
auto b = a;
```

Now both `a` and `b` can own the same `Person`.

```text
a ──┐
    ├──> Person
b ──┘
```

The `Person` stays alive until the **last owner** disappears.

So:

> **`shared_ptr` = multiple owners + automatic lifetime management**

It uses **reference counting** to track how many `shared_ptr`s own the object.

---

## `weak_ptr`

Suppose:

```text
A ──owns──> Person
B ──watches──> Person
```

`B` wants to look at the `Person`, but should **not keep the `Person` alive**.

That's `weak_ptr`.

So:

> **`weak_ptr` = "I know about this object, but I don't own it."**

It is commonly used with `shared_ptr` to avoid ownership cycles.

---

# Then what the hell is RAII?

This is the big idea underneath all of this.

**RAII = Resource Acquisition Is Initialization**

The simple idea is:

> When an object is created, it acquires a resource. When the object is destroyed, the resource is released automatically.

### Example

```cpp
{
    unique_ptr<Person> p = make_unique<Person>();

} // p dies here
  // Person is automatically deleted
```

You don't write:

```cpp
delete p;
```

because the object's lifetime controls the resource's lifetime.

That's **RAII**.

And resources aren't only memory.

They can be:

- memory
- file
- mutex/lock
- socket
- database connection

The general idea is:

> **"Put resource management inside an object's lifetime."**

---

# The easiest mental picture

## Raw Pointer

```text
RAW POINTER
"I have an address."
        ↓
YOU manage it
```

## `unique_ptr`

```text
unique_ptr
"I own this."
        ↓
I die → object dies
```

## `shared_ptr`

```text
shared_ptr
"We own this."
        ↓
Last owner dies → object dies
```

## `weak_ptr`

```text
weak_ptr
"I can see this."
        ↓
I DON'T own it
```

## RAII

```text
RAII
"My lifetime controls the resource."
        ↓
object created → resource acquired
object destroyed → resource released
```

---

# One-Line Memory Trick

```text
raw pointer  → I have an address
unique_ptr   → one owner
shared_ptr   → multiple owners
weak_ptr     → non-owner observer
RAII         → object lifetime manages resource lifetime
```
