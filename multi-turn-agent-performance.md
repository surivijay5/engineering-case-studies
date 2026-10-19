# Performance Engineering for Multi-Turn AI Agents

## Designing a Performance Testing Strategy for Multi-Turn AI Agent Workflows

### Overview

I worked on performance validation for multi-turn AI workflows where the visible user interaction depended on several system layers:

```text
UI
 ↓
streaming / gateway
 ↓
agent host
 ↓
tools
 ↓
backend services
 ↓
external dependencies
```

Traditional API load testing was not enough.

A journey could succeed technically while still failing the user experience—for example, if an expected action did not appear in the live stream even though the final state was later persisted correctly.

I helped turn this ambiguity into a reusable testing approach and executable performance suites.

### The Problem

Multi-turn agent workflows introduced several performance-testing complications:

- Later steps depended on state generated earlier
- Tool execution could add variable latency
- The UI received incremental streamed responses
- History/recovery behavior could differ from live behavior
- Multiple services contributed to completion time

Testing a single endpoint could therefore give a misleading picture.

### Testing Model

```text
Request admitted
      ↓
First useful feedback
      ↓
Intermediate agent/tool activity
      ↓
Required user interaction
      ↓
Next turn
      ↓
Workflow completion
```

Different measurements answer different questions.

#### Time to First Useful Feedback

Does the user know that useful work has started?

#### Turn Latency

How long does one interaction take?

#### End-to-End Completion

How long does the entire business workflow take?

#### Concurrency Behavior

Does the workflow remain correct when multiple journeys execute simultaneously?

### Important Discovery

During concurrency testing, I found a case where an expected confirmation action was unavailable on the **live streamed interaction**, even though the reconstructed history later contained the correct interactive state.

I reproduced the issue under load and documented the difference between expected and actual behavior for follow-up investigation.

That exposed an important testing principle:

> **A correct persisted result does not prove that the live user journey was correct.**

Both paths need validation.

### Test Strategy

#### Journey-Level Validation

Test the hardest realistic multi-turn flow under representative concurrency.

#### First-Feedback Measurement

Measure when the user receives meaningful progress—not merely when the server accepts the request.

#### Cross-Layer Isolation

When latency or correctness fails, isolate the boundary:

```text
Frontend?
Streaming?
Host?
Tool execution?
Backend?
External dependency?
```

#### Functional + Performance Validation Together

A performance test should not merely verify response time.

It should also verify that expected workflow state and interactions remain available.

### Tooling

I contributed reusable k6 performance suites and a shared performance-testing framework for multi-turn agent features.

The broader testing strategy included:

- Browser workflow validation
- API/service checks
- Contract validation
- k6 load testing
- Hands-on development testing

### Outcome

The work helped establish a common way to reason about:

- Agent capacity
- Concurrency
- First-feedback latency
- Multi-turn correctness
- Dependency behavior
- Live-path versus history-path validation

It also surfaced a live concurrency issue before customer impact.

### What I Learned

Agent performance should not be reduced to:

> “How long did the API take?”

A production agent experience has several important boundaries:

**Feedback, state progression, tool execution, interaction availability, and completion.**

The most useful performance tests reproduce the actual customer journey and verify both **speed and correctness under concurrency**.

### Technologies / Areas Involved

- k6
- Playwright
- JavaScript / TypeScript
- Streaming systems
- Agentic workflows
- Performance testing
- API/service validation
- Concurrency analysis

### Confidentiality Note

This case study describes the engineering approach at a high level. Internal product names, performance targets, infrastructure details, APIs, customer workloads, test datasets, and proprietary workflow behavior have been removed or generalized.
