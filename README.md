<img src="assets/banner.svg" alt="M&A Deal-Flow Platform — NDA & deal flow, state-machine enforced" width="100%">

# M&A Deal-Flow Platform

**A deal cannot advance without the required fields logged, a signature request cannot fail silently, and every state change is auditable after the fact.**

![running on my own n8n](https://img.shields.io/badge/status-running%20on%20my%20own%20n8n-2F6B52?style=flat-square) ![availability](https://img.shields.io/badge/availability-ready%20to%20deploy-5B6472?style=flat-square) ![sector](https://img.shields.io/badge/sector-M%26A%20advisory-12151B?style=flat-square) ![built with](https://img.shields.io/badge/built%20with-n8n-12151B?style=flat-square) ![Workflows](https://img.shields.io/badge/Workflows-6-5B6472?style=flat-square) [![honesty check](https://github.com/mdsadrhoman123-stack/edugrow-ma-platform/actions/workflows/honesty-check.yml/badge.svg)](https://github.com/mdsadrhoman123-stack/edugrow-ma-platform/actions/workflows/honesty-check.yml)

<img src="assets/demo.gif" alt="M&A Deal-Flow Platform: one execution running step by step, a step failing on purpose — held and escalated, in red — then that same step handled and the run completing" width="100%">

<p align="center">6 workflows · <b>6 failure paths, every one alerted</b> · 5 retry attempts, exponential backoff · every action audit-logged</p>

**One execution, with a failure injected into it on purpose — and the work still finishing.** That is a recording of [`docs/index.html`](docs/index.html) in this repository — one file, no build step, no network — running its own steps, not an illustration of them. The red step is the interesting one: it is held, someone is told, and then it is dealt with and the run carries on. Nothing fails quietly. Open the page yourself and **Break it** lights every failure path at once, each carrying what happens next.

> [!NOTE]
> **What this is.** A production-grade system built to a brief that businesses in this sector post publicly, in their own words — the problem exactly as they stated it, not one invented to demonstrate something. It was engineered the way anything a business actually depends on has to be: the failure paths designed before the features, every one of them logged and alerted rather than left to chance. It runs on my own infrastructure. It is ready to deploy for any business with this problem, and it has not been sold or deployed into a customer's business yet.

| | |
| :--- | :--- |
| **Built for** | Boutique M&A advisory firms |
| **The brief** | The problem exactly as businesses in this sector post it — public job briefs on Upwork and Fiverr, in their words, not my framing |
| **Industry** | M&A advisory |
| **Status** | running on my own n8n |
| **Failure paths designed** | 6 — each with how it is detected, what the system does about it, and who finds out |
| **My role** | Sole engineer — scoping, architecture, build, failure design and operation |
| **Availability** | Ready to deploy for any business with this problem — built once as a product, not as a one-off. Running on my own infrastructure; not sold yet. |

---

### On this page

[The problem](#the-problem) · [What changed](#what-changed) · [What the client gets](#what-lands-in-front-of-the-client) · [How it works](#how-it-works) · [The shape of it](#the-shape-of-the-system) · [When it breaks](#when-it-breaks) · [Why this way](#why-it-is-built-this-way) · [Limitations](#honest-limitations) · [What is here](#what-is-in-this-repository) · [Read deeper](#read-deeper)

---

## The problem

A boutique M&A advisory firm needed its NDA and deal-flow process to run without a human watching every step.

But a dropped signature request or a silent failure in a live deal costs a client's trust, or the deal itself. Off-the-shelf automation tools do not give this kind of visibility or recovery.

So the build started from the failure cases rather than the happy path.

## What changed

| | Before | After |
| :--- | :--- | :--- |
| **Deal advancement** | Whenever someone moved it | Blocked until required fields are logged |
| **A failed signature** | Discovered by asking | Retried five times, then dead-lettered and alerted |
| **Lost work** | Possible | Dead-letter table — nothing disappears |
| **Tracing a deal** | Search email | Correlation ID through every step |
| **Alerting** | One channel, or none | Two channels |

<sub>Before/after describes the change in process, not benchmarked throughput. Where a number is not measured, it is not claimed.</sub>

## What lands in front of the client

<img src="assets/output.png" alt="M&A Deal-Flow Platform: sample of the output a client receives, including one step that failed and what happened instead" width="100%">

**Sample output — every figure and every person in it is invented for the illustration.** No client data exists in this repository in any form: not anonymised, not sampled, not a redacted screenshot. What is not invented is the shape of it — the steps are this project's own, and the step called out mid-way is one row of the failure table further down this page, with what happened instead. It is a rendered image rather than markup for the same reason the internal build is not published: the output is a template, and a template in markup is a template anyone can paste into their own project and run.

## How it works

A versioned PostgreSQL schema with state-machine enforcement, full audit logging, dead-letter tables and idempotency guarantees. The NDA workflow retries a failed send up to five times with exponential backoff, threads a correlation ID through every step, and raises dual alerts the moment a human is needed.

<table>
<tr>
<td width="42" valign="top" align="center"><b>01</b></td><td valign="top"><b>A deal is entered</b><br>Intake writes to a versioned schema, not a spreadsheet.</td>
</tr>
<tr>
<td width="42" valign="top" align="center"><b>02</b></td><td valign="top"><b>The state machine decides</b><br>A deal cannot move to the next stage without its required fields. Blocked, not warned — that distinction is the product.</td>
</tr>
<tr>
<td width="42" valign="top" align="center"><b>03</b></td><td valign="top"><b>The NDA goes out</b><br>And if the send fails it is retried five times with exponential backoff, not assumed successful.</td>
</tr>
<tr>
<td width="42" valign="top" align="center"><b>04</b></td><td valign="top"><b>Failure has somewhere to go</b><br>After five attempts the item lands in a dead-letter table and two alert channels fire. Nothing evaporates.</td>
</tr>
<tr>
<td width="42" valign="top" align="center"><b>05</b></td><td valign="top"><b>Everything is traceable</b><br>A correlation ID runs through every step, so one deal's whole path can be reconstructed months later.</td>
</tr>
<tr>
<td width="42" valign="top" align="center"><b>06</b></td><td valign="top"><b>Re-running is safe</b><br>Idempotency guarantees mean nothing runs twice.</td>
</tr>
</table>

### How it flows

<sub>What happens to the client's work, in the order they experience it. The internal build — node graph, execution order, prompts, thresholds — is deliberately not published.</sub>

```mermaid
flowchart LR
    in(["A deal is entered"])
    gate{"Is everything required present?"}
    blk["No → blocked, not merely warned"]
    sig["Agreement goes out"]
    ok["Signed → the deal advances"]
    lost["Send failed → nothing lost, two people told"]

    in --> gate
    gate -.-> blk
    gate --> sig
    sig --> ok
    sig -.-> lost

    classDef default fill:#F8F7F3,stroke:#12151B,stroke-width:1px,color:#12151B;
    classDef ok fill:#2F6B52,stroke:#12151B,stroke-width:1px,color:#F5F4EF;
    classDef bad fill:#FEE2E2,stroke:#DC2626,stroke-width:1.5px,color:#7F1D1D;
    class ok ok;
    class blk,lost bad;
```

<details>
<summary><b>What the shapes mean</b> — colour is not the only signal</summary>

| Shape | Means |
| :--- | :--- |
| **rounded** | Where the client's process starts |
| **box** | Something the system does |
| **diamond** | A decision point |
| **slanted** | A person has to act |
| **green box** | The good outcome |
| **red box** | Failure path — held, escalated or alerted |

Red appears in exactly one role across every repo in this portfolio: where failure goes. Nowhere else. If you see red, something is being held, escalated or alerted.
</details>

> **Walk it interactively** — [`docs/index.html`](docs/index.html) is a single self-contained page. Download it, open it in any browser, and it executes one run in front of you — failure included. **Run it again** gives you a clean pass, **Break it** lights every failure path at once. Nothing to install, no network calls.

## The shape of the system

Parts and the role each one plays. Not the wiring — no execution order, no prompt text, no thresholds. That is a deliberate line, and the last branch of the tree names exactly what sits on the other side of it.

```text
M&A Deal-Flow Platform — the running system
│
├── Interfaces ...................... the systems it talks to
│   ├── DocuSign API ................ The NDA send, retried rather than assumed
│   └── Custom buyer portal (web) ... Where the advisory team works the pipeline
│
├── Judgement ....................... where a decision or a piece of writing is made
│   └── OpenAI GPT-4 ................ Document handling inside the deal workflows
│
├── Memory .......................... what is remembered, and for how long
│   └── PostgreSQL .................. Versioned schema, state machine, audit log and dead-letter tables
│
├── Oversight ....................... how a human stays in the loop
│   └── Telegram + Email ............ Two alert channels, because one channel is a single point of failure
│
├── Ground .......................... what the whole thing runs on
│   └── n8n ......................... Six workflows covering the deal lifecycle
│
├── Failure design .................. 6 paths, designed before the features
│   ├── detected by ................. an error output, a timer, or a failed connection
│   ├── handled by .................. falling back, holding, or halting — never guessing
│   └── announced to ................ a named person, with the reason attached
│
└── Not in this repository .......... the part that would let you skip the thinking
    ├── the node graph .............. which part runs after which, and on what condition
    ├── the prompts ................. wording, guardrails, the shape of the output
    ├── the thresholds .............. what counts as urgent, late, at capacity, a match
    └── the credentials ............. never committed, in any form, at any point
```

Read it as a set of decisions rather than a parts list. Every part is there because a specific failure or a specific constraint put it there, and the two sections below are the same story told twice: **When it breaks** is what each part is defending against, and **Honest limitations** is what it costs to have chosen that part and not another.

### Counted, not estimated

| | |
| :--- | :--- |
| Workflows | **6** |
| Retry attempts | **5, exponential backoff** |
| Alert channels | **2** |
| Schema version | **v2** |
| Actions audit-logged | **every one** |

<sub>These are counts from the built system — nodes, stages, versions, gates. No efficiency percentages are published here without a stated measurement method.</sub>

## When it breaks

Most automation portfolios show you the happy path. The happy path is the easy half. This is the half that decides whether a system survives contact with a real business.

| What goes wrong | How it is detected | What the system does | Who finds out |
| :--- | :--- | :--- | :--- |
| **Required field missing** | State-machine guard | Deal cannot advance — blocked, not warned | Blocked state is visible and logged |
| **Signature send fails** | API error | Retried up to five times with exponential backoff | Alert only if all five fail |
| **All retries exhausted** | Retry count | Written to a dead-letter table, never dropped | Dual alert: Telegram and email |
| **Same action submitted twice** | Idempotency guarantee | Second submission does not re-run the step | Nobody — by design |
| **Tracing a problem after the fact** | Correlation ID threaded through every step | The whole path for one deal can be reconstructed | Whoever asks |
| **One alert channel is down** | Dual delivery | The other channel still lands | The human still finds out |

The default on an unhandled condition is to **stop and tell someone** — never to continue on a guess. A silent success is the failure mode that costs the most, because nobody goes looking for it.

## Why it is built this way

Three decisions, each with the option that was turned down and the price of turning it down. A choice with no cost attached to it was not a choice — it was a default, and defaults are not worth reading about.

<details open>
<summary><b>Why a state machine instead of a status field</b></summary>

**What it does.** A versioned schema where a deal can only move along transitions that are permitted, with the audit log written as it moves.

**What was turned down.** A status column any part of the system can set. Flexible, and every team asks for it — and it lets a deal arrive at signature with required fields still empty, which is the failure that costs a firm a transaction.

**What that costs.** Strict by design. A firm that wants to advance a deal with fields missing will find it obstructive, which is the point. The schema is at v2, so migrations are a real operational step rather than an afterthought.

</details>

<details>
<summary><b>Why a failed NDA send retries five times and then dead-letters</b></summary>

**What it does.** Exponential backoff, a correlation ID threaded through every step, and a dead-letter table when it still will not go.

**What was turned down.** Sending and assuming it worked. The common case is fine — and the failure is silent, and a silent failure here means a signature nobody ever chased.

**What that costs.** Dead-lettered items need a person to work the queue. The system guarantees nothing is lost; it does not guarantee that nothing needs attention.

</details>

<details>
<summary><b>Why there are two alert channels</b></summary>

**What it does.** Anything that needs a human is announced on Telegram and by email, both, every time — not one with the other as a fallback.

**What was turned down.** One channel. Less noise and one integration to keep credentialed — and one channel is a single point of failure on the one day it matters.

**What that costs.** Duplicate alerts to live with, and two integrations to keep credentialed.

</details>

Every cost above also appears in **Honest limitations** below. It is there twice on purpose: once as the reasoning, once as the consequence, so neither can be quietly dropped from the other.

## Honest limitations

Every design decision costs something. These are the trade-offs in this build, stated by the person who made them.

- The state machine is strict by design. A firm that wants to advance a deal with fields missing will find it obstructive — which is the point.
- Dead-lettered items need a human to work the queue. The system guarantees nothing is lost, not that nothing needs attention.
- Schema is versioned at v2, so migrations are a real operational step rather than an afterthought.

## What is in this repository

Every file, and the question it answers. Same layout in all eleven repositories in this portfolio, so the second one you open needs no orientation at all.

```text
edugrow-ma-platform/
├── README.md ....................... ← you are here
├── SECURITY.md ..................... how to report something that should not be public
├── NOTICE.md ....................... what is withheld, and why
├── LICENSE ......................... covers the documentation, not a software grant
│
├── docs/ ........................... the long form — read in order or not at all
│   ├── index.html .................. the interactive demo, one file, no network
│   ├── 01-problem.md ............... the situation before, in full
│   ├── 02-journey.md ............... step by step, from their side
│   ├── 03-architecture.md .......... the diagrams, and why they are shaped that way
│   ├── 04-failure-handling.md ...... every failure path, and where it lands
│   ├── 05-stack.md ................. each choice, the option turned down, the cost
│   ├── 06-results.md ............... what is measured, and what is deliberately not
│   └── 07-limitations.md ........... the trade-offs, in detail
│
├── diagrams/ ....................... source, so the flow can be re-rendered
│   ├── pipeline-lr.mmd ............. the client-level flow, left to right
│   └── pipeline-tb.mmd ............. the same flow, top to bottom
│
├── assets/ ......................... local files only — nothing from a CDN
│   ├── banner.svg .................. the header on this page
│   ├── demo.gif .................... the recording at the top of this page
│   ├── output.png .................. a sample of what the client receives
│   └── cta.svg ..................... the closing card
│
├── workflows/ ...................... empty on purpose — see below
│   └── README.md ................... why it is empty, in writing
│
└── .github/ ........................ the badge at the top of this page
    ├── honesty-check.py ............ the claim linter it runs
    └── workflows/
        └── honesty-check.yml ....... runs it on every push
```

There is no `src/` in that tree, and no `workflows/*.json`. That is not an omission — it is the design, and the next section says exactly what is being withheld and why.

## What is not in this repo

- **Data belonging to a real business.** None, in any form. Not anonymised, not sampled — there never was any.
- **Credentials and endpoints.** Never committed. See [`NOTICE.md`](NOTICE.md) for what is withheld, and [`SECURITY.md`](SECURITY.md) for how to report anything that slipped through.
- **The workflow itself.** No exports, no node graph, no execution order, no prompts, no scoring thresholds, no integration wiring — not sanitised, not partial, not in a screenshot. That is the build, and the build is not portfolio material.

This repository documents *how the problem was thought about* — the failure paths, the trade-offs, the reasoning. That is what tells you whether to hire someone. A copy of the wiring would not.

This is a portfolio repository documenting a system I designed and built. It is not a product you can clone and run against your own accounts.

## Read deeper

| | |
| :--- | :--- |
| [01 · The problem](docs/01-problem.md) | The situation before, in full |
| [02 · The journey](docs/02-journey.md) | Step by step, from their side |
| [03 · Architecture](docs/03-architecture.md) | Diagrams and the reasoning |
| [04 · Failure handling](docs/04-failure-handling.md) | Every path, and where it lands |
| [05 · The stack](docs/05-stack.md) | What was chosen and what was rejected |
| [06 · Results](docs/06-results.md) | What is measured and what is not |
| [07 · Limitations](docs/07-limitations.md) | The trade-offs, in detail |

---

<img src="assets/cta.svg" alt="If a process depends on someone noticing when it breaks, that is the problem I work on." width="100%">

### Tell me what the process is

I will tell you honestly whether automating it is worth your money — including when the answer is no.

**K MD SAYAD RAHMAN** — AI Automation Engineer  
n8n · AI agents · production reliability  
[khandokarsayad@gmail.com](mailto:khandokarsayad@gmail.com) · [mdsadrhoman123@gmail.com](mailto:mdsadrhoman123@gmail.com) · [LinkedIn](https://www.linkedin.com/in/khandokarsayad) · [More systems](https://github.com/mdsadrhoman123-stack)

