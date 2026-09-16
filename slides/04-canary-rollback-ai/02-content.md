### Ship it, but not to everyone at once

![img.png](../../img/01-canary-rollback-ai/img2.png)

Notes:
- Default deploy: big bang, all or nothing
- One bad release, all users feel it
- Progressive delivery: roll out gradually instead

---

### Canary rollout, step by step

![Canary rollout flow with Argo Rollouts](img/01-canary-rollback-ai/canary-flow.png)
<!-- .element: class="kc-smaller" -->

- <!-- .element: class="fragment" -->
  `git push` triggers Argo Rollouts
- <!-- .element: class="fragment" -->
  Raise the canary weight a bit at a time
- <!-- .element: class="fragment" -->
  Check metrics after every step, before raising further

Notes:

- Argo Rollouts is the Kubernetes controller doing the gradual traffic shift
- Alternatives exist too, e.g. Flagger
- Each step queries a metric (originally hand-written PromQL) against a threshold
- All steps pass, promote to stable, any step fails, abort and roll back

---

### Where AI comes in
![Canary flow with an AI analysis step deciding promote or rollback](img/01-canary-rollback-ai/ai-canary-flow.png)


- <!-- .element: class="fragment" -->
  This isn't AI replacing the rollout strategy, it's replacing the manual "is this metric OK" judgment call.
  - <!-- .element: class="fragment" -->
    Hand-written PromQL thresholds are brittle
  - <!-- .element: class="fragment" -->
    Replace the metric check with an AI analysis step
  - <!-- .element: class="fragment" -->
    Same loop, smarter judgment call


Notes:

- "Write custom PromQL" vs "use AI to analyze failures", the talk's own framing
- The AI looks at logs and metrics together, not just one threshold
- Failure path already opens a rollback, before a human even looks at a screen

---

### Under the hood, an agent pipeline

![Agentic architecture: trigger, parallel data collection, analysis loop, response, async remediation](img/01-canary-rollback-ai/agentic-architecture.png)
<!-- .element: class="kc-smallest" -->

<!-- .slide: class="is-empty" -->

Notes:

- Trigger: Argo Rollouts calls a metric plugin during the canary step
- Parallel data collection: separate agents pull logs, error rates, latency, memory
- Analysis loop: one agent analyzes, another scores the analysis, retries if low quality
- Response: promote or rollback decision, returned synchronously so the rollout isn't blocked
- Async remediation, branches on root cause: a code bug gets a generated patch and a PR, an operational issue gets a GitHub issue instead

---

### Takeaways

> Let AI judge the metrics, not a brittle script.

---

### Like it? See...

![img.png](../../img/01-canary-rollback-ai/img.png)

<!-- .slide: class="is-fancy1" -->
