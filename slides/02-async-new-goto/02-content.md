### True or false?

> `goto` is a keyword in Java

---

### Answer

![img_2.png](img/02-async-new-goto/img_2.png)


---

### Async is the new goto

<!-- .slide: class="is-fancy1" -->

It rips execution out of its scope.

Notes:

- Structured programming banned goto for the same reason: it broke predictable, nested control flow
- Async does the same thing to concurrent code, execution escapes the block that started it
- That escaped scope is the actual problem this talk is about, not concurrency itself

---

### Async returns before the work is done

- `thread.start()`
- `executor.submit()`
- `observable.subscribe()`

---

### Take away

> Async means: the function returns before its work is done.

**And that's unstructured.**

Notes:

- JavaZone talk on Java's upcoming structured concurrency feature
- Each example hands off work and moves on immediately, no waiting
- Ask the room: where did that work go, who's watching it now
- This is the key takeaway of the whole slide, the rest of the talk unpacks why "unstructured" is a problem

---

### What that costs us

- Cancellation
- Exception handling
- Resource management

All three assume the work lives inside a scope. Async work doesn't.

Notes:

- A cancelled parent doesn't cancel the orphaned thread or subscription
- An exception in a background task has nowhere defined to surface to
- try-with-resources can't close what it can't see anymore

---

### Seeing it: two call trees

<div class="kc-columns kc-gap2">

<div>

**Sync**

:::mermaid
graph TD
main --> methodA
methodA --> methodB
:::

</div>

<div>

**Async**

:::mermaid
graph TD
main -->|submit| task
main --> continues[main moves on]
task -.-> somewhere[result, seen by no one]
:::

</div>

</div>

Notes:

- Built a custom IntelliJ IDEA plugin for this talk, a visual call tree debugger
- Live demo: step through sync code, one clean growing stack
- Same demo in async code, the stack just stops, the real work continues off-screen

---

### Structured concurrency, back to sync rules

- Child tasks are scoped to a block
- The block doesn't exit until its children do, or are cancelled
- One child fails, the rest are cancelled automatically
- Exceptions propagate up, like a normal method call

We're not avoiding concurrency. We want it to read, and behave, like sync code.


Notes:

- Java's `StructuredTaskScope`, still finding its final shape through JEP previews
- Fork is just a method call, join is just returning, same guarantees as sync code
- This is the payoff line for the whole talk, repeat it if nothing else lands

---


### Call tree

![img_1.png](img/02-async-new-goto/img_1.png)

---


### Call tree

![img_2.png](img/02-async-new-goto/img.png)