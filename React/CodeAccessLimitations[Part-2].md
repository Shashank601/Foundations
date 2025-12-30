
## What AI “clone this site” extensions actually do (and what they never do)

These tools **do not access real source code**.
They only observe what the browser already exposes after rendering.

They are **DOM / screenshot → code generators**, not reverse engineers.

---

## Core limitation (non-negotiable)

Once code is shipped to the browser:

* Framework abstractions are gone
* Component boundaries are erased
* Business logic is compiled/minified/flattened
* Only **effects**, not **intent**, remain

So these tools work with **outputs**, not **implementation**.

---

## The actual pipeline these extensions use

### 1. Post-render DOM extraction (not source)

They read:

* Rendered HTML tree
* Computed styles
* Layout boxes
* Visible text
* Image assets

This is **post-render DOM**, not React/Vue/Svelte source.
Imports, hooks, state machines, services — all gone.

---

### 2. Heuristic component inference

The AI **guesses structure**:

* “This looks like a navbar”
* “This looks like a card grid”
* “This looks like a modal”

It matches visual patterns to components it already knows.

This is **pattern matching**, not reverse engineering.

No original component boundaries are recovered.

---

### 3. CSS regeneration (not extraction)

They do **not** read original CSS.

Instead they:

* Sample computed styles
* Rewrite them into Tailwind / CSS / styled-components

That’s why:

* Class names look clean
* Code looks human-written
* Styles appear intentional

But this is **reconstruction**, not recovery.

---

### 4. Shallow behavior emulation

They fake interaction:

* Buttons → dummy handlers
* Forms → no real validation
* Animations → approximated
* Data → hardcoded placeholders

There is:

* No real state management
* No business logic
* No edge handling

---

## What is ALWAYS missing in AI clones

These are **never present**, because they are not visible in the DOM:

* Real data flow
* API integration
* Authentication / authorization
* Permission systems
* Error handling
* Performance optimizations
* Accessibility details
* Edge cases
* Non-visual logic

---

## Where AI clones fail instantly

Try cloning any of the following:

* Dashboard with role-based permissions
* Infinite scroll with caching
* Real-time sync
* Complex multi-step forms

The clone collapses because **visual similarity ≠ functional equivalence**.

---

## Capability boundary (hard limits)

| Capability                    | Possible |
| ----------------------------- | -------- |
| See original React components | ❌        |
| See readable production JS    | ❌        |
| Reconstruct visual layout     | ✅        |
| Generate “similar” UI code    | ✅        |
| Clone real behavior deeply    | ❌        |

---

## Bottom line

These tools:

* Don’t steal
* Don’t decode
* Don’t reverse-engineer

They **hallucinate a clean implementation** that merely *resembles* the site.

If the product’s value is in **logic**, **data**, or **rules**, nothing important is exposed.

---

Execution cue: pick one real app you use daily and mentally trace where its behavior lives — you’ll see how little of it reaches the DOM.
