# Exercise 4 — Deploy & Test Your Agent

In this exercise you will deploy your agent to Joule Studio and run the full supplier disruption scenario via Joule Work.

---

## Step 4.1 — Validate and Deploy

Ask the coding agent to deploy:

```
deploy the agent
```

The agent is packaged and deployed to SAP managed Joule Studio runtime on the provided landscape. **Deployment includes full end-to-end integration — expect it to take several minutes.**

> **Deployment taking longer than expected?** See [TS-03 — Deployment stuck on "Integrating"](../../troubleshooting/README.md#ts-03--deployment-stuck-on-integrating)

Before packaging, `jl solution validate` runs automatically. It checks 9 rules across your solution — from confirming `solution.yaml` exists and is schema-valid, to verifying every asset folder has a proper `asset.yaml`, every file reference resolves, and every agent uses a known LLM model. Errors block the deploy; warnings are advisory. If validation fails, the output tells you exactly which rule failed and what to fix.

---

### What's happening now: Joule Studio runtime

Your agent is being deployed to Joule Studio runtime — SAP's managed execution environment for enterprise AI agents. Here's what's happening under the hood while you wait.

You build it. We run it. Joule Studio runtime provides the operational foundation to build, deploy, observe, and scale enterprise AI applications and agents, delivering trusted guardrails, integration, and extensibility. While you own the AI logic — the intent, the rules, the risk appetite, the business value — we own the runtime: from infrastructure to lifecycle to integration.

**Trusted guardrails.** Agents run in ephemeral, sandboxed containers with no persistent state or cross-tenant access, enforcing strict isolation at execution time. Agent lifecycle management — versioning, start, stop, and rollback — is handled through the runtime, ensuring every agent instance meets the same quality, security, and lifecycle standard. SAP's own agents run on this same managed runtime, discoverable and activatable through the SAP Discovery Center.

**Trusted integration.** Deployment is fully automated through a pipeline into Joule Studio runtime — no Kubernetes management, no manual infrastructure provisioning required. Out-of-the-box OpenTelemetry instrumentation delivers traces, metrics, and logs from day one, with AI actions consumption surfaced as a transparent cost metric for clear spend visibility. SAP handles provisioning, identity management, scaling, availability, and upgrades — the full operational layer.

**Trusted extensibility.** You can tailor agent behavior to your business context without touching the underlying agent code: Instruction extensions to adjust language, apply business policies, reorder results, or add clarification steps. Hooks (powered by n8n) to inject custom logic before or after agent execution: access checks, downstream system updates, approvals, or notifications — including connections to non-SAP systems via APIs and MCPs.

![Joule Studio Runtime: Agent flow](images/runtime_agent_flow.jpg)

---

### While you wait — explore your solution in the Web UI

Open Joule Studio at [https://747391938.eu10.sapdas.cloud.sap/new/](https://747391938.eu10.sapdas.cloud.sap/new/) (credentials provided by your facilitator). Your solution will appear there as it registers — open the agent and start testing it against the mocked data. No terminal needed.

![Solution appears in Joule Studio Web UI](images/01_solution_in_web_ui.png)

Ask the coding agent in the Web UI to generate prompts tailored to your mocked data — the sample prompts below use specific IDs that may differ in your dataset:

```
Please create 5 test prompts to test the agent against my mocked data
```

![Ask coding agent in Web UI to generate prompts](images/02_ask_coding_agent_for_prompts.png)

Or use the prompts below as inspiration — adapt them to the vendor IDs and agreement numbers in your own dataset:

> **No mock data?** If you used the fast-track setup, the Testing Overview may be empty and the Preview with Mock Data section may show no data. See [TS-02 — Testing Overview shows no test reports](../../troubleshooting/README.md#ts-02--testing-overview-shows-no-test-reports) — skip the prompts below and continue to Step 4.2.

**1. Full triage — the core use case**
```
Supplier TW-VENDOR-001 just had a factory fire in Taiwan. Show me all their open
purchase order items, score each item by risk, and find me alternative suppliers.
```

**2. Single PO deep-dive**
```
Give me a full risk breakdown for purchase order 4500000001, including the
delivery date, open quantity, and total exposure value.
```

**3. Alternative supplier check**
```
For material TW-COMP-007 — which other suppliers have delivered it in the past
based on our PO history? Include their supplier IDs.
```

**4. High-risk items only**
```
Which of our open purchase order items for supplier TW-VENDOR-001 are scored HIGH risk?
Show delivery dates and open values only.
```

**5. Executive summary**
```
We have a supply disruption with TW-VENDOR-001. Give me a one-page summary: how many
PO items are at risk, what's the total open value, and which alternative supplier is
our best option based on PO history?
```

---

## Step 4.2 — Your solution is green and running — test with real S/4HANA data

Once your solution status turns green (**Running**), you're live against the real SAP S/4HANA Public Cloud backend. Use the supplier table below to pick a real supplier ID and test the agent against live data.

### Available test suppliers

These suppliers have open purchase orders in the system. Use the supplier ID (the code in parentheses) as input to your agent.

| Supplier name | Supplier ID | Open POs (examples) | Net value range |
|---|---|---|---|
| EV Parts Inc. | `USSU_V8000` | 4500097084, 4500096300, 4500095068 | $75k – $115k |
| WaveCrest Labs | `USSU_V8001` | 4500095071 | $47k |
| Vendor V8003 | `USSU_V8003` | 4500095072, 4500079663 | $263k – $386k |
| Vendor V8004 | `USSU_V8004` | 4500096970, 4500086028 | $21k – $42k |

You can verify PO data directly in S/4HANA (same login as your workshop account):
[Open Purchase Orders in S/4HANA →](https://my444255.s4hana.cloud.sap/sap/bc/ui2/flp?sap-language=en&sap-shell=FLP&sap-touch=0#PurchaseOrder-manage?sap-ach=MM-FIO-PUR-PO&sap-keep-alive=restricted)

### Send the disruption prompt

Pick a supplier from the table and send this to your agent — replace the supplier ID with one from the table:

```
Supplier USSU_V8003 has announced a production halt. Triage all their open purchase orders.
```

![Send prompt to agent against mocked data](images/03_send_prompt_mocked_data.png)

![Disruption scenario sent to deployed agent](images/04_disruption_scenario_sent.png)

### What to look for in the response

Work through this checklist:

**M1 — Signal intake**
- [ ] Agent acknowledges the supplier ID and begins triage

**M2 — SAP Data Retrieval**
- [ ] Open purchase order items returned for the supplier, each labeled `[Confirmed Fact]`

**M3 — Risk Scoring**
- [ ] Each line item is assigned HIGH, MEDIUM, or LOW
- [ ] Scoring rationale is shown (delivery proximity, open value, geographic overlap)
- [ ] Scoring thresholds are labeled `[Assumption]`
- [ ] Results sorted HIGH → MEDIUM → LOW

**M4 — Consolidated Risk Report**
- [ ] Risk-scored exposure summary presented with all items labeled `[Confirmed Fact]` or `[Assumption]`
- [ ] Alternative suppliers listed for HIGH and MEDIUM items, or "No alternative supplier found in PO history"
- [ ] Recommended actions labeled `[Recommendation]`

![Disruption scenario response with risk-scored report](images/05_disruption_scenario_response.png)

> **💡 Use OTEL traces to debug**
> If the agent skips a milestone or produces unexpected output, open the **Tracing** tab in Joule Studio — each span shows which tool was called, with what inputs, and how long it took. This is the fastest way to identify instruction drift or a misconfigured tool.

> **Getting a 400 "Input is too long" error?** See [TS-01 — 400: Input is too long for requested model](../../troubleshooting/README.md#ts-01--400-input-is-too-long-for-requested-model)

---

## Step 4.3 — Optional: Try Other Interfaces

Your agent is now live and reachable from multiple surfaces. Pick one or try all three:

**Option A — A2A via CLI**

Open a new terminal tab and send the disruption scenario directly:

```bash
jl a2a message "Supplier USSU_V8003 has announced a production halt. Triage all their open purchase orders." --solution <your-solution-id> --asset <your-agent-name>
```

The solution ID is printed when deployment completes. The asset name matches the folder under `assets/`.

**Option B — Joule Work conversation**

First, find your agent's Asset ID. In Joule Studio, go to **Manage → Overview** and look at the asset table — copy the Asset ID next to your AI Agent (it looks like `your-agent-name-xxxxx`):

![Joule Studio Manage Overview — Asset ID column with @ symbol highlighted](images/06_joule_work_asset_id.png)

Open SAP Joule Work, start a new conversation, type `@` and paste the Asset ID, then add the prompt:

```
@<your-asset-id> Supplier USSU_V8003 has announced a production halt.
Triage all their open purchase orders.
```

![Joule Work — successful agent response with supply disruption impact report](images/07_joule_work_success.png)

> **Note — Why this matters**
> A2A is how machine-to-machine callers reach your agent — the same path an n8n workflow or another agent would use. Joule Work is the business-user surface. Testing both confirms your agent works end-to-end across all entry points.

> **First run not working?** See [TS-05 — Agent unavailable on first Joule Work run](../../troubleshooting/README.md#ts-05--agent-unavailable-on-first-joule-work-run)

---

## Summary

```
Supplier production halt signal (e.g. USSU_V8003)
        ↓
M1  Disruption intake confirmed
        ↓
M2  Open PO items retrieved  [Confirmed Fact]
        ↓
M3  Risk scored: HIGH / MEDIUM / LOW with rationale  [Assumption for thresholds]
        ↓
M4  Consolidated risk report presented  [Recommendation]
```

**Next step →** [Exercise 5 — Bonus Exercises](../ex05/README.md) — if you have time remaining.

Or head back to the [session overview](../../README.md).
