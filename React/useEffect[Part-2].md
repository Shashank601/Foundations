Not exactly. `useEffect` **can run multiple things at once**, but the key is **all of them are treated as part of the same effect function**. React only guarantees that **the entire effect runs after the render**, and it cleans up (if you return a cleanup function) **before the next effect runs or before unmounting**.

---

### 1. Multiple things in one `useEffect`

```js
useEffect(() => {
  console.log("Effect starts");

  // Network call
  fetch("/api/data").then(res => res.json()).then(console.log);

  // Timer
  const id = setInterval(() => console.log("tick"), 1000);

  // Cleanup
  return () => clearInterval(id);
}, []);
```

> This one `useEffect` runs **all of it together** after the render. The cleanup will cancel the timer when the component unmounts.

---

### 2. Splitting effects

You can also create **multiple `useEffect`s** for clarity:

```js
useEffect(() => {
  fetch("/api/data").then(res => res.json()).then(console.log);
}, []);

useEffect(() => {
  const id = setInterval(() => console.log("tick"), 1000);
  return () => clearInterval(id);
}, []);
```

> Each effect runs **separately after render**. React doesn’t merge them—they just all run after paint.

---

### 3. Key point

* `useEffect` **doesn’t block rendering**. Everything inside it runs **after the DOM updates**.
* **You can do multiple things in one effect**. It’s not limited to one operation.
* Splitting effects is mostly for **readability or independent cleanup**.

