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

4. Open `onboarding-agent-prd.md` and read through it — notice it was written by hand, without API ORD IDs or a fit-gap analysis.

5. In the coding agent, type:

   ```
   I have a PRD at ./onboarding-agent-prd.md. Use it as input to regenerate a proper IBD PRD with API discovery from the SAP Knowledge Graph — including ORD IDs, fit-gap analysis, and capability coverage. Do not create a specification yet.
   ```

6. When the new `product-requirements-document.md` is generated, compare it to the original hand-written PRD:
   - Which APIs did IBD discover? Do they have ORD IDs?
   - What did the fit-gap analysis surface that the hand-written PRD missed?
   - Which requirements changed or became more precise?

7. When you're ready to build, type:

   ```
   Create specification
   ```

8. Compare what IBD generates from the PRD vs. what it generated from the intent prompt in Exercise 1 — which starting point produced a more precise specification?

---

## Back to start

[← Session overview](../../README.md)
