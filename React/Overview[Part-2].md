Below is the **React execution model**, end-to-end,

## 0. What problem exists without React’s execution model

If you manipulate the DOM directly:

* Every state change forces you to:

  * Manually find DOM nodes
  * Decide what changed
  * Update them in correct order
* Multiple changes cause:

  * Redundant DOM writes
  * Inconsistent UI states
  * Hard-to-reason update order

Root cause:

> **DOM mutations are expensive and unordered. There is no deterministic update pipeline.**

So how do we enforce **order, predictability, and efficiency**?

---

## 1. Core idea (one sentence)

> React is a **deterministic state → render → commit pipeline** that re-executes your component functions and reconciles the result with the DOM.

Everything else exists to protect this pipeline.

---

## 2. What a React component actually is

A React component is **not**:

* An object
* An instance
* A UI element

It is:

> **A pure function that maps (props, state) → virtual tree**

Every render:

```text
ComponentFunction(props, state) -> ReactElementTree
```

No memory lives *inside* the function body.

---

## 3. Two worlds React manages

### 3.1 Render world (pure, replayable)

* Executes component functions
* Builds a virtual tree
* No DOM access allowed
* Can be paused, restarted, discarded

### 3.2 Commit world (impure, irreversible)

* Applies changes to real DOM
* Runs effects
* Cannot be interrupted

This split is **fundamental**.

---

## 4. High-level execution phases

Every update goes through:

```
Trigger
  ↓
Render Phase
  ↓
Reconciliation
  ↓
Commit Phase
```

Let’s expand each.

---

## 5. Trigger phase

A render is triggered by:

* `setState`
* `setX` from `useState`
* New props from parent
* Context change

Important:

> **Triggers do not update state immediately. They enqueue work.**

---

## 6. Render phase (function execution)

React starts from the root and **re-executes component functions**.

For each component:

1. Reset hook cursor to 0
2. Call component function
3. Each hook call:

   * Reads/writes from an internal hook array
   * Advances cursor

This is why:

* Hooks **must be called in the same order**
* No conditionals, loops, early returns around hooks

Internally:

```text
hooks = [slot0, slot1, slot2, ...]
cursor = 0

useState -> hooks[cursor++]
useEffect -> hooks[cursor++]
```

No names. Only positions.

---

## 7. Reconciliation (diffing)

After render, React has:

* Previous virtual tree
* New virtual tree

Reconciliation rules:

1. Same component type → reuse
2. Different type → destroy + recreate
3. Lists → keys decide identity

Output:

> A list of **effects to apply** (DOM ops, effect runs, cleanups)

No DOM touched yet.

---

## 8. Commit phase (single, synchronous)

Now React **must finish**.

Order:

1. Apply DOM mutations
2. Run layout effects (`useLayoutEffect`)
3. Paint happens
4. Run passive effects (`useEffect`)

Important guarantees:

* Commit is atomic
* DOM is always consistent
* Effects see committed DOM

---

## 9. Effects execution model (critical)

### `useEffect(fn, deps)`

Lifecycle:

* On mount:

  * Commit DOM
  * Run `fn`
* On deps change:

  * Run previous cleanup
  * Run new `fn`
* On unmount:

  * Run cleanup

Cleanup runs:

> **Before the next effect OR on unmount**

Not immediately. Not during render.

---

## 10. Why render can run multiple times

Render phase:

* Is pure
* Can be restarted
* Can be discarded

React may:

* Re-render without committing
* Render twice in Strict Mode (dev only)

If your render has side effects → bugs.

---

## 11. State updates batching

Multiple `setState` calls:

* Are queued
* Processed together
* Cause **one render**

Unless forced sync.

State update model:

```text
oldState
  ↓ apply update queue
newState
```

Not step-by-step mutation.

---

## 12. Scheduling and priority

React assigns priorities:

* User input → high
* Data fetching → low
* Transitions → interruptible

Render phase can:

* Pause
* Yield
* Resume

Commit phase cannot.

---

## 13. Why hooks exist at all

Without hooks:

* State tied to class instances
* Lifecycle split across methods
* Reuse via inheritance (bad)

Hooks:

* Attach state to render position
* Keep execution linear
* Enable composition

Tradeoff:

> Order becomes a hard constraint.

---

## 14. Full mental model (compressed)

```
Event
  ↓
enqueue update
  ↓
render (pure, repeatable)
  ↓
diff trees
  ↓
commit (DOM + effects)
```

That’s React.

Nothing more.

---

### Execution cue

Open a simple component, add `console.log` in render, in `useEffect`, and in cleanup. Trigger state updates and observe the exact order until it’s boring.
