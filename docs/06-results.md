# 06 · Results

---

## Counted

| | |
| :--- | :--- |
| Workflows | **6** |
| Retry attempts | **5, exponential backoff** |
| Alert channels | **2** |
| Schema version | **v2** |
| Actions audit-logged | **every one** |

These are counts from the built system: nodes, stages, versions, gates, retries. They are verifiable from the workflow itself.

## What changed in the process

| | Before | After |
| :--- | :--- | :--- |
| **Deal advancement** | Whenever someone moved it | Blocked until required fields are logged |
| **A failed signature** | Discovered by asking | Retried five times, then dead-lettered and alerted |
| **Lost work** | Possible | Dead-letter table — nothing disappears |
| **Tracing a deal** | Search email | Correlation ID through every step |
| **Alerting** | One channel, or none | Two channels |

## What is deliberately not claimed

No time-saved percentage, cost-reduction figure or throughput multiplier appears in this repository. Those numbers require a measured baseline and a measured after, over a stated period, on a stated definition. Where that measurement exists it will be published with its method. Where it does not, the number is not worth more than the process description above.

> An unsourced percentage in a portfolio is a claim the reader has to take on trust. A node count is a claim they can check.

---

[← 05 · The stack](05-stack.md) · [07 · Limitations →](07-limitations.md)
