Here’s a simpler, plain-language explanation of what `useEffect` is doing, keeping your examples:

---

## 1. What `useEffect` really is

React’s **render function** is like a pure calculator: it only **takes props/state → returns JSX**. It **doesn’t like doing anything “outside”** itself**, like talking to the server, changing the document, or starting timers.**

`useEffect` is React’s **way to schedule “outside world” work after the render is done**. Think of it as:

> “Hey React, once you finish painting the screen, run this code that touches stuff outside your pure render.”

So anything that is **not just calculating JSX** goes in `useEffect`.

---

## 2. Your examples explained

* **Networking / fetching data**

```js
useEffect(() => {
  fetch("/api/data").then(...);
}, []);
```

> After React renders the component, fetch the data. React doesn’t block rendering for this.

* **DOM manipulation**

```js
useEffect(() => {
  document.title = "New Title";
}, [title]);
```

> Once React paints the screen, update the browser’s title. React doesn’t track `document` itself.

* **Subscriptions / event listeners**

```js
useEffect(() => {
  const handler = () => {};
  window.addEventListener("resize", handler);
  return () => window.removeEventListener("resize", handler);
}, []);
```

> Add the listener after render. When the component leaves the page, remove it so it doesn’t leak memory.

* **Timers / intervals**

```js
useEffect(() => {
  const id = setInterval(() => {}, 1000);
  return () => clearInterval(id);
}, []);
```

> Start the timer after render. Clean up when the component disappears.

* **Sync with external libraries**

```js
useEffect(() => {
  chartLib.render(chartRef.current, data);
}, [data]);
```

> Render the chart after React has drawn the component. Update only when `data` changes.

---

## 3. When NOT to use `useEffect`

Anything **inside React’s render scope**, i.e., things React can handle while drawing:

```js
const sum = numbers.reduce((a, b) => a + b, 0); // just math → no effect
return <div>{sum}</div>;
```

---

### ✅ Rule of thumb

If your code **touches something outside React’s render**, put it in `useEffect`.
If it’s just **calculating JSX**, don’t.

---

**Execution cue:** Take one small component. Move all network calls, DOM updates, timers, or subscriptions into `useEffect`. Watch the render happen first, then the effects run. Notice cleanup happens automatically when dependencies change or component unmounts.

---
