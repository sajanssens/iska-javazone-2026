### True or false?

> `goto` is a keyword in Java

---

### Answer

![img_2.png](img/02-async-new-goto/img_2.png)


---

### Async function...

... returns before the work is done

- <!-- .element: class="fragment" -->
  `thread.start()`
- <!-- .element: class="fragment" -->
  `executor.submit()`
- <!-- .element: class="fragment" -->
  `observable.subscribe()`
- <!-- .element: class="fragment" -->
  ...

---

### What that costs us

- <!-- .element: class="fragment" -->
  Cancellation
- <!-- .element: class="fragment" -->
  Exception handling
- <!-- .element: class="fragment" -->
  Resource management

All three assume the work lives inside a scope. Async work doesn't.
<!-- .element: class="fragment" -->

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

<!-- .element: class="fragment" -->

</div>

<div>

**Async**

:::mermaid
graph TD
main -->|submit| task
main --> continues[main moves on]
task -.-> somewhere[result, seen by no one]
:::

<!-- .element: class="fragment" -->

</div>

</div>

Notes:

- Built a custom IntelliJ IDEA plugin for this talk, a visual call tree debugger
- Live demo: step through sync code, one clean growing stack
- Same demo in async code, the stack just stops, the real work continues off-screen

---

### Async is the new goto!

<!-- .slide: class="is-fancy1" -->

It rips execution out of its scope.

Notes:

- Structured programming banned goto for the same reason: it broke predictable, nested control flow
- Async does the same thing to concurrent code, execution escapes the block that started it
- That escaped scope is the actual problem this talk is about, not concurrency itself

---

### Structured concurrency, back to sync rules

- <!-- .element: class="fragment" -->
  Child tasks are scoped to a block
- <!-- .element: class="fragment" -->
  The block doesn't exit until its children do, or are cancelled
- <!-- .element: class="fragment" -->
  One child fails, the rest are cancelled automatically
- <!-- .element: class="fragment" -->
  Exceptions propagate up, like a normal method call

We're not avoiding concurrency. We want it to read, and behave, like sync code.
<!-- .element: class="fragment" -->


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

---

### Take away

> Async means: the function returns before its work is done.

**And that's unstructured.** <!-- .element class="fragment" -->  

- <!-- .element class="fragment" -->
  That's like `goto`. 🤢    
- <!-- .element class="fragment" --> 
  Avoid async. 💡  

---

### Like it? See...

![img.png](../../img/02-async-new-goto/img3.png)

<!-- .slide: class="is-fancy1" -->

