
# Understanding React StrictMode

## 1. The Problem React is Trying to Catch
In React, **rendering is supposed to be pure**. A pure function means that given the same inputs (props and state), it should always return the same output (UI) without causing any "side effects."

### What is a Side Effect?
A side effect is anything that happens outside of calculating the UI. This includes:
* ❌ API calls
* ❌ Timers (setTimeout/setInterval)
* ❌ Manual DOM mutations
* ❌ Generating random values (Math.random)
* ❌ Changing state during render

**The Rule:** `(props + state) → return UI`

**Common Mistake:**
```javascript
function App() {
  console.log("rendered");
  fetch("/api"); // ❌ Side effect inside the render body
  return <div>Hello</div>;
}

```

This might appear to work, but it causes unpredictable behavior when React renders more than once.

---

## 2. Why React Renders More Than Once

To improve performance and enable **Concurrent Features**, React 18+ reserves the right to:

* Pause and restart rendering.
* Throw away a render and start over.
* Re-run a render.

If your rendering phase has side effects, React **cannot safely perform these optimizations**.

---

## 3. What StrictMode Does

`StrictMode` acts like a stress test for your code. In **Development Mode only**, it:

1. **Renders components twice.**
2. **Mounts, unmounts, and re-mounts Effects.**

**The Goal:** To expose code that incorrectly assumes a component only "happens once." If your code is pure, double-rendering causes no issues. If your code is buggy, it breaks loudly in development so you can fix it before it reaches a user.

---

## 4. Concrete Example

### Without StrictMode

```javascript
function App() {
  useEffect(() => {
    console.log("API call");
  }, []);
}

```

**Output:** `API call`

(This gives you a false sense of security.)

### With StrictMode (Dev Only)

**Output:**

```text
API call
cleanup
API call

```

React is essentially saying: *"Your effect must be resilient enough to be destroyed and recreated at any time."*

---

## 5. The Golden Rules

* **Render phase:** Must be pure (no side effects).
* **Effects:** Must always include a cleanup function if they perform an action (like a subscription or a timer).
* **Never assume "runs once":** Even if you use an empty dependency array `[]`.

---

## 6. What Happens in Production?

* **No** double rendering.
* **No** double effects.
* `StrictMode` becomes completely invisible and has **zero** performance impact.

---

## 7. The Mental Model

> **StrictMode is a "fake enemy" that attacks your code in development so that real users never encounter those bugs in production.**

