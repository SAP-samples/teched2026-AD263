# Exercise 2 — Review Intent, PRD & Specification

In this exercise you will review the three documents IBD generates before writing any code: `intent.md`, `product-requirements-document.md`, and `specification.md`. Work through each checklist before issuing the next command.

> **Note — Your agent, your choices**
> The generated documents reflect your exact input from Exercise 1. They may differ from the examples here — that is expected and intentional. Review the intent behind each checklist item, not the exact wording.
>
> **IBD variance is normal.** The document structure, section names, and content depth vary between runs and between participants — even with the same prompt. This is IBD working as designed: it adapts to your specific intent. Use the checklists below to verify the substance is correct, not to match the screenshots exactly.

---

## Step 2.1 — Review `intent.md`

IBD generates `intent.md` automatically after the clarifying questions in Exercise 1. Open it from the VS Code Explorer panel.

**Check the following:**

- [ ] SAP Source-to-Pay is identified as the business process domain
- [ ] SAP S/4HANA Public Cloud is named as the source system
- [ ] A fit-gap analysis was conducted (existing APIs vs. agent requirements)
- [ ] A pro-code Python AI agent is the recommended solution type


Scroll through the document — it covers business goals, milestones, RBA mapping, fit-gap analysis, and recommendations:

![intent.md open in VS Code — terminal review output with intent fit score](images/01_intent_md_terminal_review.png)

> **💡 Refining intent**
> If something important is missing, type a correction in the coding agent before proceeding. Example:
> `"Also add: the risk score must show the scoring rationale for each line item, not just the label."`

When you are satisfied, type in the coding agent to proceed:

```
Continue with PRD
```

![intent.md reviewed — typing "Continue with PRD" in the coding agent](images/01_intent_reviewed_continue_with_prd.png)

---

## API Availability on This Tenant

Use this table when reviewing the PRD and specification. IBD may discover APIs that are valid in the SAP API catalogue but not available on this workshop tenant — those will fail at deployment.

| API ORD ID                                            | Description                                  | Available | Why |
| ----------------------------------------------------- | -------------------------------------------- | --------- | --- |
| `sap.s4:apiResource:CE_PURCHASEORDER_0001:v1`         | Purchase Orders (Cloud Edition)              | ✅ Yes     | Registered and available on the workshop tenant |
| `sap.s4:apiResource:API_SCHED_AGRMT_PROCESS_SRV:v1`   | Scheduling Agreements                        | ❌ No      | Not registered on the workshop tenant — this is an on-premise API not available in S/4HANA Public Cloud |
| `sap.s4:apiResource:CE_SUPPLIERLIST_0001:v1`          | Preferred Supplier List (Cloud Edition)      | ❌ No      | Not registered on the workshop tenant — no access configured for this session |
| `sap.s4:apiResource:CE_SUPPLIERLIST_0002:v1`          | Preferred Supplier List v2                   | ❌ No      | Not registered on the workshop tenant |
| `sap.s4:apiResource:OP_SUPPLIERLIST_0002:v2`          | Preferred Supplier List (On-Premise Edition) | ❌ No      | On-premise edition — not applicable to S/4HANA Public Cloud |
| `sap.s4:apiResource:CE_SOURCINGSUPPLIERLIST_0001:v1`  | Sourcing Supplier List                       | ❌ No      | Not registered on the workshop tenant |
| `sap.s4:apiResource:API_BUSINESS_PARTNER:v1`          | Business Partner                             | ❌ No      | On-premise edition — not applicable to S/4HANA Public Cloud |
| `sap.s4:apiResource:API_PURCHASEORDER_PROCESS_SRV:v1` | Purchase Orders (On-Premise Edition)         | ❌ No      | On-premise edition — not applicable to S/4HANA Public Cloud |

> **💡 Why does IBD sometimes pick the wrong API?**
> IBD uses the SAP Knowledge Graph to discover APIs. The graph contains all published SAP APIs — including ones not registered on every tenant. The warnings in Steps 2.1–2.3 above tell you exactly how to correct the scope if IBD picks an unavailable API.


## Step 2.2 — Review the PRD

IBD generates `product-requirements-document.md`. VS Code will prompt you to save it — click **Yes, allow all edits during this session**.

![PRD generated — VS Code save prompt](images/02_prd_save_prompt.png)

Open it from the Explorer panel and review the full document:

![PRD open in VS Code editor — Product Purpose, Business Metrics, Solution Architecture](images/03_prd_open_in_editor.png)

**Check the following:**

- [ ] Business metrics are meaningful and match your input intent
- [ ] Personas for the use case are well defined
- [ ] Solution architecture lists exactly S/4HANA MCP servers that are avaliable on this tenant
- [ ] All milestones are represented as named functional requirements
- [ ] The composite risk scoring model (HIGH / MEDIUM / LOW) is described as a functional requirement


> **Note**
> In a real project this is where business analysts and stakeholders would review and sign off on the PRD. For this session, review the requirements above and move on.

When ready, type:

```
Create specification
```

![PRD reviewed — typing "Create specification", terminal confirms PRD saved](images/04_prd_reviewed_create_spec_command.png)

---

## Step 2.3 — Review the Specification

IBD generates `specification.md` inside `specification/<your-agent-name>/`. Open it.

**Check the following:**

- [ ] A `specification/` folder was created
- [ ] Exactly **one** S/4HANA MCP server is listed — only `CE_PURCHASEORDER_0001`
- [ ] A `risk_scoring` logic is described
- [ ] Guidance for the agent is captured in `.md` skill files

> **⚠️ Unregistered API in the spec?**
> If the spec lists any API other than `CE_PURCHASEORDER_0001`, **stop here** and correct it now. Deploying with an unregistered API in `asset.yaml` will fail with a tenant configuration error in Exercise 4. Tell the coding agent: `"Remove [API name] from the specification and only use CE_PURCHASEORDER_0001."` The spec is the direct input to implementation — fixing it here is much faster than fixing it after code is generated.

![specification.md — API Discovery Results, file structure, ready to execute](images/05_spec_api_discovery_execute_command.png)

When ready, type:

```
Execute specification
```

After execution, the full agent file structure is generated and the terminal confirms the spec is complete:

![Specification complete — API Discovery Results open, full file structure in Explorer](images/06_spec_complete_file_structure.png)

> **💡 MCP translation files**
> Each MCP server entry in the spec references a `translation.json`. This file maps OpenAPI/EDMX endpoints to named tools the agent can call — you will see them generated in Exercise 3.

---


## What Happened Behind the Scenes

| Document                           | Generated by             | Purpose                                                         |
| ---------------------------------- | ------------------------ | --------------------------------------------------------------- |
| `intent.md`                        | Intent analysis pipeline | Machine-readable problem definition with fit-gap analysis       |
| `product-requirements-document.md` | PRD generator            | Functional and non-functional requirements                      |
| `specification.md`                 | Spec generator           | Implementable task list the coding agent executes in Exercise 3 |

The specification is the direct input to the implementation step — the coding agent reads it and generates all files in Exercise 3.

---

**Next step →** [Exercise 3 — Implementation & Code Review](../ex03/README.md)
