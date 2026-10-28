# maieutic

**A terminal-native learning harness for [herdr](https://herdr.dev), running on your own Claude Code subscription.**

> *Maieutic* (from Greek *maieutikos*, "of midwifery"): the Socratic method of drawing understanding out of a learner by questioning, rather than pouring it in.

maieutic turns a herdr pane into a tutor that finds out what you actually understand, plans a path to what you want to understand, and teaches it one reasoning step at a time. You predict, it explains, it checks, and anything that didn't land gets repaired before more is built on it. A separate practice mode makes the knowledge usable. Every model call goes through the official `claude` CLI, logged in with your Claude subscription. You don't need any API keys.

Status: **pre-alpha, design phase.** Nothing is runnable yet. See [docs/roadmap.md](docs/roadmap.md).

## What makes it different

Most "AI tutor" setups are a chat window with an encouraging system prompt. maieutic is built around these ideas.

1. **It measures before it teaches.** A prober works out, for each idea the goal depends on, the highest point you reliably get right and the point where you start getting things wrong or don't know. It keeps a record of misconceptions. The lesson starts at the edge of what you already know, not at chapter one.
2. **Your confidence and your reasoning are data, not decoration.** Every question records your answer, how confident you are, and what you were thinking. A wrong answer given with high confidence is read as a committed misconception. A right answer given with confused reasoning is read as a warning sign.
3. **It commits to the answer before asking.** The tutor writes down the correct answer and its explanation before it shows you the question. Wrong options are built to catch specific mistaken beliefs. This is a structural guard against the tutor quietly agreeing with whatever you say.
4. **The aim is "obviously true", not "familiar".** Teaching starts from facts you can accept without caveats, and each step answers the question "how could I have discovered this?". A step is only done when the next one feels inevitable.
5. **Rules enforced by code, not just prompts.** The lesson plan has to be a valid sequence of steps, there is only one question open at a time, and each answer is saved before the tutor's next turn starts. These rules are enforced by a local MCP server, not just by asking the model nicely.
6. **Terminal-native and agent-native.** maieutic lives in the same herdr workspace as your coding agents. Lessons are files you can inspect, background work runs as headless `claude` jobs, and nothing needs a browser.

### Ideas that only work inside herdr (proposed, not yet built)

- **Learn from what your agents just did.** Start a lesson grounded in a neighbouring pane's agent session or diff, for example "explain the concurrency change the agent just made, starting from what I already know".
- **Learn in the codebase you're working in.** Practice problems are drawn from your own repository, not textbook examples.
- **Learn while agents work.** Short practice sessions fill the time while long agent runs finish. herdr can tell when an agent is busy and when it's idle, so it knows when you're free.

## Lineage and license

maieutic adapts the teaching method of [LearnHarness](https://github.com/amosblomqvist/learnharness) by Amos Blomqvist, which is licensed AGPL-3.0. Its prompt doctrine, question schema and validators are the starting point. maieutic is therefore licensed **AGPL-3.0-only**; see [LICENSE](LICENSE) and [NOTICE](NOTICE).

What maieutic changes:
- It drops the Next.js/Electron app and its bring-your-own-API-key engine.
- It runs as a herdr plugin on the official Claude Code CLI.
- It adds terminal-native and agent-aware learning.

## Design

The design rationale lives in the author's knowledge wiki: *LearnHarness adaptation paper* (2026-10-28). It covers a value assessment of LearnHarness and a comparison of a pane-first and a background-first architecture. The recommendation is a staged hybrid:

- an MCP server for the rules above;
- an interactive `claude` tutor in a herdr pane, next to a pane that shows quiz cards;
- headless `claude -p` workers for extracting the knowledge graph and for research.

The roadmap is in [docs/roadmap.md](docs/roadmap.md).
