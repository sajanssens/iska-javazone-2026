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

- `git push` triggers Argo Rollouts
- Raise the canary weight a bit at a time
- Check metrics after every step, before raising further

Notes:

- Argo Rollouts is the Kubernetes controller doing the gradual traffic shift
- Alternatives exist too, e.g. Flagger
- Each step queries a metric (originally hand-written PromQL) against a threshold
- All steps pass, promote to stable, any step fails, abort and roll back

---

### Where AI comes in
![Canary flow with an AI analysis step deciding promote or rollback](img/01-canary-rollback-ai/ai-canary-flow.png)


- This isn't AI replacing the rollout strategy, it's replacing the manual "is this metric OK" judgment call.
  - Hand-written PromQL thresholds are brittle
  - Replace the metric check with an AI analysis step
  - Same loop, smarter judgment call

<!-- .element class="fragment" -->


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

> Rolling out to everyone at once is risky
<!-- .element class="fragment" -->

> Canary rollouts make bad releases cheap to catch
<!-- .element class="fragment" -->
 
> AI can read the metrics and logs for you
<!-- .element class="fragment" -->

> GitOps ties it together: promote or roll back, automatically
<!-- .element class="fragment" -->


---

### Like it? See...

![img.png](../../img/01-canary-rollback-ai/img.png)

<!-- .slide: class="is-fancy1" -->
