# Troubleshooting

Common issues encountered during the AD263 workshop, with root causes and fixes.

---

- [TS-01 — 400: Input is too long for requested model](#ts-01--400-input-is-too-long-for-requested-model)

---

## TS-01 — 400: Input is too long for requested model

### What you see

The agent responds with a generic error in Joule Studio:

> *I encountered an error while processing your request. Please try again.*

Opening the **Console → Logs** panel shows an `Error` entry:

```
litellm.exceptions.BadRequestError: litellm.BadRequestError: Error code: 400 —
  "400 - LLM Module: Input is too long for requested model."
```

![Error visible in Joule Studio chat and logs](images/ts01_error_in_chat.png)

### Root cause

Your agent was generated with **all tools** from the connected MCP server — in this case 228+ tools from `sap-s4-ce-purchaseorder-0001-mcp-server` alone, totalling **302 tools** across both servers.

![MCP server with 228 tools listed](images/ts01_mcp_tools_228.png)

Every tool definition (name + description + parameter schema) is sent to the LLM as part of each request. 302 tool definitions fill the context window before the agent can even begin reasoning — the LLM rejects the request with a 400 before it processes the user message.

The fix is to keep only the tools your agent actually needs.

### Fix — send the log to the coding agent

Download the logs from Joule Studio (**Console → Logs → Download logs**) and send the file to the coding agent with this prompt:

```
I'm getting a 400 "Input is too long for requested model" error when running my agent.
Here are the logs: <attach the downloaded log file>

Please reduce the MCP tools to only the ones this agent actually needs.
```

The coding agent will read the log, identify which tools are referenced in your agent instructions, and update `asset.yaml` to include only those tools — reducing the tool list from 300+ to the handful your agent calls.

> **Why this works**
> The `asset.yaml` file controls which tools from each MCP server are exposed to your agent. By listing only the tools needed (e.g. `list_purchaseorder_for_sap_self`, `list_a_purchaseorderitem_for_api_purchaseorder_process_srv`, `get_a_purchaseorder_for_api_purchaseorder_process_srv`), the combined tool definitions fit well within the model's context window.

After the coding agent updates `asset.yaml`, redeploy:

```
deploy the agent
```
