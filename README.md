


 # ClusterWorldAI — Official Documentation 🚀

 Welcome to the ClusterWorldAI documentation. This guide explains how to run, observe, and develop S.E.T.H — an autonomous agent living and learning inside a confined virtual environment called `Seth_world`.

 The system follows a core loop: Observe → Think → Decide → Act → Learn, and uses an LLM backend (e.g., Ollama) for internal thought generation and decision-making.

 This document includes a Quick Start, platform notes (Windows/Linux), important file locations, troubleshooting tips, and recommended next steps.

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
 pip install -r Models/S.E.T.H/configuration/requirements.txt
 ```

 3) (Optional) Pull an Ollama model (example: `qwen2.5:7b`)

 ```bash
 ollama pull qwen2.5:7b
 ollama list
 ```

 4) Run a single sandbox cycle (test)

 ```bash
 cd Models/S.E.T.H
 python core/autonomous_loop.py --mode sandbox --interval 1 --cycles 1
 ```

 5) Check logs and outputs

 - Thoughts: Models/S.E.T.H/logs/thoughts.txt
 - Actions: Models/S.E.T.H/logs/actions.log
 - Monitor (God View snapshot): Models/S.E.T.H/data/monitor_state.json

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
 pip install -r Models/S.E.T.H/requirements.txt
 curl http://localhost:11434/version
 cd Models/S.E.T.H
 pytest -q
 python core/autonomous_loop.py --mode sandbox --interval 1 --cycles 1
 ```

 If something fails, collect these details when reporting:

 - OS version (Windows 10/11), Python version, and terminal used (PowerShell / CMD / WSL)
 - Exact command executed and the full error/stack trace
 - Relevant log files: Models/S.E.T.H/logs/daemon.log, Models/S.E.T.H/logs/errors.log, Models/S.E.T.H/logs/thoughts.txt

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

 - Monitor file: Models/S.E.T.H/data/monitor_state.json — snapshot of location, current activity, stats, uptime.
 - Thoughts log: Models/S.E.T.H/logs/thoughts.txt — sequential log of generated internal thoughts.
 - Actions log: Models/S.E.T.H/logs/actions.log — history of executed actions and commands.
 - Daemon/system logs: Models/S.E.T.H/logs/daemon.log — cycle traces, errors, and system events.

 How to inspect as an operator

 1. Open Models/S.E.T.H/data/monitor_state.json for a quick JSON snapshot.
 2. Tail Models/S.E.T.H/logs/thoughts.txt to read the agent's thought stream.
 3. Inspect Models/S.E.T.H/logs/actions.log for executed actions.

 ---

 ## Key Files & Module References 📚

 - [Main loop](Models/S.E.T.H/core/autonomous_loop.py#L1)
 - [Observer](Models/S.E.T.H/core/observer.py#L1)
 - [Thought generator / Ollama integration](Models/S.E.T.H/core/thought_generator.py#L1)
 - [Goal manager](Models/S.E.T.H/core/goal_manager.py#L1)
 - [Tools & ToolManager](Models/S.E.T.H/core/tools.py#L1)
 - [Sandbox manager](Models/S.E.T.H/core/sandbox.py#L1)
 - [Memory manager (SQLite)](Models/S.E.T.H/core/memory_manager.py#L1)
 - [Log manager](Models/S.E.T.H/core/log_manager.py#L1)

 ---

 ## Known Limitations & Safety ⚠️

 - LLM calls (Ollama) are asynchronous and may fail if the service is unavailable. `ThoughtGenerator` implements fallbacks.
 - Keep `safe_mode` enabled during local development to avoid risky operations.

 ---

 ## Development & Contribution 🎯

 - Tests: see `tests/` for unit and integration tests. Run `pytest` from `Models/S.E.T.H`.
 - Workflow: fork the repo, create a feature branch, add tests, and open a pull request.
