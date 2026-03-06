# Hackathon Learnings: Agentic Factory

This document tracks the key concepts, technical skills, and architectural patterns learned throughout the Agentic Factory hackathon challenges.

## Challenge 0: Environment Setup
*   **Infrastructure as Code & CI/CD**: Hands-on experience deploying complex Azure resource groups (including OpenAI, Cosmos DB, API Management) using pre-configured Bicep templates via GitHub Actions.
*   **Data Seeding**: Understanding the foundational data model for the factory scenario—Machines, Warning Conditions, Technicians, and Parts Inventory—and how it maps to Azure Cosmos DB.
*   **Configuration Management**: Managing environment variables securely (using `.env`) to orchestrate local development against cloud resources without hardcoding credentials.

## Challenge 1: Fault Diagnosis Agent
*   **Foundry Agents SDK (Python)**: Building a Python-based intelligent agent capable of reasoning over real-time factory data.
*   **Function Calling / Tool Use**: Teaching an LLM to interact with external systems. We implemented and registered custom tools (e.g., fetching thresholds, retrieving live sensor readings) allowing the agent to dynamically gather context before diagnosing issues.
*   **Knowledge Base Integration (RAG)**: Leveraging Foundry IQ to provide the agent with unstructured domain knowledge (e.g., machinery manuals). This enables the agent to cross-reference live sensor anomalies with documented troubleshooting steps to determine root causes.

## Challenge 2: Repair Planner Agent
*   **Foundry Agents SDK (.NET)**: Implementing agentic workflows in C# using `Azure.AI.Projects` and `Microsoft.Agents.AI`, highlighting that multi-agent systems can be multilingual based on the best tool/team for a specific service.
*   **Data Integration (Cosmos DB)**: Writing a service layer to interact with Cosmos DB—querying available technicians by required skills and checking parts inventory against part numbers.
*   **Structured Outputs**: Using the LLM to generate highly structured, predictable JSON outputs (a complete Work Order) rather than just conversational text.
*   **GitHub Copilot Custom Agents**: Using the `@agentplanning` tool in VS Code to accelerate boilerplate generation and ensure adherence to best practices via custom system prompts and local workspace context.

## Challenge 3: Maintenance Scheduler & Parts Ordering (Completed)
*   **Agent Memory Context**: Implemented persistent chat history stored in Azure Cosmos DB, allowing intelligent agents to recall prior conversations (per-machine and per-work order). This ensures consistent reasoning, context-aware decision-making, and coherent multi-step tasks over time without requiring the LLM to start from scratch.
*   **System Observability (Azure Monitor & Application Insights)**: Integrated the `azure-ai-inference[tracing]` package and OpenTelemetry to automatically capture end-to-end execution timelines. This includes monitoring AI token usage, underlying model latency, tool execution, and database I/O, which is essential for diagnosing failures and optimizing costs in production.
*   **Design Considerations for Agent Operations**: Distinguished when data retrieval should happen via traditional application code (e.g., standard Cosmos DB queries for predictable, required input data) versus when it should be exposed as an MCP tool (for adaptive problem solving). In these agents, deterministic data loading was preferred to focus the LLM solely on complex risk assessment and scheduling logic.

## Challenge 4: End-to-End Agent Workflow with Aspire (Completed)
*   **.NET Aspire Orchestration**: Operated a polyglot agent workflow where Azure AI Foundry agents (Python) were orchestrated alongside local C# logic in a single unified system, with Aspire acting as the centralized host and telemetry provider.
*   **Sequential Workflow Pattern**: Deployed a step-by-step sequential orchestration (from Anomaly Classification -> Diagnosis -> Repair -> Scheduling -> Parts Ordering) ensuring strict dependency tracking and deterministic, explainable multi-stage processing. 
*   **Agent-to-Agent (A2A) Interface**: Leveraged standard invocation boundaries so that agents implemented in various languages or hosting topologies (e.g., Azure API Management or Foundry Agent Service) could fluidly communicate and pass context to each other autonomously.

*(Future challenges will be added here as we progress)*