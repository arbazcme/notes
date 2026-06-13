# ⚛️ React Hooks - Student Notes

---

# 🧠 Hook Dependency Cheat Sheet

| Hook | Dependency Same | Dependency Changed |
|------|----------------|-------------------|
| useMemo | Reuse old value | Recalculate value |
| useCallback | Reuse old function | Create new function |
| useEffect | Do nothing | Run effect |

---

# 🎯 useEffect

## What is it?

Whenever something changes, perform an action.

```jsx
useEffect(() => {
    console.log("Count changed");
}, [count]);
```

---

## Visualization

```text
count = 0
     ↓
Effect Runs

count = 0
     ↓
Nothing Happens

count = 1
     ↓
Effect Runs Again
```

---

## Think Like React

```text
Did dependency change?

YES ✅
 ↓
Run Effect

NO ❌
 ↓
Skip
```

---

## Real Life Example

```jsx
useEffect(() => {
    fetchUsers();
}, []);
```

```text
Component Loaded
      ↓
Fetch Users
```

---

## Common Uses

```text
🌐 API Calls

⏰ Timers

🎧 Event Listeners

💾 Local Storage

📄 DOM Manipulation
```

---

# 🎯 useRef

## What is it?

A variable that survives re-renders.

Changing it DOES NOT re-render.

---

## Visualization

```jsx
const countRef = useRef(0);
```

React creates:

```text
┌─────────┐
│ current │ → 0
└─────────┘
```

---

Update:

```jsx
countRef.current++;
```

Now:

```text
┌─────────┐
│ current │ → 1
└─────────┘
```

---

## React Thinks

```text
Ref Changed?

YES

Need Re-render?

NO
```

---

## State vs Ref

### State

```jsx
setCount(5);
```

```text
Value Changed
      ↓
Re-render
```

---

### Ref

```jsx
countRef.current = 5;
```

```text
Value Changed
      ↓
No Re-render
```

---

## Most Famous Example

Focus an input.

```jsx
const inputRef = useRef();
```

```jsx
<input ref={inputRef} />
```

```jsx
inputRef.current.focus();
```

---

Visualization:

```text
inputRef
   │
   ▼
<input />
```

---

# 🎯 useMemo

## What is it?

Stores a calculated answer.

---

Without useMemo

```jsx
const total = expensiveCalculation();
```

```text
Render 1
 ↓
Calculate

Render 2
 ↓
Calculate Again

Render 3
 ↓
Calculate Again
```

---

With useMemo

```jsx
const total = useMemo(() => {
    return expensiveCalculation();
}, [data]);
```

```text
Render 1
 ↓
Calculate

Render 2
 ↓
Reuse Old Answer

Render 3
 ↓
Reuse Old Answer
```

---

## Think Like React

```text
Did dependency change?

YES
 ↓
Calculate Again

NO
 ↓
Reuse Old Value
```

---

## Example

```jsx
const totalPrice = useMemo(() => {
    return products.reduce(
        (sum, p) => sum + p.price,
        0
    );
}, [products]);
```

---

## Easy Memory Trick

```text
Question:
5 + 10

Answer:
15
```

useMemo stores:

```text
15
```

---

# 🎯 useCallback

## What is it?

Stores a function.

---

Without useCallback

```jsx
const handleClick = () => {};
```

```text
Render 1
 ↓
Function A

Render 2
 ↓
Function B

Render 3
 ↓
Function C
```

New function every render.

---

With useCallback

```jsx
const handleClick = useCallback(() => {
    console.log("Clicked");
}, []);
```

```text
Render 1
 ↓
Function A

Render 2
 ↓
Function A

Render 3
 ↓
Function A
```

Same function reused.

---

## Think Like React

```text
Did dependency change?

YES
 ↓
Create New Function

NO
 ↓
Reuse Old Function
```

---

## Example

```jsx
const handleSubmit = useCallback(() => {
    console.log("Submitted");
}, []);
```

---

## Easy Memory Trick

Imagine Pizza 🍕

```text
Pizza = Result

Recipe = Function
```

useMemo stores:

```text
🍕 Pizza
(Result)
```

useCallback stores:

```text
📜 Recipe
(Function)
```

---

# 🚀 One-Line Revision

```text
useEffect
→ Run something when dependency changes
```

```text
useRef
→ Store data without re-render
```

```text
useMemo
→ Store answer
```

```text
useCallback
→ Store function
```
