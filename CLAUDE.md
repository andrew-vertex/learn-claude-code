# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a 0-to-1 teaching repository on **harness engineering** — building the operational environment (tools, knowledge, memory, permissions) around an AI agent model. It teaches how Claude Code works from the inside out through 20 progressive Python lessons.

The core insight: **Agency comes from the model, not from code orchestration.** The harness gives the model hands, eyes, and a workspace. Each lesson adds one harness mechanism on top of the same fundamental agent loop — the loop itself never changes.

```
Agent = Model (LLM) + Harness (Tools + Knowledge + Observation + Action + Permissions)
```

## Build & Run Commands

### Python Lessons (Current Track: s01–s20)

```sh
pip install -r requirements.txt        # anthropic>=0.25.0, python-dotenv, pyyaml
cp .env.example .env                    # configure ANTHROPIC_API_KEY and MODEL_ID

# Run individual chapters
python s01_agent_loop/code.py           # Start here — one loop + bash
python s08_context_compact/code.py      # Context compaction (complex)
python s20_comprehensive/code.py        # Endpoint: all mechanisms in one loop

# Run legacy track (12-lesson version in agents/)
python agents/s01_agent_loop.py
python agents/s_full.py                 # Full legacy agent
```

### Tests

```sh
python -m pytest tests/ -v              # Smoke tests: compile-check legacy agent scripts
```

Tests live in `tests/` and use pytest. They are minimal smoke/compile checks — each lesson's `code.py` is its own self-contained demo, not a library with unit tests.

### Web Platform

```sh
cd web && npm install && npm run dev    # http://localhost:3000 — renders the legacy docs/ track
```

## Architecture: Two Tracks

**Current canonical track — root-level `s01/` through `s20/`:**
Each folder is a self-contained chapter:
```
s08_context_compact/
  README.md          # Chinese source (complete narrative with inline code)
  README.en.md       # English translation
  README.ja.md       # Japanese translation
  code.py            # Standalone runnable implementation
  images/            # SVG diagrams
```

**Legacy transition track — `agents/` and `docs/`:**
The older 12-lesson version. `agents/` holds runnable Python files (`s01_agent_loop.py` through `s12_worktree_task_isolation.py` plus `s_full.py`). `docs/` holds the markdown for each chapter in `zh/`, `en/`, `ja/`. The `web/` app currently renders the legacy `docs/` track.

Do not mix chapter numbers across tracks — the legacy and current chapter numbers differ (see mapping in README).

## The Agent Loop Pattern

Every lesson layers on the same invariant loop — this is the fundamental pattern the whole repo teaches:

```python
def agent_loop(messages):
    while True:
        response = client.messages.create(
            model=MODEL, system=SYSTEM, messages=messages, tools=TOOLS
        )
        messages.append({"role": "assistant", "content": response.content})
        if response.stop_reason != "tool_use":
            return                          # Model stopped — done
        results = []
        for block in response.content:
            if block.type == "tool_use":
                output = TOOL_HANDLERS[block.name](**block.input)
                results.append({"type": "tool_result", "tool_use_id": block.id, "content": output})
        messages.append({"role": "user", "content": results})  # Loop back
```

## Lesson Progression

| # | Chapter | Mechanism |
|---|---------|-----------|
| s01 | Agent Loop | `while stop_reason == "tool_use"` — the core pattern |
| s02 | Tool Use | Dispatch map, `TOOL_HANDLERS` dict, concurrent tool calls |
| s03 | Permission | `PermissionRule`, approval pipeline, dangerous-command blocking |
| s04 | Hooks | `PreToolUse` / `PostToolUse` extension points around the loop |
| s05 | TodoWrite | Plan-then-execute, `TodoItem` structure |
| s06 | Subagent | Fresh `messages[]` per subagent, context isolation |
| s07 | Skill Loading | `SkillManifest`, on-demand system-prompt injection |
| s08 | Context Compact | snipCompact, microCompact, toolResultBudget, autoCompact |
| s09 | Memory | Selection → extraction → consolidation pipeline |
| s10 | System Prompt | Runtime assembly via section concatenation |
| s11 | Error Recovery | Token escalation, fallback model, retry strategies |
| s12 | Task System | `TaskRecord` with `blockedBy`, file-backed persistence |
| s13 | Background Tasks | Threaded execution, notification injection on completion |
| s14 | Cron Scheduler | Durable scheduled triggers, session-scoped |
| s15 | Agent Teams | `MessageBus` + per-agent inboxes + async mailboxes |
| s16 | Team Protocols | Request-reply format, shutdown handshake, plan approval |
| s17 | Autonomous Agents | Idle cycle, auto-claim from task board, self-organization |
| s18 | Worktree Isolation | `WorktreeRecord`, task-directory binding, parallel safety |
| s19 | MCP Plugin | Multi-transport, channel routing, external tool pool assembly |
| s20 | Comprehensive | All mechanisms combined into one complete harness |

## Coding Conventions

- **Standalone scripts**: Every `code.py` is self-contained and directly runnable — no shared library, no imports between chapters. This is intentional — each lesson teaches one mechanism in isolation.
- **Dependencies**: `anthropic` SDK, `python-dotenv`, `pyyaml`. The `.env` file supports multiple Anthropic-compatible providers (MiniMax, GLM, Kimi, DeepSeek) via `ANTHROPIC_BASE_URL`.
- **Python style**: Type hints used, `from __future__ import annotations`, `dataclass` for data containers.
- **Legacy vs current**: Changes to mechanism implementations should target the root-level `s*/code.py` files, not `agents/`. The `agents/` directory is frozen legacy.
