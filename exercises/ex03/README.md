# Exercise 3 — Implementation & Code Review

The coding agent is now implementing the solution from the specification. This takes **10–20 minutes**. Work through the steps below while it runs.

> **💡 Tip**
> If the coding agent pauses and waits for input, type `continue`.

> **Note — IBD variance is normal**
> The generated code, file names, function names, and implementation choices will differ between participants — even with the same intent prompt. Some generations put risk scoring in `app/risk_scorer.py`, others inline in `agent.py`, others in a runtime skill. All of these are valid. The hunts below are designed to work regardless of which pattern your generation chose.

---

## Hunts in this exercise

> **💡 How to find the code**
> Your agent folder name will vary (e.g. `supply-disruption-agent`, `supplier-halt-risk-agent`) — it matches `metadata.name` in `assets/<your-agent>/asset.yaml`. All the files below are inside `assets/<your-agent>/app/`.
> Use **Cmd+P** (Mac) / **Ctrl+P** (Windows) in VS Code to open any file by name instantly — no folder navigation needed. Type `agent.py`, `mcp_tools.py`, or `conftest.py` and press Enter.

| Hunt | What you find |
|------|---------------|
| [Hunt 1](#hunt-1----appagentpy) | Model config & milestone logging in `agent.py` |
| [Hunt 2](#hunt-2----risk-scoring-wherever-your-generation-put-it) | Risk scoring logic and HIGH/MEDIUM/LOW thresholds |
| [Hunt 3](#hunt-3----output-labeling) | `[Confirmed Fact]` / `[Assumption]` / `[Recommendation]` labels |
| [Hunt 4](#hunt-4----mcp-tool-indirection-appmcp_toolspy) | MCP tool indirection in `mcp_tools.py` |
| [Hunt 5](#hunt-5----tests) | Test structure and offline mocking |

---

## Step 3.1 — Code Hunt

Work through the hunts below as files appear in the Explorer panel.

> **Note — Compare with your neighbour**
> Your agent name, MCP server names, and threshold values may differ depending on how you answered the clarifying questions in Exercise 1. That is expected — every item below exists in every valid generation.

### Hunt 1 — `app/agent.py`

Find the `get_model_name()` function — decorated with `@agent_model`:

```python
@agent_model(key="config.model", label="LLM Model")
def get_model_name() -> str:
    return "sap/anthropic--claude-4.5-sonnet"   # primary model
```

- [ ] Found it. What model is set as primary in your file?

> **Note**
> The `@agent_model` decorator exposes this setting in the Joule Studio Design View, so the model can be changed without touching the surrounding code. The same pattern applies to temperature and other config values via `@agent_config`.

Find the OTel milestone logging. Look for `logger.info` calls that contain milestone markers like `M1.achieved` or `M5.achieved` — these track whether key pipeline steps succeeded or were skipped:

```python
logger.info("[M1.achieved]: MCP tools loaded successfully — proceeding with agent")
...
logger.info("[M5.achieved]: Risk assessment report delivered to requestor")
```

- [ ] Found milestone logging. How many milestones are instrumented?
- [ ] The span wraps the `execute()` method, not `stream()` — why does that matter for tracing parallel tool calls?

### Hunt 2 — Risk scoring (wherever your generation put it)

IBD can implement risk scoring in different ways — a Python file (`app/tools/scoring_engine.py`), a runtime skill (`app/skills/risk-scoring/SKILL.md`), or inline in `agent.py`. Find where yours ended up.

- [ ] Located the risk scoring logic — is it a Python file, a skill file, or inside `agent.py`?
- [ ] Found the HIGH / MEDIUM / LOW threshold values — what are they in your generation?

The thresholds will look something like this if implemented as a Python tool:

```python
DELIVERY_HIGH_DAYS = int(os.getenv("DELIVERY_HIGH_DAYS", "14"))
VALUE_HIGH_EUR     = float(os.getenv("VALUE_HIGH_EUR", "50000"))
```

Or like this if implemented as a runtime skill:

```markdown
## Scoring Thresholds [Assumption]
- HIGH: delivery within 14 days AND open value ≥ €50k
- MEDIUM: delivery within 30 days OR open value ≥ €10k
- LOW: all other lines
```

- [ ] The thresholds are labeled `[Assumption]` — why does that distinction matter for the procurement manager reading the output?

### Hunt 3 — Output labeling

Find where `[Confirmed Fact]`, `[Assumption]`, and `[Recommendation]` labels are applied. This may be in `app/tools/labeling.py`, inline in `agent.py`, or in a runtime skill.

```python
def label_confirmed_fact(value, field_name=""):
    return {"label": "[Confirmed Fact]", "field": field_name, "value": value}
```

- [ ] Found the labeling logic
- [ ] Confirmed that API-retrieved values (PO number, supplier, delivery date) are labeled `[Confirmed Fact]`
- [ ] Confirmed that scoring thresholds and geographic assumptions are labeled `[Assumption]`
- [ ] Confirmed that suggested re-routing actions are labeled `[Recommendation]`

### Hunt 4 — MCP tool indirection (`app/mcp_tools.py`)

Find the file `app/mcp_tools.py`. It should be a thin wrapper — not the actual MCP connector:

```python
from sap_cloud_sdk.agentgateway import get_mcp_tools
__all__ = ["get_mcp_tools"]
```

- [ ] Found it. Why does `agent_executor.py` import from `mcp_tools` instead of directly from `sap_cloud_sdk`?

> **Note**
> `mcp_tools.py` is the patch target in tests — `conftest.py` mocks `get_mcp_tools` here so tests never need a live Agent Gateway connection. Importing directly from the SDK would break the mock.

### Hunt 5 — Tests

Open the `tests/` folder inside your agent asset directory.

- [ ] At least one test file per tool (`test_scoring_engine.py`, `test_labeling.py`, etc.)
- [ ] An integration test (`test_integration.py`) that exercises the full pipeline without network calls
- [ ] A `conftest.py` that patches `get_mcp_tools` so tests run offline
- [ ] All tests pass

> **Test files missing or empty?** If you used the fast-track setup, test generation may have been skipped. See [TS-02 — Testing Overview shows no test reports](../../troubleshooting/README.md#ts-02--testing-overview-shows-no-test-reports)

> **💡 Finished early?**
> Start the bonus while you wait: [Exercise 5 — Bonus Exercises](../ex05/README.md)

---

## Step 3.3 — Verify the Generated File Structure

When the coding agent finishes, verify your project has this shape. The exact folder and file names will match your agent — they will differ from the example below.

```
<your-project-folder>/
├── solution.yaml                    ← solution manifest listing all assets
├── assets/
│   ├── <your-agent-name>/           ← one folder per agent
│   │   ├── asset.yaml
│   │   ├── mcp-mock.json            ← mock data for tests
│   │   ├── tests/                   ← test files live here, inside the agent folder
│   │   │   └── test_*.py
│   │   └── app/
│   │       ├── agent.py             ← main agent logic
│   │       ├── agent_executor.py
│   │       ├── main.py
│   │       ├── util.py
│   │       ├── skills/              ← runtime skill files (.md)
│   │       │   └── <skill-name>/
│   │       │       └── SKILL.md
│   │       ├── tools/               ← business tool wrappers (may be present)
│   │       │   └── *.py
│   │       └── mcp_providers/
│   │           └── agw.py           ← Agent Gateway connector
│   ├── <s4-purchase-order-mcp-server>/
│   │   └── mcp-translation/
│   │       ├── translation.json     ← tool definitions
│   │       └── api-spec.edmx        ← OData spec
```

**Check the following:**

- [ ] `solution.yaml` exists at the project root and lists all asset folders
- [ ] MCP server asset folder exists only for `CE_PURCHASEORDER_0001`
- [ ] Each MCP server has a `mcp-translation/` folder with `translation.json` and an API spec file
- [ ] At least one skill file exists under `app/skills/`
- [ ] A `tests/` folder exists inside `assets/<your-agent-name>/` with test files

> **⚠️ Unexpected MCP server folders?**
> If you see MCP server folders for APIs other than `CE_PURCHASEORDER_0001`, the spec contained an unregistered API. Tell the coding agent: `"Remove the [name] MCP server asset — only CE_PURCHASEORDER_0001 is registered on this tenant."` Then ask it to update `solution.yaml` and `asset.yaml` to remove the reference before deploying.

---

## Step 3.2 — Pre-deploy: validate retrieval performance

Before deploying, ask the coding agent to check one common performance issue in generated agents: fetching PO items one purchase order at a time instead of in a single batched call.

A supplier like `USSU_V8003` has 3 open POs — manageable either way. But on a supplier with dozens of open POs, a per-PO loop makes one API call per order and runs significantly slower. The fix is a single item-level call filtered on all PO numbers at once.

Send this to the coding agent:

```
Before I deploy, check my agent's purchase-order retrieval logic.
Does it fetch all PO items in a single batched call — filtering on
all PO numbers at once — or does it make one separate call per
purchase order? If it's looping one call per PO, refactor it to
batch them into a single request.
```

The coding agent will read your `agent.py` (or wherever the retrieval logic lives) and either confirm it is already batched, or refactor it to use a single call.

> **Why this matters**
> The risk-scoring logic lives at the item level — material, open value, planned delivery duration. The agent has to retrieve PO headers first to discover which POs exist, then fetch their items. If it makes a separate item call per header, latency scales linearly with the number of open POs. A single batched call (`PurchaseOrder eq 'PO1' or PurchaseOrder eq 'PO2' or ...`) returns everything in one round trip regardless of how many POs there are.

---

**Next step →** [Exercise 4 — Deploy & Test Your Agent](../ex04/README.md)
