### From prompting to context

- Prompt engineering: craft the perfect instruction
- Context engineering: design what the model sees

**And what it never sees**

Notes:

- Baruch Sadogursky, JavaZone 2026
- 100k tokens of context, still forgets two messages ago
- More context is not better context
- Demos were a coding agent, applies to any agent

---

### 4 antipatterns

<div class="kc-grid kc-gap2">

**The Stuffed Prompt**
<!-- .element: class="pos-color1" -->

**The Wrong Tool**
<!-- .element: class="pos-color2" -->

**The Goldfish Agent**
<!-- .element: class="pos-color3" -->

**The Vibes Eval**
<!-- .element: class="pos-color4" -->

</div>

Notes:

- Stuffed: everything crammed upfront, static context does not scale
- Wrong tool: one retrieval method used everywhere
- Goldfish: forgets everything between sessions
- Vibes: shipped because it felt right

---

### Wrong tool, right tool

| You need    | Wrong                  | Right          |
| ----------- | ---------------------- | -------------- |
| Correctness | Similarity search, RAG | Versioned docs |
| Process     | Static reference docs  | Skills         |
| Determinism | LLM reasoning          | Scripts        |
| Judgment    | Scripts, regex         | Reasoning      |

<!-- .element: class="kc-table kc-smaller" -->

Notes:

- RAG returns topically similar, version blind
- Context7-style docs give reference, not how-to
- LLM re-derives the same math every turn, slightly differently
- The regex trap: open-ended input, brittle code

---

### The fixes

| Antipattern    | Fix                                    |
| -------------- | -------------------------------------- |
| Stuffed Prompt | Lazy-load on demand via skills         |
| Wrong Tool     | Route to docs, scripts, rules          |
| Goldfish Agent | External memory you control            |
| Vibes Eval     | LLM writes rubrics, you review, LLM judges |

<!-- .element: class="kc-table kc-smaller" -->

Notes:

- Built-in memory: no versioning, no backup, no portability
- Eval pitfalls: bleeding, leaking, negative scenarios
- You assess the delta, not the vibe

---

### Ship the context

**Docs + Skills + Scripts + Rules**

Versioned. Tested. Distributed.

<!-- .slide: class="is-fancy1" -->

Notes:

- Context becomes an artifact, like a jar
- Same rigor as code: review it, test it, release it
- Takeaway: stop tuning prompts, start shipping context


---

### Like it? See...

![img.png](../../img/05-prompting-antipatterns/img.png)

<!-- .slide: class="is-fancy1" -->
