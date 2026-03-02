---
title: The Harness is the Product
date: 2026-03-02
---

LangChain published results this week that I keep thinking about.

They took a coding agent from 52.8% to 66.5% on Terminal Bench 2.0 — a 13.7 point jump that moved them from outside the Top 30 to Top 5. They did not change the model. Same GPT-5.2-Codex throughout. The only thing they changed was what they call the harness — the system of prompts, tools, middleware, and context that wraps around the model.

That result is clarifying in a way that most AI benchmarks are not.

---

There is a reflex in the industry right now to treat the model as the variable that matters. When something does not work, the answer is usually: try a better model, wait for the next release, switch providers. The model is the product. Everything else is scaffolding.

The LangChain result suggests the opposite is closer to true.

What they actually changed:

They added a structured build-verify loop to the system prompt. The agent had been writing code, glancing at it, deciding it looked fine, and stopping. No tests. No comparison against the actual task specification. A middleware hook now intercepts the agent before it exits and forces a verification pass. The agent cannot mark itself done without checking its own work.

They injected environment context at startup. A middleware runs before the agent takes any action and maps the directory structure, finds installed tools, identifies available runtimes. Context the agent would have wasted time discovering — or gotten wrong — is delivered before it needs it.

They added doom loop detection. When an agent commits to a broken approach, it tends to stay committed. They tracked per-file edit counts. After too many edits to the same file, the agent receives a prompt asking it to reconsider its approach entirely. A simple heuristic, but it broke a pattern that was showing up across dozens of traces.

They managed reasoning budgets. Reasoning models can think longer before responding, but more thought burns more tokens and more time. Running at maximum reasoning on every step caused timeouts. Their solution: high reasoning for planning and final verification, medium for execution. Spend cognitive budget where it changes outcomes, conserve it where it does not.

---

None of these are model improvements. They are engineering decisions about how the model encounters a problem.

This is worth sitting with. The model is a black box. You can observe its inputs and outputs, and you can see in traces where it went wrong, but you cannot directly fix the reasoning. What you can fix is the context it receives, the constraints it operates within, the checkpoints that catch failure before it propagates.

Harness engineering is what you do when you accept that the model is not going to get smarter on your timeline, but the system around it absolutely can.

There is a useful parallel to software engineering. In 1999 no one debated whether computers were fast enough to run a good web product. The constraint was architecture — how you structured the system, where you put the logic, what you cached, what you deferred. Raw compute was a given. The engineering was the differentiator.

We are in roughly that position with AI models now. Intelligence is increasingly abundant and accessible. GPT, Claude, Gemini — every company has access to the same underlying capabilities. What differs is the harness: the system prompt that shapes how the model approaches problems, the tools it can reach for, the middleware that catches its predictable failure modes, the observability that tells you where to look when something goes wrong.

The companies that are quietly building advantage in AI right now are not the ones with the best model access. They are the ones with the best harness engineering — the teams running trace analysis loops, building middleware for known failure patterns, iterating on context injection, tuning reasoning budgets. The boring operational work that does not make for a good demo but compounds over time.

---

The practical takeaway for anyone building with agents today: trace everything. If you do not know why your agent failed, you cannot improve it. Start there. Run the same task against traces and find the pattern in the failures. Then fix the harness, not the model selection.

The model is not the product. The system is.
