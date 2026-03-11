# ClusterWorldAI — Official Documentation 🚀

## Vision

**S.E.T.H** is an autonomous agent research platform — a self-contained AI that lives, learns, and acts within a virtual world. The goal is to explore how agents can develop persistent memory, pursue self-generated goals, and exhibit emergent behaviors in a safe, observable sandbox. Think of it as a digital petri dish for studying autonomous intelligence.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                         SETH_WORLD                               │
│                    (Virtual Environment)                         │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐            │
│  │  House  │  │Apartment│  │ Village │  │  City   │            │
│  └─────────┘  └─────────┘  └─────────┘  └─────────┘            │
└─────────────────────────────────────────────────────────────────┘
                              ▲
                              │ Observe
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                        S.E.T.H AGENT                             │
│                                                                  │
│   ┌──────────┐    ┌──────────┐    ┌──────────┐                 │
│   │ Observer │───▶│  Thought │───▶│   Goal   │                 │
│   │          │    │Generator │    │ Manager  │                 │
│   └──────────┘    └──────────┘    └──────────┘                 │
│        │               │               │                        │
│        │          ┌────┴────┐          │                        │
│        │          │   LLM   │          │                        │
│        │          │(Ollama) │          │                        │
│        │          └─────────┘          │                        │
│        │                               │                        │
│        ▼                               ▼                        │
│   ┌──────────┐                  ┌──────────┐                   │
│   │  Memory  │◀─────────────────│  Action  │                   │
│   │ (SQLite) │                  │ Executor │                   │
│   └──────────┘                  └──────────┘                   │
│                                        │                        │
└────────────────────────────────────────┼────────────────────────┘
                                         │ Act
                                         ▼
                              ┌──────────────────┐
                              │    God View      │
                              │  (Operator UI)   │
                              │ • monitor_state  │
                              │ • thoughts.txt   │
                              │ • actions.log    │
                              └──────────────────┘

            ┌─────────────────────────────────────┐
            │         CORE LOOP                    │
            │                                     │
            │  Observe → Think → Decide → Act     │
            │     ↑                    │          │
            │     └──── Learn ◀───────┘          │
            └─────────────────────────────────────┘
```

---

## World Types

S.E.T.H operates within different environment scales. Each world type defines the scope and complexity of the agent's surroundings:

| World | Description | Complexity | Use Case |
|-------|-------------|------------|----------|
| **house** | Single building, limited rooms | Low | Testing, debugging, simple behaviors |
| **apartment** | Multi-room dwelling with neighbors | Low-Medium | Social interaction experiments |
| **village** | Multiple buildings, NPCs, paths | Medium | Navigation, community simulation |
| **city** | Large scale, many locations, complex systems | High | Full autonomous agent testing |

**Choose based on your goals:**
- New to the project? Start with `house` — fewer variables, easier to observe
- Testing social behaviors? Use `apartment` or `village`
- Stress-testing full autonomy? Run `city` mode

---

## Goal System

S.E.T.H uses a hierarchical goal system that drives autonomous behavior:

### Goal Types
- **Survival Goals** — Basic needs (hunger, rest, safety)
- **Exploration Goals** — Discover new locations, items, knowledge
- **Social Goals** — Interact with NPCs, build relationships
- **Self-Generated Goals** — Emergent objectives from reflection

### How Goals Work
```
1. GoalManager initializes with base survival goals
2. Observer detects environmental changes
3. ThoughtGenerator reflects on current state
4. Goals are prioritized based on urgency and opportunity
5. Action selected to progress toward top goal
6. Result evaluated → goal updated or completed
```

### Goal Priority Factors
- **Urgency** — How critical is this need? (hunger → high urgency)
- **Opportunity** — Is now a good time to act on this?
- **Curiosity** — Self-generated exploration drives learning

---

## Quick Start (Linux / Windows) ⚡️

Prerequisites: Python 3.8+ (3.11 / 3.13 recommended). Install `ollama` if you plan to run local LLM models.

1) Create and activate a virtual environment

PowerShell (Windows):

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

Linux / macOS:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

2) Install dependencies

```bash
pip install -r Models/ClusterWorldAI/configuration/requirements.txt
```

3) (Optional) Pull an Ollama model (example: `qwen2.5:7b`)

```bash
ollama pull qwen2.5:7b
ollama list
```

4) Run a single sandbox cycle (test)

```bash
cd Models/ClusterWorldAI
python core/autonomous_loop.py --mode sandbox --interval 1 --cycles 1
```

5) Check logs and outputs

- Thoughts: Models/ClusterWorldAI/logs/thoughts.txt
- Actions: Models/ClusterWorldAI/logs/actions.log
- Monitor (God View snapshot): Models/ClusterWorldAI/data/monitor_state.json

---

## Architecture & Key Files 🗂️

- `core/autonomous_loop.py` — Main orchestration loop
- `core/thought_generator.py` — LLM integration and thought generation
- `core/observer.py` — Environment observer
- `core/goal_manager.py` — Goal management
- `core/sandbox.py` — Seth_world confinement and validation
- `core/memory_manager.py` — Persistent memory (SQLite)
- `core/log_manager.py` — Centralized logging

---

## Modes of Operation 🧭

The project is primarily designed to run in `sandbox` mode (recommended). Other development modes exist but sandbox is the safest default for autonomous runs.

---

## Platform Compatibility (Windows & Linux) 🖥️

- Tested on **Linux** and **Windows**. Use Python 3.8+ (3.11/3.13 recommended).

Windows Notes & Caveats

- Always create and activate a virtual environment before installing dependencies.
- Ensure external services (e.g. Ollama) are reachable (default: http://localhost:11434).
- Shell scripts (`.sh`) and `systemd` units do not apply on Windows — use PowerShell, WSL, or Git Bash.
- Path separators and permissions can differ on Windows; the `SandboxManager` may validate paths — prefer forward slashes or normalized paths where needed.
- Subprocess and shell calls may behave differently on Windows; avoid enabling untested features.

Quick Windows validation checklist

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r Models/ClusterWorldAI/requirements.txt
curl http://localhost:11434/version
cd Models/ClusterWorldAI
pytest -q
python core/autonomous_loop.py --mode sandbox --interval 1 --cycles 1
```

If something fails, collect these details when reporting:

- OS version (Windows 10/11), Python version, and terminal used (PowerShell / CMD / WSL)
- Exact command executed and the full error/stack trace
- Relevant log files: Models/ClusterWorldAI/logs/daemon.log, Models/ClusterWorldAI/logs/errors.log, Models/ClusterWorldAI/logs/thoughts.txt

---

## Secrets & Tokens 🔐

- Example token file: Models/GTA-Agent/Discord/data/token.json.example
- For local runs, copy that example to Models/GTA-Agent/Discord/data/token.json and insert your token, or export `DISCORD_TOKEN` to your environment.
- NEVER commit real tokens. `.gitignore` excludes `token.json` and `tokens.json`.

---

## Quick Usage Examples ✨

Run the autonomous loop (recommended: sandbox):

```bash
python3 core/autonomous_loop.py --mode sandbox --interval 10 --cycles 3
```

Useful options

- `--mode`: `sandbox` (recommended)
- `--cycles`: number of iterations to run
- `--interval`: seconds between cycles
- `--world`: `house|apartment|village|city` (select Seth_world type)

Debug example: run one quick cycle and exit

```bash
python3 core/autonomous_loop.py --mode sandbox --interval 1
```

---

## Observability — "God View" 👁️

The "God View" is the operator's perspective for monitoring the agent. Primary entry points:

- Monitor file: Models/ClusterWorldAI/data/monitor_state.json — snapshot of location, current activity, stats, uptime.
- Thoughts log: Models/ClusterWorldAI/logs/thoughts.txt — sequential log of generated internal thoughts.
- Actions log: Models/ClusterWorldAI/logs/actions.log — history of executed actions and commands.
- Daemon/system logs: Models/ClusterWorldAI/logs/daemon.log — cycle traces, errors, and system events.

How to inspect as an operator

1. Open Models/ClusterWorldAI/data/monitor_state.json for a quick JSON snapshot.
2. Tail Models/ClusterWorldAI/logs/thoughts.txt to read the agent's thought stream.
3. Inspect Models/ClusterWorldAI/logs/actions.log for executed actions.

---

## Key Files & Module References 📚

- [Main loop](Models/ClusterWorldAI/core/autonomous_loop.py#L1)
- [Observer](Models/ClusterWorldAI/core/observer.py#L1)
- [Thought generator / Ollama integration](Models/ClusterWorldAI/core/thought_generator.py#L1)
- [Goal manager](Models/ClusterWorldAI/core/goal_manager.py#L1)
- [Tools & ToolManager](Models/ClusterWorldAI/core/tools.py#L1)
- [Sandbox manager](Models/ClusterWorldAI/core/sandbox.py#L1)
- [Memory manager (SQLite)](Models/ClusterWorldAI/core/memory_manager.py#L1)
- [Log manager](Models/ClusterWorldAI/core/log_manager.py#L1)

---

## Known Limitations & Safety ⚠️

- LLM calls (Ollama) are asynchronous and may fail if the service is unavailable. `ThoughtGenerator` implements fallbacks.
- Keep `safe_mode` enabled during local development to avoid risky operations.

---

## Development & Contribution 🎯

- Tests: see `tests/` for unit and integration tests. Run `pytest` from `Models/S.E.T.H`.
- Workflow: fork the repo, create a feature branch, add tests, and open a pull request.