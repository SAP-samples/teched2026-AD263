# Exercise 5 — Bonus Exercise

Congratulations on completing the main scenario! If you have time remaining, try the bonus exercise below.

---

## Bonus — PRD as Input (Different Use Case)

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
