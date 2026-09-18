# Exercise 4 — Deploy & Test Your Agent

In this exercise you will deploy your agent to SAP AI Core and run the full supplier disruption scenario via SAP Joule.

---

## Step 4.0 — Configure Mock Data Mode

Before deploying, open the agent's `asset.yaml`. You'll find it inside the generated agent folder:

```
assets/<your-agent-name>/asset.yaml
```

Add an `env` block under the `container:` section:

```yaml
container:
  buildPath: .
  port: 5000
  env:
    - name: IBD_TESTING
      value: "1"
```

> **Note — Why this matters**
> With `IBD_TESTING=1` the agent runs against mock data instead of live S/4HANA APIs. This is the correct mode for this workshop — results are predictable and the exercise works regardless of whether the underlying S/4 services are fully provisioned on the landscape.
>
> Remove this flag only when you have a verified live landscape with all APIs reachable.

---

## Step 4.1 — Validate and Deploy

Ask the coding agent to deploy:

```
deploy the agent
```

The agent is packaged and deployed to SAP managed Joule Studio runtime on the provided landscape. This takes **1–2 minutes**.

In the background, `jl solution validate` runs automatically — it checks your generated code against the deployer rules and reports any issues without you doing anything.

When deployment completes you will see a confirmation with the deployment endpoint URL.

> **💡 Bonus: Monitor your agent in the web UI**
> You can open Joule Studio in your browser while the agent deploys — it will appear there with the same folder structure once registered. There you can also share the solution with a business user for review.

---

## Step 4.2 — Verify the Agent is Reachable

Once deployed, test connectivity. You can do this in three ways:

Option A - Joule Studio web UI: if you agent is in status integrating you can go to the agent file and test your agnet 
**Option A — Joule Studio web UI:** Open the deployed agent in Joule Studio and start a conversation directly from there.

**Option B — Coding agent:** Ask the coding agent:

```
Use Joule to send a 'hi' to my agent.
```

**Option C — A2A CLI:** Every deployed agent exposes the A2A protocol endpoint. Open a new terminal tab and send a message directly:

```bash
jl a2a message "hi" --solution <your-solution-id> --asset <your-agent-name>
```

The solution ID is printed when deployment completes. The asset name matches the folder under `assets/`.

The agent should respond with a brief greeting in all three cases. If it times out, ask a session guide to check the deployment status.

> **Note — Why A2A?**
> The A2A protocol is how machine-to-machine callers reach your agent — the same path an n8n workflow or another agent would use. Testing via `jl a2a message` confirms the agent endpoint is reachable independently of the Joule UI.

---

## Step 4.3 — Run the Disruption Scenario

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
