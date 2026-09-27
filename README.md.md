# Customer Support AI Agent — Amazon Bedrock AgentCore

An AI-powered customer support agent for a fictional e-commerce store, built with
Amazon Bedrock AgentCore and the Strands SDK. The agent handles order tracking,
refunds, product/policy questions, loyalty discount calculations, and live web
browsing — all through a single conversational interface, with memory that
persists across sessions.

This project was built as part of the **Udacity AWS AI Engineering Nanodegree**.

## What it does

- **Order tracking & refunds** — calls two AWS Lambda functions through the
  AgentCore Gateway using the Model Context Protocol (MCP)
- **Product & policy Q&A (RAG)** — retrieves answers from a Bedrock Knowledge
  Base built on a product catalog
- **Cross-session memory** — remembers a customer's name and preferences
  across separate conversations using AgentCore Memory
- **Loyalty discount calculation** — runs exact arithmetic in a secure
  AgentCore Code Interpreter sandbox, with a fallback if the sandbox is
  unavailable
- **Live web browsing** — fetches real-time page content using the AgentCore
  Browser tool

## Architecture

- **Runtime**: Amazon Bedrock AgentCore Runtime (`BedrockAgentCoreApp`)
- **Agent framework**: Strands SDK (`Agent`, `@tool`, hooks)
- **Model**: Amazon Nova Lite
- **Tool integration**: AgentCore Gateway (MCP) → API Gateway REST proxy
  (order tracking) + direct Lambda ARN target (refunds)
- **Knowledge base**: Amazon Bedrock Knowledge Base (Managed vector store)
- **Memory**: AgentCore Memory with two strategies — semantic extraction
  (`customer_facts`) and user preference (`customer_preferences`)
- **Compute**: AgentCore Code Interpreter (loyalty discounts), AgentCore
  Browser (live web access)

## Repo contents

```
main.py                  Completed agent code (all TODOs implemented)
lambda/
  order_tracker.py       Order/customer lookup Lambda
  refund_processor.py    Refund processing Lambda
  lambda_schema          Tool schema for the refund Gateway target
product_catalog.txt      Source data for the Knowledge Base
pyproject.toml           Project dependencies (uv-managed)
uv.lock                  Locked dependency versions
reflection.md            Written reflection on design, challenges, production notes
screenshots/             Terminal output for all 6 functional tests
```

## Functional tests

All six required test scenarios passed against the deployed agent:

1. **Order tracking** — status, tracking number, carrier, delivery date
2. **Refund processing** — refund ID, approval status, timeline
3. **Knowledge base (RAG)** — loyalty tier benefits pulled from the KB
4. **Cross-session memory** — introduced in one session, recalled in a new one
5. **Loyalty discount calculation** — exact points/discount math via Code
   Interpreter
6. **Browser tool** — live page title retrieved from a real website

See `screenshots/` for the terminal output of each run.

## Notes

- Deployed via the `bedrock-agentcore-starter-toolkit` (`agentcore configure`
  / `agentcore deploy` / `agentcore invoke`).
- The AgentCore Gateway uses the **NONE** inbound authorizer, matching the
  unauthenticated connection the agent code uses (`MCPClient` with no bearer
  token or AWS request signing). This is appropriate for this educational
  environment only — a production deployment would use a proper authorizer
  (IAM or OAuth) with matching client-side authentication.
- Setup scripts used for the AWS lab environment (credentials, PATH fixes)
  are intentionally excluded from this repo.
