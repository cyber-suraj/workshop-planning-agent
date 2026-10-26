# Live Demo Transcript

Verified on **26 October 2026** as part of the Hacktoberfest Hack Day -
NIAT-ADYPU submission.

## Environment

- Windows 11
- Python 3.14.7
- Ollama 0.40.11
- Model: `llama3.1:8b` (4.9 GB)
- Ollama endpoint: `http://localhost:11434`

## Command

```powershell
.\venv\Scripts\python.exe app.py
```

## Tool trace

```text
INFO Step 1 REQUEST calculate_budget {'daily_cost_per_person': 200, 'days': 2, 'participants': 30, 'budget': 15000}
INFO Step 1 RESULT calculate_budget {'currency': 'INR', 'total_estimated_cost': 12000, 'budget': 15000, 'remaining_budget': 3000, 'within_budget': True}
INFO Step 2 REQUEST check_resources {'required_items': 'projector,Wi-Fi,whiteboard,laptops,microphones'}
INFO Step 2 RESULT check_resources {'available': ['projector', 'wi-fi', 'whiteboard', 'laptops'], 'missing': ['microphones']}
```

The two tool calls happened in the required order. The host exposed each tool
only for its allowed turn. The model did not perform the arithmetic or invent
inventory.

## Model-generated final summary

> The total estimated cost for the two-day workshop is 12000 INR, which is
> within the budget of 15000 INR. The remaining budget is 3000 INR.
>
> The required resources are projector, Wi-Fi, whiteboard, and laptops, which
> are all available in the local inventory. However, microphones are missing
> from the inventory.

## Reproducibility

The run was repeated twice. Both runs produced the same tool order, budget
figures, availability list, and missing-item result. The model's wording was
consistent across the two runs. The deterministic tool output can be checked
without Ollama by running:

```powershell
.\venv\Scripts\python.exe demo_tools.py
```
