# Workshop Planning Agent — Submission

**Event:** Hacktoberfest Hack Day - NIAT-ADYPU

**Initiative:** NIAT × Hacktoberfest 2026

This file collects the materials required by the event submission rules. Items
marked **[TEAM TO COMPLETE]** must be filled in or confirmed by the team before
the deadline. Do not submit placeholders.

## Event context

Hacktoberfest 2026 at NIAT-ADYPU is an open-source hack day focused on moving
from individual coding to collaborative, real-world software development. This
project was built for that event and is intended to be shared publicly so that
other contributors can inspect, run, and improve it.

## Short submission description

Workshop Planning Agent is a local AI assistant for event organizers. It turns a
natural-language workshop request into an exact budget calculation and a
verified equipment-availability report. A local Llama 3.1 model handled through
Ollama selects the next approved action, while a Python host enforces a strict
two-step workflow, validates every argument, performs deterministic arithmetic,
and blocks unknown, repeated, or malformed tool calls. For the sample two-day,
30-student workshop with a INR 15,000 budget, the deterministic tools compute a
INR 12,000 estimate, INR 3,000 remaining, and missing microphones. The project
runs without cloud API keys and keeps all budget and inventory facts inside
deterministic Python tools.

## Project title and problem statement

**Title:** Workshop Planning Agent

**Problem statement:** Workshop organizers must validate a budget and confirm
equipment availability before committing to an event. General-purpose language
models can hallucinate arithmetic or inventory facts. This project builds a
bounded local AI agent that reasons about the request while Python performs
exact calculations and trusted resource checks.

## Solution overview and key features

The agent combines a local `llama3.1:8b` model with deterministic Python tools:

- `calculate_budget` computes exact INR totals and budget status.
- `check_resources` resolves common aliases and reports unavailable items.

The host exposes only one workflow tool at a time, validates arguments against
the tool signature, and terminates on errors instead of permitting blind
retries. The model receives structured JSON tool results and writes the final
summary.

Key features:

- Local-only inference through Ollama.
- Exact integer arithmetic with boolean rejection.
- Deterministic allowlist and workflow lock.
- Graceful failure when the model server is unavailable.
- Reproducible tool verification without an LLM.

## Source-code repository

**Public repository URL:** **[TEAM TO COMPLETE]**

Suggested repository description: "Bounded local LLM agent for deterministic
workshop budgeting and equipment checks."

## Working prototype / demo

Prerequisites: Python 3.10+ and Ollama with `llama3.1:8b`.

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
ollama pull llama3.1:8b
```

Verify deterministic logic:

```powershell
python demo_tools.py
# All direct tool checks passed successfully.
```

Run the prototype:

```powershell
python app.py
```

For the sample workshop, the deterministic results are:

- Estimated cost: INR 12,000
- Remaining budget: INR 3,000
- Availability: projector, Wi-Fi, whiteboard, and laptops; microphones missing

A verified terminal transcript with the exact tool calls and model response is
available in [DEMO.md](DEMO.md). It documents two consecutive successful runs.

A demo recording or screenshot can be added here if the organizers require
visual evidence:

**Demo link:** **[TEAM TO COMPLETE, if applicable]**

## README and technology stack

Setup instructions, architecture, expected output, and the technology stack are
documented in [README.md](README.md). The stack is Python 3.10+, the Ollama
Python SDK, and `llama3.1:8b` served locally.

## Verification status

- [x] `python demo_tools.py` passes for budget, deficit, alias, duplicate,
  boolean, range, non-string, and empty-input cases.
- [x] Host gates were exercised with a fake Ollama client: ordered happy path,
  incomplete workflow, multiple tool calls, unknown tool, out-of-order tool,
  missing arguments, and boolean rejection.
- [x] Ollama function schemas were generated locally to confirm the exposed
  parameter types and descriptions.
- [x] Full live run completed with Ollama 0.40.11, `llama3.1:8b`, and the final
  summary on 26 October 2026. The correct result was reproduced twice; see
  [DEMO.md](DEMO.md).
- [ ] The team has reviewed the live output, README, and submission files.

## Team and contributions

**Team name:** Solo — Suraj Balasaheb Khanase

**Team size:** 1 participant (solo submission)

**Participant and contribution:**

| Member | Responsibilities / contribution |
| --- | --- |
| Suraj Balasaheb Khanase | Project concept and requirements, environment setup, live-run verification, and submission preparation. The implementation and documentation were produced with disclosed AI assistance. |

## Originality and pre-existing work

- The implementation in this repository was created for this submission.
- The original problem brief and high-level design were provided by the team.
- No pre-existing project source code is included.
- Third-party libraries are declared in [requirements.txt](requirements.txt) and
  retain their original licenses.
- AI assistance must remain disclosed. See the next section.

**[TEAM TO CONFIRM]** that no undisclosed template, repository, or pre-existing
code has been added, and that all external ideas are cited.

## Responsible AI and verification

AI-assisted development was used for the initial implementation, documentation,
and test scaffolding. This must not be represented as unaided work.

Before submission, the team must:

- Read and understand `app.py`, `tools.py`, and `SKILL.md`.
- Run `python demo_tools.py` successfully.
- Exercise the live agent with Ollama and review the final summary.
- Confirm that no secrets, credentials, personal data, or confidential workshop
  records are committed.
- Confirm that the model is run locally and that no unexpected external egress
  has been introduced.
- Recheck any AI-generated claims in the README and submission against the code.

## Open-source and licensing

**Public repository:** **[TEAM TO COMPLETE]**

**License:** MIT License. Copyright (c) 2026 Suraj Balasaheb Khanase. See
[LICENSE](LICENSE).

The license is a permissive open-source license that allows reuse,
modification, and redistribution with attribution. The participant reviewed
this choice for the solo submission.

Open-source readiness:

- [ ] Repository is public and accessible without credentials.
- [x] A `LICENSE` file is present and matches this document.
- [x] Setup and test instructions work from a clean environment.
- [x] Contribution and conduct expectations are documented in
  [CONTRIBUTING.md](CONTRIBUTING.md) and [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md).
- [x] Third-party dependency licenses remain intact.
- [x] No secrets, personal data, or local configuration are committed.

The project uses open-source technologies including Python, Ollama, Pydantic,
HTTPX, and their transitive dependencies. Those components retain their
original licenses and copyright notices.

## Security and privacy

- Tool access is restricted to the `TOOLS` allowlist.
- Arguments must be a dictionary of strings and must match the Python signature.
- The tools reject booleans, non-integers, out-of-range values, and empty input.
- Tool errors stop the run and are not sent back for automatic retries.
- The model loop is capped at five turns.
- The application calls Ollama at `localhost`; it does not use cloud inference.
- The repository contains no API keys or personal data.

**[TEAM TO CONFIRM]** that the demo and submission do not expose student names,
contact details, attendance records, or other personal information.

## Eligibility, registration, and conduct

These are team/organizer actions, not repository features:

- [ ] All members are eligible students under the organizer's rules.
- [ ] The official registration is complete and information is accurate.
- [ ] The team complies with the announced team-size limit.
- [ ] The team has read the event Code of Conduct.
- [ ] The submission is made before the announced deadline.
- [ ] The team will follow judging and organizer instructions.
- [ ] All third-party intellectual property and licenses are respected.
- [ ] Team decisions and submitted work remain the team's own.

## Final submission checklist

- [ ] Public repository URL added above and in the submission form.
- [x] Team name and member contribution completed for the solo submission.
- [x] MIT license reviewed and `LICENSE` file added.
- [ ] Demo video/screenshot added if required.
- [ ] `python demo_tools.py` passes.
- [x] Live Ollama demo completed and recorded in `DEMO.md`.
- [ ] Team has reviewed the live output and final submission.
- [ ] AI and pre-existing-work disclosures reviewed for accuracy.
- [ ] No secrets, personal data, or confidential records committed.
- [ ] Eligibility and registration confirmed.
- [ ] Event-specific judging and submission instructions reviewed.
- [ ] Submission completed before the deadline.
