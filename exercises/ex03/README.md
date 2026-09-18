# Exercise 3 — Implementation & Code Review

The coding agent is now implementing the solution from the specification. This takes **20–25 minutes**. Work through the steps below while it runs.

> **💡 Tip**
> If the coding agent pauses and waits for input, type `continue`.

> **Note — IBD variance is normal**
> The generated code, file names, function names, and implementation choices will differ between participants — even with the same intent prompt. Some generations put risk scoring in `app/risk_scorer.py`, others inline in `agent.py`, others in a runtime skill. All of these are valid. The hunts below are designed to work regardless of which pattern your generation chose.

---

## Step 3.1 — Code Hunt

Work through the hunts below as files appear in the Explorer panel — use **Ctrl+P** (Cmd+P on Mac) to open files by name without navigating the tree.

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

Find the OTel milestone logging. IBD instruments milestones inside a helper method (`_run_agent`) — never inside `stream()`. Look for a pattern like:

```python
async def _run_agent(self, ...):
    with tracer.start_as_current_span("..."):
        logger.info("M1.achieved: ...")
        ...

async def stream(self, ...):
    result = await self._run_agent(...)
    yield result
```

- [ ] Found milestone logging. How many milestones are instrumented?
- [ ] Confirmed the OTel span is in `_run_agent`, not inside `stream` — why does that matter?

### Hunt 2 — Risk scoring (wherever your generation put it)

IBD can implement risk scoring in different ways — a Python file (`app/risk_scorer.py`), a runtime skill (`app/skills/risk-scoring/SKILL.md`), or inline in `agent.py`. Find where yours ended up.

- [ ] Located the risk scoring logic — is it a Python file, a skill file, or inside `agent.py`?
- [ ] Found the HIGH / MEDIUM / LOW threshold values — what are they in your generation?
- [ ] The thresholds are labeled `[Assumption]` not `[Confirmed Fact]` — why does that distinction matter?

> **Note — Compare with your neighbour**
> Different answers to the clarifying questions in Exercise 1 can produce different implementation choices here. Both a Python scorer and a skill-based scorer are valid — the labeling discipline is what matters.


> **💡 Finished early?**
> Start the bonus while you wait: [Exercise 5 — Bonus Exercises](../ex05/README.md)

---

## Step 3.2 — Verify the Generated File Structure

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
│   └── <s4-scheduling-agreements-mcp-server>/
│       └── mcp-translation/
│           ├── translation.json
│           └── api-spec.edmx
```

**Check the following:**

- [ ] `solution.yaml` exists at the project root and lists all asset folders
- [ ] MCP server asset folders exist only for registered APIs (`CE_PURCHASEORDER_0001`, `API_SCHED_AGRMT_PROCESS_SRV`, `CE_SUPPLIERLIST_0001`)
- [ ] Each MCP server has a `mcp-translation/` folder with `translation.json` and an API spec file
- [ ] At least one skill file exists under `app/skills/`
- [ ] A `tests/` folder exists inside `assets/<your-agent-name>/` with test files

> **⚠️ Unexpected MCP server folders?**
> If you see MCP server folders for APIs **not** in the available list (`CE_PURCHASEORDER_0001`, `API_SCHED_AGRMT_PROCESS_SRV`, `CE_SUPPLIERLIST_0001`), the spec contained an unregistered API. Tell the coding agent: `"Remove the [name] MCP server asset — only CE_PURCHASEORDER_0001, API_SCHED_AGRMT_PROCESS_SRV, and CE_SUPPLIERLIST_0001 are registered on this tenant."` Then ask it to update `solution.yaml` and `asset.yaml` to remove the reference before deploying.

---

**Next step →** [Exercise 4 — Deploy & Test Your Agent](../ex04/README.md)
