# Integrate Datadog Remote MCP Server with AgentCore Gateway

## Overview
This tutorial demonstrates how to integrate the [Datadog Remote MCP Server](https://docs.datadoghq.com/bits_ai/mcp_server/) with Amazon Bedrock AgentCore Gateway, giving agents a single, governed interface to Datadog observability tools (logs, metrics, traces, monitors, incidents, LLM Observability, security signals, and more).

The integration uses **OAuth 2.1 3LO (Authorization Code + PKCE)** via **Dynamic Client Registration (DCR)**, so each tool call executes under the *authorized user's* Datadog RBAC, Data Access Controls, and audit identity. Per-user attribution is preserved end-to-end.

## Tutorial Details

| Information          | Details                                                              |
|:---------------------|:---------------------------------------------------------------------|
| Tutorial type        | Interactive                                                          |
| AgentCore components | AgentCore Gateway, AgentCore Identity                                |
| Agentic Framework    | Strands Agents                                                       |
| Gateway Target type  | MCP server                                                           |
| Agent                | Strands                                                              |
| Inbound Auth IdP     | Amazon Cognito                                                       |
| Outbound Auth        | OAuth 2.1 3LO (Authorization Code + PKCE) via Dynamic Client Reg.    |
| LLM model            | Anthropic Claude Sonnet 4                                            |
| Tutorial components  | Creating AgentCore Gateway and Invoking AgentCore Gateway           |
| Tutorial vertical    | Observability                                                        |
| Example complexity   | Intermediate                                                         |
| SDK used             | boto3                                                               |

## Key Features

* Integrate the Datadog Remote MCP Server with AgentCore Gateway
* Configure OAuth 2.1 3LO authentication for Datadog via Dynamic Client Registration
* Search and invoke Datadog observability tools through the Gateway
* Use Strands agents to interact with Datadog capabilities with per-user RBAC

## Key facts about the Datadog Remote MCP Server

* **MCP endpoint (US1):** `https://mcp.datadoghq.com/api/unstable/mcp-server/mcp` — for other sites, swap the host (e.g. `mcp.datadoghq.eu`).
* **Auth:** OAuth 2.1 **3LO only** (Authorization Code + PKCE). `client_credentials` (2LO) is not supported.
* **No OAuth app portal** — credentials are obtained via **Dynamic Client Registration** at `.../mcp-server/register`. AgentCore's callback URI pattern is pre-allowlisted on the Datadog side.
* **Discovery:** Datadog serves OAuth 2.0 discovery (`.well-known/oauth-authorization-server`), **not OIDC** — so configure the endpoints manually in AgentCore.
* **Toolset scoping:** append `?toolsets=core,llmobs,...` to the endpoint (default: `core`).
* **Permissions:** the Datadog user needs `mcp_read` (and `mcp_write` for write tools) plus the relevant resource permissions.

## Tutorials

Two variants, differing only in the **outbound** auth to Datadog:

- **[01 — OAuth 2.1 3LO](01-datadog-mcp-server-target.ipynb)** *(per-user)* — Dynamic Client Registration + a per-user browser Authorize step. Each call runs under the authorized user's Datadog RBAC, Data Access Controls, and audit identity. Best for multi-user agent platforms.
- **[02 — API key](02-datadog-mcp-server-target-apikey.ipynb)** *(shared identity)* — `DD_API_KEY` + `DD_APPLICATION_KEY` headers; no DCR, no browser Authorize. Simpler to run; all calls share one identity (no per-user attribution). Best for demos and internal automation. Available to any Datadog customer.

Both keep Cognito JWT inbound auth and register the Datadog MCP server as the gateway target.

### Which to use

| | OAuth 3LO (01) | API key (02) |
|---|---|---|
| Setup | DCR + browser Authorize | Paste two keys |
| Identity | Per-user | Single shared |
| Datadog RBAC / audit | Per-user | Coarse (one identity) |
| Best for | Multi-user platforms | Demos, internal automation |

### Two-header note (API-key variant)

Datadog needs two headers but a gateway target allows one credential provider. Notebook 02 injects `DD_API_KEY` via an API-key credential provider and forwards `DD_APPLICATION_KEY` from the client via header propagation (`metadataConfiguration.allowedRequestHeaders`).
