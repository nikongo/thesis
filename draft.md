# Thesis Draft

## 1. Title of the Degree Project

**Long-Running Autonomous AI Agents: Architecture, Code Quality Integration, and Evaluation with gptme**

_Tentative subtitle: Towards Sustained Agentic Operation Beyond Contained Tasks_

---

## 2. Objectives and Problem Focus

### Background

AI coding agents such as gptme are increasingly capable of solving contained, single-session programming tasks. However, real software engineering work is rarely contained — it unfolds over days or weeks, requires maintaining context across sessions, managing evolving goals, and producing consistently high-quality code without continuous human oversight.

Two open problems are:

1. **Code quality degradation**: Agents operating autonomously tend to accumulate technical debt. There is no established method for integrating continuous code health feedback (e.g. from tools like CodeScene) into the agent decision loop.
2. **Evaluation gap**: Existing agent benchmarks (SWE-bench, gptme-evals, HumanEval) evaluate agents on isolated, short-horizon tasks. No established benchmark exists for sustained, long-running agentic operation.

### Research Questions

**RQ1**: How can code quality analysis (CodeScene) be effectively integrated into an agentic workflow to guide autonomous decision-making and prevent technical debt accumulation?

**RQ2**: What architectural patterns best support autonomous AI agents for long-running, multi-session operation without human intervention — and what are their trade-offs?

**RQ3**: How do different agent architectures compare on long-running, multi-objective tasks, and what metrics are appropriate for evaluating sustained agentic performance?

---

## 3. Solution Approach and Method

### Phase 1 — CodeScene Integration (Implementation + Empirical)

- Implement a CodeScene tool/plugin for gptme that exposes code health signals (hotspots, complexity trends, technical debt) to the agent at decision time.
- Design integration patterns: reactive (agent queries on demand), proactive (injected into context on file edits), and gate-based (blocking commits that worsen health score).
- Evaluate integration patterns qualitatively (agent behavior) and quantitatively (code health metrics before/after).

### Phase 2 — Agent Architecture Design (Design Science)

- Survey and characterize existing long-running agent architectures: persistent memory, task management systems, self-reflection loops, context compression, lesson/constraint systems (as in gptme-agent-template / Gurt).
- Propose and implement 2–3 candidate workflows for sustained operation, varying along the key dimensions below.

#### Candidate Workflows

| Workflow                          | Description                                                                                                                                                                                                |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Baseline (stateless)**          | Standard single-session agent; no memory or task system; restarted fresh for each subtask. Represents current common practice.                                                                             |
| **Structured agent (Gurt-style)** | Persistent task tracker (YAML frontmatter), journal system, knowledge base, lessons system. Context injected from files at session start. Represents structured long-term memory without external tooling. |
| **CodeScene-augmented**           | Structured agent + CodeScene tool integrated into the edit/commit loop. Agent receives code health feedback after file edits and before commits; can decide to refactor before proceeding.                 |
| **Reactive + self-reflection**    | Agent explicitly reflects on its own output at configurable intervals (e.g. after N steps or on CI failure); writes reflections to a lesson file and adjusts strategy.                                     |

These workflows are ordered from least to most autonomous support infrastructure, allowing controlled comparison of what each layer adds.

### Phase 3 — Benchmarking (Empirical Study)

- Design a benchmark suite for long-running tasks — tasks spanning multiple sessions, requiring goal tracking, incremental progress, and quality maintenance. Candidate tasks: implement a feature end-to-end in an unfamiliar codebase, maintain and extend a project over N days, refactor a legacy module while keeping CI green.
- Run agent architectures (with/without CodeScene integration) on the benchmark.
- Define evaluation metrics across four categories (see table below) and compare workflows on the benchmark.

#### Evaluation Metrics

**Productivity**
| Metric | Description |
|--------|-------------|
| Task completion rate | % of benchmark tasks fully completed |
| Lines added / removed | Net and gross code churn per task |
| Commits | Number of commits produced; commit quality (message format, atomic scope) |
| Wall-clock time | Total elapsed time per task |
| Human intervention events | Number of times a human had to unblock the agent |

**Quality**
| Metric | Description |
|--------|-------------|
| Test coverage (Δ) | Change in line/branch coverage from baseline |
| CI pass rate | % of commits that pass the test suite on first push |
| CodeScene health score (Δ) | Change in code health score (hotspots, complexity, duplication) |
| Linting / type errors | Ruff + mypy violations introduced vs. resolved |

**Efficiency**
| Metric | Description |
|--------|-------------|
| Token usage | Input + output tokens per task (and per commit) |
| Cost | Estimated API cost per task |
| Context window utilisation | % of context used; frequency of context compression events |
| Redundant tool calls | Repeated reads/writes with no net change |

**Resilience**
| Metric | Description |
|--------|-------------|
| Session recovery rate | How well the agent resumes after an interrupted session |
| Goal retention | Whether the agent's actions remain aligned with the original task over time |
| Error recovery | Rate of self-correction after tool failures or test failures |

### Methods Summary

| Phase                 | Method                                    |
| --------------------- | ----------------------------------------- |
| CodeScene integration | Implementation + controlled experiment    |
| Architecture study    | Design science + literature review        |
| Benchmarking          | Empirical comparison / quantitative study |

---

## 4. Motivation for Desired Field

Software engineering research increasingly intersects with AI. Autonomous agents are transitioning from research curiosity to practical tools used in industry. Yet the field lacks:

- Principled methods for maintaining code quality under autonomous operation.
- Evaluation frameworks for sustained, realistic agentic work.
- Understanding of which agent architectures are suited to long-horizon tasks.

This thesis sits at the intersection of **software testing/quality** (CodeScene integration, code health metrics), **software development approaches** (ML/DevOps, continuous operation), and **empirical software engineering** (benchmarking, evaluation design) — all active SERG research areas.

gptme is an open-source, actively developed agent framework (Spring 2023 — present), making it an ideal platform: reproducible, extensible, and with an existing eval infrastructure to build upon.

---

## 5. Information About the Company

**Primary project**: [gptme](https://github.com/gptme/gptme) — open-source AI agent framework

- Creator: Erik Bjäreholt / gptme org
- Active community, Discord, PyPI package, production users
- Existing eval suite, plugin system, agent-template (Gurt/Bob)
- Contact: Erik Bjäreholt (maintainer), via GitHub / Discord

**Secondary tool**: [CodeScene](https://codescene.com)

- Code analysis platform focused on behavioral code analysis, technical debt, and code health
- Provides CLI/API access suitable for integration
- Relevant SERG contact: Per Runeson / Emelie Engström (software testing & quality)

**Supervisor**: Markus Borg (markus.borg@cs.lth.se)
**Examiner**: Emma Söderberg (emma.soderberg@cs.lth.se)

---

## 6. Other Information

- **Implementation language**: Python (gptme is Python-based)
- **Artifacts**: CodeScene gptme tool/plugin, long-running benchmark suite, agent architecture implementations — all open-source
- **Reproducibility**: Benchmarks designed to be deterministic and re-runnable; results published alongside code
- **Scope risk**: Long-running benchmarks are time-intensive to run; mitigation is to define a minimal viable benchmark set early and expand if time allows
- **Related work to review**: SWE-bench, SWE-agent, Devin benchmarks, AgentBench, CodeScene research papers, gptme eval suite
