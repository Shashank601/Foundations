
Below is the **minimal, correct answer**, built from first principles.

---

## 1. What problem a component solves

A component exists to do **one thing only**:

> Given some input, describe what the UI should look like.

Everything else is optional.

So the *absolute core* is:

* input
* output



---

## 2. The only thing a component must have

### A function

```js
function Component(props) {
  return <div />;
}
```

That’s already a valid component.

No state.
No hooks.
No lifecycle logic.

If it can return UI → it’s a component.

---

## 3. Inputs a component may need

### 1. Props (external input)

Props are:

* read-only
* owned by parent
* used to parameterize UI

Without props:

* component is static
* reusable only by copy-paste

So most real components need props.

---

### 2. State (internal memory)

State is needed only when:

* data changes over time
* change originates inside the component

Examples:

* form input
* toggle
* local UI state

If data comes from parent → **do not duplicate it in state**.

State is optional, not mandatory.

---

## 4. Side effects (only when necessary)

A component **does not need side effects by default**.

Side effects are needed only when the component must:

* talk to the outside world
* synchronize with non-React systems

Examples:

* fetch data
* subscribe to events
* manipulate DOM
* start timers

If nothing external exists → no effects.

---

## 5. Cleanup responsibility

If a component:

* creates something outside React

Then it **must**:

* clean it up when removed

This is not optional.
This is ownership.

No cleanup = broken component.

---

## 6. What a component does NOT need

A component does **not** need:

* lifecycle methods (hooks cover this)
* local state by default
* effects by default
* memoization
* context
* refs
* callbacks
* reducers

These are tools, not requirements.

---

## 7. The real checklist (this is the answer)

A correct component answers these questions:

1. **What UI do I describe?**
2. **What inputs does it depend on?** (props)
3. **Does anything change over time?** (state)
4. **Does it touch the outside world?** (effects)
5. **If yes, how is it cleaned up?**

If you can answer all five, the component is complete.

---

## 8. Minimal component patterns

### Pure component (best case)

```js
function Label({ text }) {
  return <span>{text}</span>;
}
```

Needs:

* function
* props
  Nothing else.

---

### Stateful component

```js
function Counter() {
  const [x, setX] = useState(0);
  return <button onClick={() => setX(x + 1)}>{x}</button>;
}
```

Needs:

* state
* render logic

Still no effects.

---

### Effectful component

```js
function Timer() {
  useEffect(() => {
    const id = setInterval(() => {}, 1000);
    return () => clearInterval(id);
  }, []);
  return null;
}
```

Needs:

* effect
* cleanup

---

## 9. Final rule (remember this)

A component is **not a place to put logic**.

It is:

* a **description**
* plus **owned side effects**
* plus **cleanup**

Anything else is accidental complexity.

---

**Execution cue:**
Take any component you wrote recently. Remove everything except `props → JSX`. Add things back only if they answer one of the five checklist questions.
