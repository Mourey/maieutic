# Roadmap

Derived from the LearnHarness adaptation paper (wiki, 2026-10-28), §6–§8. Effort figures are rough focused-hours estimates.

## Phase 0: spikes (about 4–6 h)

Each spike answers one question; the answer decides a design point.

| ID | Question | Decides |
|---|---|---|
| S1 | Does `herdr agent start <name> --kind claude --pane <P> -- --plugin-dir … --agent tutor --mcp-config … --session-id <uuid>` work, and does `--agent` resolve a plugin agent? | Launcher shape |
| S2 | Does the interactive Claude Code pane show MCP tool *inputs*, so the correct answer would be visible? Can that be suppressed? | Answer secrecy; whether we need the background driver front end |
| S3 | Does `herdr agent prompt` accept a ~2 KB single-line JSON answer payload? | Answer channel |
| S4 | Does a PostToolUse hook returning `{"continue": false}` end the turn after an MCP tool call? | One question per turn |
| S5 | Does `[[events]]` with `pane.agent_status_changed` fire when the tutor goes idle? Can a UserPromptSubmit hook block typed input while a question is open? | Waking the tutor when jobs finish; one input surface |
| S6 | Can `npx tsx` import LearnHarness's pure core (`question-core.ts`, `plan-tree.ts`, `mermaid-plan.ts`, `phases.ts`, `graph.ts`) cleanly? | Reuse vs. reimplement |
| S7 | Does the hidden `--max-turns` flag work? Does `--max-budget-usd` do anything on a subscription? | Limits on background workers |
| S8 | Where does `HERDR_PLUGIN_STATE_DIR` point? Does a `--no-focus` tab survive the client detaching? | Data layout; background tutor |

## Phase 1: MVP, probe → plan → teach (about 3–5 days)

- **Pieces:**
  - `maieutic-mcp` with the tools `ask`, `probe`, `quiz`, `explain`, `recall`, `plan_init`, `probe_done`;
  - the prompt composer;
  - the launcher action;
  - the quiz-card pane;
  - read-only access to the knowledge graph.
- **Acceptance:**
  - Contract tests accept and reject the same inputs LearnHarness's validators do: cyclic plan, unknown node, a second open question, a repeated `probe_done`, and a correct answer that isn't one of the options.
  - A real session passes `maieutic verify`.
  - No `pending.json` ever contains `correctAnswer`.
  - Every answer is saved before the next turn.
  - Killing the pane and resuming continues the lesson.
  - No child process has `ANTHROPIC_API_KEY` set or `--bare` passed.

## Phase 2: background workers and the knowledge graph (about 3–4 days)

- **Pieces:**
  - extraction, audit and merge as headless `claude -p --json-schema` workers, under a file lock;
  - research jobs;
  - waking the tutor when a job finishes;
  - recovery at startup;
  - herdr notifications.
- **Acceptance:**
  - New knowledge-graph nodes appear in the graph files.
  - Two concurrent merges lose nothing.
  - At most 2 workers run at once.

## Phase 3: practice (about 2–3 days)

- **Pieces:** practice agent; the `problem`/`conclude` gates; the model solution revealed verbatim; retesting missed problems.

## Phase 4: unique-to-herdr learning (to be scoped)

- Lessons grounded in a neighbouring agent's session or diff.
- Practice problems drawn from the current repository.
- Short practice sessions while agents are busy.
- Optional: a headless driver front end, if S2/S4 show the pane can't keep answers hidden.

## Open decisions

- Graph location: separate (default) or shared with the LearnHarness Electron app.
- Whether the tutor may read the graph during teaching. LearnHarness's teacher can't; only its prober and practice agents can.
- Worker model choice.
- Whether demos and visuals are in scope.
