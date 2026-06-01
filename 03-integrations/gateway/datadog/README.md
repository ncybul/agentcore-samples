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

> Simpler alternative for a single shared identity: send `DD_API_KEY` + `DD_APPLICATION_KEY` as HTTP headers to the MCP endpoint and skip DCR/Cognito/3LO. The trade-off is no per-user RBAC or attribution.

## Tutorial

- [Integrate Datadog Remote MCP Server into AgentCore Gateway](01-datadog-mcp-server-target.ipynb)
