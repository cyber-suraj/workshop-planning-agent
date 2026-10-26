# Workshop Planning Agent

**Hacktoberfest Hack Day - NIAT-ADYPU · NIAT × Hacktoberfest 2026**

**Author:** Suraj Balasaheb Khanase

A bounded, local AI agent for workshop budgeting and equipment checks. The
language model only chooses actions; Python controls tool availability,
validates arguments, performs exact arithmetic, and owns all inventory facts.

## Problem

Workshop organizers need to answer two high-stakes questions before committing
to an event:

1. Will the planned attendance and daily costs fit the available budget?
2. Are all required equipment categories actually available?

General-purpose chatbots can produce plausible but incorrect arithmetic or
resource claims. This project separates language understanding from exact
calculations and trusted inventory lookups so the final answer is grounded in
deterministic tool results.

## Solution

The user describes the workshop in natural language. A local
`llama3.1:8b` model, served by Ollama, selects the next approved tool. The
Python host executes that tool inside strict security gates and returns the
JSON result to the model for a final, grounded summary.

The demonstrated scenario is a two-day workshop for 30 students at INR 200 per
person per day, with a budget of INR 15,000. The agent verifies projector,
Wi-Fi, whiteboard, laptops, and microphone availability.

## Key features

- Exact INR budgeting with over-budget detection.
- Inventory checks with aliases such as `beamer`, `WIFI`, and `notebooks`.
- Deterministic facts; the model never performs the arithmetic or invents stock.
- Strict allowlist, workflow-order lock, signature validation, and safe
  exception handling.
- Fail-safe termination on malformed or repeated tool calls.
- Fully local model execution at `http://localhost:11434`; no cloud API keys.

## Architecture

```text
User prompt
    |
    v
System prompt + SKILL.md + next allowed tool
    |
    v
Ollama (llama3.1:8b)
    |
    +-- tool request --> Gate 1: allowlist + workflow order
                         |
                         +--> Gate 2: argument dict + signature validation
                              |
                              +--> Gate 3: guarded execution in tools.py
                                   |
                                   +--> JSON result appended as role: tool
    |
    +-- no tool request --> final answer
```

The workflow is locked to `calculate_budget` followed by `check_resources`.
Execution is capped at five model turns. Requesting multiple tools, an unknown
tool, a repeated/out-of-order tool, or retrying after an error stops the run.

## Tech stack

| Component | Technology |
| --- | --- |
| Language model | `llama3.1:8b` via Ollama |
| Local LLM client | `ollama` Python SDK 0.6.3 |
| Host application | Python 3.10+ |
| Deterministic tools | Python standard library |
| Interface | Command-line runner in `app.py` |
| Verification | `demo_tools.py` with Python assertions |

## Files

| File | Purpose |
| --- | --- |
| `app.py` | Host agent, tool gates, Ollama loop, and final dispatch |
| `tools.py` | Integer-guarded budget arithmetic and inventory lookup |
| `SKILL.md` | Behavioral contract loaded into the system prompt |
| `demo_tools.py` | Deterministic tool verification, independent of the LLM |
| `DEMO.md` | Verified live-run transcript and final model summary |
| `requirements.txt` | Pinned Python dependency |
| `SUBMISSION.md` | Submission details and compliance checklist |
| `LICENSE` | MIT License |
| `CONTRIBUTING.md` | Setup, contribution workflow, and AI disclosure rules |
| `CODE_OF_CONDUCT.md` | Project-level collaboration and reporting expectations |
| `.gitignore` | Local environments, caches, and secrets |

## Setup

Requires Python 3.10+ and a local
[Ollama](https://ollama.com/download) installation.

### Windows PowerShell

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
ollama pull llama3.1:8b
```

If `ollama` is not recognised in an already-open terminal, restart the terminal
or use `%LOCALAPPDATA%\Programs\Ollama\ollama.exe` directly. On this machine the
Ollama 0.40.11 service is installed and `llama3.1:8b` is already pulled.

### Linux / macOS

```bash
python3 -m venv venv
source venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
ollama pull llama3.1:8b
```

Keep Ollama running at `http://localhost:11434` (the default).

## Demo

Verify deterministic tools first:

```powershell
python demo_tools.py
```

Expected output:

```text
All direct tool checks passed successfully.
```

Run the agent:

```powershell
python app.py
```

Expected result for the sample request:

- Estimated cost: INR 12,000
- Budget: INR 15,000
- Remaining budget: INR 3,000 (within budget)
- Available: projector, Wi-Fi, whiteboard, laptops
- Missing: microphones

`microphones` are intentionally absent from the local inventory, so the agent
must report them as missing rather than invent availability.

### Live verification

Verified end to end on 26 October 2026 with Ollama 0.40.11, Python 3.14.7, and
`llama3.1:8b`:

- Both tool calls executed in the required order.
- The model's final summary used the deterministic results and reported
  microphones as missing.
- The run was repeated twice with the same results.
- Full transcript: [DEMO.md](DEMO.md).

## Security and responsible AI

- The `TOOLS` dictionary is the only allowlist; there is no dynamic dispatch or
  `eval()`.
- Only the next workflow tool is exposed to the model.
- `inspect.signature(...).bind()` rejects missing, extra, or malformed arguments.
- `tools.py` rejects booleans and non-integer numeric inputs even though `bool`
  is a subclass of `int`.
- Tool exceptions become structured error payloads and terminate the run.
- `MAX_STEPS = 5` bounds model and tool execution.
- The LLM runs locally and has no access to cloud services, credentials, or
  arbitrary functions.
- The agent logs tool names and arguments for traceability. Do not submit
  student PII or confidential data as test input.

The model can still phrase a summary incorrectly; the host ensures that all
budget and inventory facts come from the deterministic tools. An operator should
review the final response and rerun `demo_tools.py` when changing tool logic.

## Originality and attribution

This repository was created for the event submission. The initial
implementation and documentation were produced with AI-assisted development
using OpenCode, then verified through the deterministic test suite, mocked
host-gate checks, and two consecutive live Ollama runs. The team must review the
code, understand the implementation, and keep this disclosure accurate before
submission.

No pre-existing project source code is included. Any libraries used retain their
original licenses. See [SUBMISSION.md](SUBMISSION.md) for the submission
checklist, team contribution template, and license placeholder.

## Open-source focus

The project is built on open-source technologies and is released under the
[MIT License](LICENSE). Contributions are welcome under the guidelines in
[CONTRIBUTING.md](CONTRIBUTING.md), and all participation is covered by
[CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md).

## License

Copyright (c) 2026 Suraj Balasaheb Khanase. Distributed under the
[MIT License](LICENSE).

## Event compliance

This project was built for **Hacktoberfest Hack Day - NIAT-ADYPU**, part of
**NIAT × Hacktoberfest 2026**. The event rules require eligibility,
registration, a public repository, original-work disclosure, responsible AI use,
and a completed submission before the organizer deadline. The project-level
checklist is in [SUBMISSION.md](SUBMISSION.md); registration, team-size limits,
judging, and deadlines remain the team's responsibility.
