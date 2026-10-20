# Exercise 4 — Deploy & Test Your Agent

In this exercise you will deploy your agent to Joule Studio and run the full supplier disruption scenario via Joule Work.

---

## Step 4.1 — Validate and Deploy

Ask the coding agent to deploy:

```
deploy the agent
```

The agent is packaged and deployed to SAP managed Joule Studio runtime on the provided landscape. **Deployment includes full end-to-end integration — expect it to take several minutes.**

Before packaging, `jl solution validate` runs automatically. It checks 9 rules across your solution — from confirming `solution.yaml` exists and is schema-valid, to verifying every asset folder has a proper `asset.yaml`, every file reference resolves, and every agent uses a known LLM model. Errors block the deploy; warnings are advisory. If validation fails, the output tells you exactly which rule failed and what to fix.

**While the deployment is running — open your browser and navigate to Joule Studio.** Your solution will appear in the Web UI as it registers. Watch for it there — once it shows up you can open it and observe its status directly.

---

### While you wait — generate test prompts locally

While deployment is in progress, use this time to prepare your test prompts. The mock data (enabled via `IBD_TESTING=1` in your `asset.yaml`) uses a fixed dataset, so the prompts below are calibrated to it and will give you predictable, verifiable results.

Ask the coding agent:

```
generate 5 prompts to test the agent locally
```

Or use these ready-made prompts — each targets a different capability of the agent:

**1. Full triage — the core use case**
```
Supplier TW-VENDOR-001 just had a factory fire in Taiwan. Show me all their open
scheduling agreements, score each item by risk, and find me preferred alternatives.
```

**2. Single agreement deep-dive**
```
Give me a full risk breakdown for scheduling agreement 5500000001, including the
delivery date, open quantity, and total exposure value.
```

**3. Alternative supplier check**
```
For material group L001 — Electronic Components — who are our preferred alternative
suppliers and what are their evaluation scores?
```

**4. Geographic risk filter**
```
Which of our active scheduling agreements have delivery addresses in Taiwan?
Score them and tell me which ones are HIGH risk.
```

**5. Executive summary**
```
We have a supply disruption with TW-VENDOR-001. Give me a one-page summary: how many
agreements are at risk, what's the total open value in EUR, and which alternative is
our best option based on scorecard rating?
```

---

## Step 4.2 — Test the Deployed Agent in the Web UI

Once your solution appears in the Joule Studio Web UI, open it and start a conversation directly from there. This confirms the agent is live and the end-to-end integration is working.

Use any of the five prompts above to run your first test against the deployed agent.

---

## Step 4.3 — Send A2A Messages from the Terminal

Come back to the terminal and send messages via the A2A protocol. Every deployed agent exposes an A2A endpoint — this is the same path an n8n workflow or another agent would use to call yours.

Open a new terminal tab and run:

```bash
jl a2a message "hi" --solution <your-solution-id> --asset <your-agent-name>
```

The solution ID is printed when deployment completes. The asset name matches the folder under `assets/`.

The agent should respond with a brief greeting. If it times out, ask a session guide to check the deployment status.

> **Note — Why A2A?**
> Testing via `jl a2a message` confirms the agent endpoint is reachable independently of the Joule UI — the same verification a machine-to-machine caller would do.

---

## Step 4.4 — Run the Disruption Scenario

Send the full test message:

```
Our supplier in Taiwan has announced a factory fire.
The disruption started on 2024-03-01. What purchase orders and scheduling
agreements are at risk?
```

### What to look for in the response

Work through this checklist:

**M1 — Signal intake**
- [ ] Agent confirms it received the event type (factory fire), country (Taiwan), and date (2024-03-01)
- [ ] Agent states it is beginning the triage sequence

**M2 — SAP Data Retrieval**
- [ ] Open purchase orders returned for suppliers in Taiwan, each labeled `[Confirmed Fact]`
- [ ] Scheduling agreement lines returned, labeled `[Confirmed Fact]`

**M3 — Risk Scoring**
- [ ] Each line item is assigned HIGH, MEDIUM, or LOW
- [ ] Scoring rationale is shown (delivery proximity, open value, geographic overlap)
- [ ] Scoring thresholds are labeled `[Assumption]`
- [ ] Results sorted HIGH → MEDIUM → LOW

**M4 — Consolidated Risk Report**
- [ ] Risk-scored exposure summary presented with all items labeled `[Confirmed Fact]` or `[Assumption]`
- [ ] Recommended actions labeled `[Recommendation]`

> **💡 Use OTEL traces to debug**
> If the agent skips a milestone or produces unexpected output, open the **Tracing** tab in Joule Studio — each span shows which tool was called, with what inputs, and how long it took. This is the fastest way to identify instruction drift or a misconfigured tool.

---

## Summary

```
Taiwan factory fire signal
        ↓
M1  Disruption intake confirmed
        ↓
M2  Open POs + scheduling agreements retrieved  [Confirmed Fact]
        ↓
M3  Risk scored: HIGH / MEDIUM / LOW with rationale  [Assumption for thresholds]
        ↓
M4  Consolidated risk report presented  [Recommendation]
```

**Next step →** [Exercise 5 — Bonus Exercises](../ex05/README.md) — if you have time remaining.

Or head back to the [session overview](../../README.md).
