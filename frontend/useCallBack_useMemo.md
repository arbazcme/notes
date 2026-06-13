# ⚛️ React `useMemo` & `useCallback` — Intuition First

## 🧠 `useMemo`

Imagine you have a very expensive calculation.

```jsx
const total = products.reduce((sum, p) => sum + p.price, 0);
```

Every render, React runs this calculation again.

If the calculation is expensive and the data hasn't changed, that's wasted work.

`useMemo` tells React:

> "If the dependencies haven't changed, give me the old result instead of recalculating it."

```jsx
const total = useMemo(() => {
    return products.reduce((sum, p) => sum + p.price, 0);
}, [products]);
```

### What React Stores

```text
Result: 1500
Dependencies: [products]
```

If `products` doesn't change:

```text
Reuse 1500
```

If `products` changes:

```text
Recalculate
Store new result
```

### Real-Life Analogy

Imagine your math teacher asks:

```text
987654 × 123456
```

You solve it once and write the answer in your notebook.

Next time she asks the exact same question:

```text
Don't solve again.
Open notebook.
Reuse answer.
```

That notebook is `useMemo`.

---

# 🧠 useCallback

Imagine you have a function:

```jsx
const handleSubmit = () => {
    console.log("Submitted");
};
```

Every render:

```text
Render 1 → Function A
Render 2 → Function B
Render 3 → Function C
```

Even though the code looks identical, JavaScript creates a new function object each render.

`useCallback` tells React:

> "Keep using the same function unless one of its dependencies changes."

```jsx
const handleSubmit = useCallback(() => {
    console.log("Submitted");
}, []);
```

### What React Stores

```text
Function: Function A
Dependencies: []
```

Next render:

```text
Dependencies unchanged
Reuse Function A
```

If dependencies change:

```text
Create Function B
Replace Function A
```

### Real-Life Analogy

Imagine you wrote a recipe on paper.

```text
How to make Pizza 🍕
```

Instead of writing the recipe again every day, you keep reusing the same paper.

Only when the recipe changes do you write a new paper.

That paper is the function reference stored by `useCallback`.

---

# 🚨 Important Thing I Learned

Many developers think:

```jsx
const getValue = useCallback(() => a + b, [a, b]);
```

means React stores:

```text
1 + 2 = 3
2 + 3 = 5
5 + 7 = 12
```

❌ Wrong.

React stores:

```text
The function itself
```

not

```text
The returned result
```

---

# 🎯 My Mental Model

### useMemo

```text
Store the ANSWER.
```

Example:

```text
5 + 10 = 15
```

React remembers:

```text
15
```

---

### useCallback

```text
Store the FORMULA.
```

Example:

```jsx
() => a + b
```

React remembers:

```text
The function object
```

not the answer.

---

# 🚀 Interview One-Liners

### useMemo

> useMemo memoizes a computed value and recomputes it only when its dependencies change.

### useCallback

> useCallback memoizes a function reference and recreates it only when its dependencies change.
