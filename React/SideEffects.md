
## 1. Fetching data

### ❌ Before (wrong)

Problem: fetch runs during render → breaks React rules.

```js
function User() {
  const data = fetch("/api/user"); // side effect in render
  return <div>{data.name}</div>;
}
```

Why wrong:

* render must be pure
* fetch may run multiple times
* React may discard render

---

### ✅ After (correct)

```js
function User() {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch("/api/user")
      .then(res => res.json())
      .then(setUser);
  }, []);

  if (!user) return null;
  return <div>{user.name}</div>;
}
```

What changed:

* side effect moved to `useEffect`
* render only describes UI
* fetch runs after commit

---

## 2. Subscribing to events

### ❌ Before (wrong)

Problem: listener added every render, never removed.

```js
function Tracker() {
  window.addEventListener("mousemove", () => {});
  return null;
}
```

Why wrong:

* leaks listeners
* duplicates handlers
* unbounded growth

---

### ✅ After (correct)

```js
function Tracker() {
  useEffect(() => {
    function onMove(e) {}

    window.addEventListener("mousemove", onMove);
    return () => {
      window.removeEventListener("mousemove", onMove);
    };
  }, []);

  return null;
}
```

What changed:

* ownership is explicit
* cleanup on unmount
* listener exists exactly once

---

## 3. Manipulating the DOM

### ❌ Before (wrong)

Problem: DOM access during render.

```js
function FocusInput() {
  document.querySelector("input")?.focus();
  return <input />;
}
```

Why wrong:

* DOM may not exist yet
* render can be replayed
* breaks concurrent rendering

---

### ✅ After (correct)

```js
function FocusInput() {
  const ref = useRef(null);

  useEffect(() => {
    ref.current.focus();
  }, []);

  return <input ref={ref} />;
}
```

What changed:

* DOM access after commit
* ref used instead of query
* predictable timing

---

## 4. Starting timers

### ❌ Before (wrong)

Problem: timer recreated on every render.

```js
function Clock() {
  setInterval(() => {}, 1000);
  return null;
}
```

Why wrong:

* infinite timers
* no cleanup
* uncontrolled behavior

---

### ✅ After (correct)

```js
function Clock() {
  useEffect(() => {
    const id = setInterval(() => {}, 1000);
    return () => clearInterval(id);
  }, []);

  return null;
}
```

What changed:

* timer created once
* timer cleaned on unmount
* no leaks

---

## Core rule (non-negotiable)

**Render phase**

* must be pure
* no IO
* no subscriptions
* no DOM access

**Effect phase**

* all side effects
* explicit cleanup
* tied to ownership

If something touches the outside world → it belongs in `useEffect`.

---

**Execution cue:**
Take one of your components. Move *every* non-UI operation into `useEffect`. If it breaks, the component was incorrectly designed.
