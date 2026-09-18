# Exercise 5 — Bonus Exercises

Congratulations on completing the main scenario! If you have time remaining, choose one of the bonus exercises below. Each extends the agent in a different direction.

---

## Bonus A — Build an n8n Workflow to Trigger Your Agent

**What you build:** Connect your supplier disruption agent to an n8n workflow so it can be triggered by an automated system event rather than a manual Joule message.

**Scenario:** Instead of a procurement manager typing a message, a logistics monitoring system detects a disruption event and fires an A2A call directly to your agent.

Go back to the coding agent and type:

```
Design an n8n workflow that calls my agent when a supplier disruption
event is detected. The workflow should pass the event type, affected country,
and disruption date as input.
```

Review the generated workflow definition, then ask a session guide for access to the pre-configured n8n instance to test it.


---

## Bonus B — PRD as Input (Different Use Case)

**What you build:** Instead of starting from an intent prompt, you provide a pre-written PRD to drive a completely different agent — demonstrating that the IBD flow works from any starting point.

**Scenario:** A new employee is hired in SuccessFactors. The agent automatically provisions their cost center in S/4HANA, enriches their profile, and raises an equipment purchase requisition in Ariba — pausing for manager approval before submitting — all without a single manual handoff.

**Steps:**

1. Create a new folder on your Desktop (e.g. `onboarding-agent`)
2. Copy [`onboarding-agent-prd.md`](onboarding-agent-prd.md) from this exercise folder into it
3. Open the folder in VS Code and run:

   ```bash
   jl init claude
   ```

4. In the coding agent, type:

   ```
   Use the PRD at ./onboarding-agent-prd.md to implement the agent.
   ```

5. Compare what IBD generates from the PRD vs. what it generated from the intent prompt in Exercise 1 — which produced a more precise specification?

---

## Back to start

[← Session overview](../../README.md)
