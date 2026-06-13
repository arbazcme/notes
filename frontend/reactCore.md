# React Core Architecture: Revision Guide

This document serves as a clean, structured revision guide for core React concepts: Virtual DOM, Reconciliation, Batch Updates, and React Fiber.

## 1. The Virtual DOM
**The Simple View:** It is a lightweight, memory-based JavaScript copy of the actual web page (the real DOM).

**The Detailed View:** Manipulating the real DOM (the actual HTML elements the browser paints on your screen) is slow and highly expensive for performance. React solves this by creating a JavaScript object that mimics the exact structure of the DOM. 
* When a component's state or props change, React immediately builds a completely new Virtual DOM tree.
* React holds both the "old" Virtual DOM and the "new" Virtual DOM in memory.
* This abstraction allows React to compute changes rapidly behind the scenes without triggering expensive, continuous browser repaints.

### Where Do the Differences Go?
The differences between the **Old Virtual DOM** and the **New Virtual DOM** are applied directly to the **Real DOM** (the actual webpage you see in the browser). 

**The Book Draft Analogy:**
* **Old Virtual DOM:** Your first draft.
* **New Virtual DOM:** Your updated second draft.
* **Real DOM:** The printed, published book.

Instead of throwing away the entire printed book and reprinting a new one from scratch every time you fix a typo, React compares the two drafts, finds the exact sentence you changed, and cleverly updates *only that specific sentence* in the printed book.

---

## 2. Reconciliation
**The Simple View:** This is the "spot the difference" algorithm React uses to figure out exactly what changed between the old Virtual DOM and the new Virtual DOM.

**The Detailed View:** Comparing two complex trees mathematically usually takes a massive amount of computing power. React optimizes this by using a heuristic "diffing" algorithm that drops the comparison time to $\mathcal{O}(n)$ complexity. It uses two main shortcuts:

* **Element Types (HTML Tags):** If a root element changes its tag (e.g., going from a `<div>` to a `<span>` or a `<button>`), React immediately assumes the entire subtree is different. 
  * *Analogy:* If you swap a bicycle for a car, you wouldn't try to reuse the bicycle pedals on the car. You just remove the bike completely and bring in the car. React destroys the old tree and builds a new one from scratch.
* **Attributes:** If the element type is the same, React simply looks at the attributes and updates only the ones that changed (like switching a `className`).
* **Keys (For Lists):** For lists, React uses the `key` prop to efficiently match children in the original tree with children in the new tree.
  * *Analogy:* Imagine a teacher with a line of 5 students. A new student joins the front of the line. Without name tags, the teacher might think every single student changed. By giving each item in a list a unique `key` (a name tag), you tell React, "These 5 students are exactly the same, they just shifted down one spot." React then simply inserts the new student at the front without destroying and recreating the others.

---

## 3. Batch Updates
**The Simple View:** It is React's method of grouping multiple state updates into a single, unified re-render to skip unnecessary intermediate updates.

**The Detailed View:** If you update three different state variables inside a single function, React will not re-render your component three separate times. It "batches" them together and re-renders only once at the very end of the execution.
* **Automatic Batching:** Prior to React 18, React only batched updates that happened inside its own specific event handlers (like an `onClick` event). With React 18, it features *Automatic Batching*, meaning state updates inside Promises, `setTimeout`, and native browser events are also grouped together, saving significant processing time.

---

## 4. React Fiber
**The Simple View:** Fiber is the underlying internal engine (introduced in React 16) that handles Reconciliation. Its superpower is making rendering pause-able. It acts as an internal task manager that keeps your app from freezing.

**The Detailed View:** Before Fiber, React used a synchronous, recursive "stack" algorithm. Once a render started, it could not be interrupted. If a complex app had a massive update, it would block the main thread, making animations choppy and inputs unresponsive. 

Fiber rewrote the core architecture by breaking rendering work down into chunks called "fibers" (structured as a linked list). It paints a small chunk, pauses, and then checks in with the browser: *"Do you have anything more important for me to do right now, like user typing or an animation?"*
* If **yes**, Fiber pauses the rendering, lets the browser handle the typing so it feels instant, and then goes back to rendering.
* If **no**, Fiber continues rendering the next tiny chunk.
* **Concurrency:** This approach allows React to yield control back to the browser. It can pause work, abort work, or prioritize tasks on the fly.

---
