# AI PM Playbook
### Field notes from shipping agentic AI in production

This repo captures what I'm learning as a product manager working at the intersection of enterprise AI and real-world deployments. No theory for its own sake. Everything here comes from decisions made, things that broke, and what we figured out after.

---

## What this is

A running record of patterns, failure modes, and frameworks I've found useful while managing AI products in production. The goal is to give other AI PMs a head start, not a blueprint, because every deployment is different. Take what's useful, ignore what isn't.

---

## The problem that started this

We were deploying a multi-agent AI system for an enterprise HR use case. The individual agents performed well in isolation. Once we wired them together under an orchestrator, things got unpredictable. The orchestrator would forget decisions made by upstream agents mid-workflow, freezing the entire case. Randomly. Not always. Not in a reproducible pattern.

That randomness is the real enemy in agentic systems. Deterministic bugs are straightforward to fix. Random failures at the orchestration layer are a different class of problem.

Here is what we learned.

---

## Field Notes: Multi-Agent Orchestration

### 1. Agents can reason well individually. State continuity across handoffs is a different problem entirely.

When you run a single agent against a well-scoped task, modern LLMs are remarkably capable. The gap shows up the moment you chain agents together. The orchestrator coordinates them, but coordination requires passing state, and state passing is where things fall apart.

The core failure mode: Agent A makes a decision. Agent B needs that decision to proceed. The orchestrator, relying on its in-context memory, fails to carry it forward cleanly. The workflow freezes.

This is not a bug in any one agent. It is an architectural problem in how state is managed between them.

**The industry term for this:** context loss at handoff. As of early 2026, it remains one of the most common failure modes in production agentic systems.

---

### 2. Prompt engineering buys you short-term reliability gains. It does not solve the underlying problem.

We reduced orchestration failures significantly by adjusting workflow prompts. That works, and it is worth doing. But it is not durable.

The reason it works: LLM-based orchestrators are heavily prompt-dependent. How you describe the handoff conditions, how explicitly you tell the orchestrator to recall prior decisions, how you structure the sequence of instructions — all of this affects whether state survives the transition between agents.

The reason it is not enough: you are asking a probabilistic model to behave deterministically. On most runs it will. On some runs it will not. At scale, "some runs" is a lot of cases.

The durable fix is architectural: decisions should be persisted to an external state store rather than living in the orchestrator's context window. The orchestrator reads from that store rather than trying to recall from memory.

---

### 3. Random failures almost always point to state management or timing, not logic errors

In our deployment, two failure categories emerged:

**Orchestrator memory loss:** The criticality decision made by one agent was not retained by the orchestrator when routing the next step. Random in occurrence. Reduced but not eliminated by prompt adjustments.

**User context mis-attribution:** Work notes were written under the wrong user identity in some cases. The agent produced the right output; the platform wrote it under the wrong actor. This was a platform-level regression, not an agent logic error.

Both failures shared the same signature: they happened some of the time, not all of the time, with no clear trigger. That randomness is the diagnostic signal. It points to:

- Context window pressure (long workflows exceeding the token limit quietly, causing unpredictable drops)
- Asynchronous timing (agent responses arriving slightly out of sequence)
- Session context inheritance (the orchestrator picking up the triggering user's session instead of the designated AI user)

When you see random failures in agentic workflows, start by ruling out these three before debugging agent logic.

---

### 4. Defining a UAT threshold is a PM responsibility, not the customer's

When handing an agentic deployment to a customer for UAT, you need to walk in with a stated acceptable performance threshold. If you do not, the customer will set one, and it will be higher than what the system can reliably deliver.

The instinct is to let the customer define success. Resist it. You know the system's failure modes better than they do. You know which failures are platform-level (outside your control) and which are agent logic failures (within your control). Those two categories should be measured separately.

What we landed on: define the pass rate excluding known platform defects, so the accuracy measurement reflects the agent's actual reasoning quality on cases it successfully processes. Flag platform defects separately with remediation timelines.

This framing protects you from being held to a single aggregate number that conflates things you can fix with things you cannot.

---

### 5. The current state of the industry (as of April 2026)

The field has converged on a few things worth knowing:

**Context engineering has displaced prompt engineering as the primary discipline.** The recognition is that most agent failures are context failures, not model failures. The model reasons fine. It just did not have the right information at the right moment.

**Strictly typed handoffs are now considered table stakes.** If Agent A passes a decision to Agent B, that handoff should use a rigid schema, not natural language summarized in a prompt. Validation logic should reject improperly formatted state at each transition.

**External state stores are the architectural answer.** Frameworks like LangGraph externalize state to a persistent object that all agents read from and write to. The orchestrator does not need to remember because state lives outside the model entirely.

**The numbers are sobering.** As of early 2026, only 2% of organizations have deployed agentic AI at scale. Over 60% are still in exploration. Gartner predicts more than 40% of agentic AI projects will be canceled by end of 2027 due to cost, unclear value, or inadequate risk controls. If you are in production, you are ahead of most. That does not mean it is easy.

---

### 6. What enterprise platforms are doing about it

The major enterprise AI platforms are aware of this problem and actively investing in architectural solutions. The common direction: move from in-context orchestration memory to platform-managed state, where the database or a dedicated memory layer holds decisions between agent steps.

The practical implication for PMs: ask your platform vendor specifically how state is persisted between agent handoffs. "The orchestrator manages it" is not an answer. You want to know whether decisions are written to a structured store between steps, or whether the orchestrator is relying on its token window to carry context forward. Those are very different reliability profiles.

---

## Broader PM frameworks I keep returning to

**Measure what you control separately from what you do not.** In agentic deployments, platform defects and agent logic failures are different failure categories. Aggregate pass rates obscure which one you are dealing with.

**Randomness is a diagnostic signal, not just a nuisance.** Deterministic failures have logic causes. Random failures point to state, timing, or context. The right debugging path is different for each.

**Prompt changes are a tactic, not a strategy.** Iterate on prompts to improve reliability short-term. Invest in architecture to stabilize it long-term. Do not confuse one for the other.

**Go into UAT owning the threshold.** Define acceptable performance before the customer does. Separate platform issues from product issues in how you communicate results.

---

## What's coming

I'll keep adding to this as deployments progress. Areas I'm actively tracking:

- Evaluation frameworks for agentic AI output quality (not just pass/fail, but qualitative accuracy)
- How to structure LLM-as-judge approaches for automated testing
- UAT design for AI products: what's different from traditional software UAT
- How to communicate AI failure modes to non-technical enterprise stakeholders

---

## About

I'm a product manager working on enterprise AI adoption. This repo is a personal learning log, not affiliated with any employer. Feedback welcome via issues or PRs.

---

*Last updated: April 2026*
