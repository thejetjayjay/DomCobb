# Claude Code Agent Best Practices

**Version:** 1.0  
**Date:** 2025-11-28  
**Topic:** Best Practices for Building Agents with Claude Code and the Anthropic Platform

---

## Executive Summary

This document provides a comprehensive set of expert-level best practices for designing, building, and deploying robust and effective agents using **Claude Code** and the **Anthropic platform**. The techniques described here are synthesized from official documentation, engineering posts, industry guides, and real-world implementations, aiming to provide actionable standards for creating production-grade agentic systems.

AI agents are evolving from simple tools to complex autonomous systems that can perform multi-step tasks. **Claude Code**, with its strong coding capabilities and development environment integration, is at the forefront of this transformation. However, building reliable agents that operate consistently in complex scenarios requires a disciplined engineering approach. This guide describes the essential patterns and practices for success.

**Key Claude Code-specific features covered:**

- **`CLAUDE.md` files**: Persistent project context that Claude Code automatically ingests to adapt to your project's conventions
- **Claude Agent SDK**: Pre-built tools for file operations, code execution (`bash`), web search, and text editing with automatic context compaction
- **Agent Skills (`SKILL.md`)**: Self-contained skill directories for progressive disclosure of domain-specific capabilities
- **Model Context Protocol (MCP)**: Standardized integration layer for third-party tools and external services
- **State artifacts**: Files like `claude-progress.txt` and `feature_list.json` for maintaining agent state across sessions
- **Git-based memory**: Using commit history as reliable long-term memory for coding agents
- **Tool permissions**: `allowedTools` and `disallowedTools` for scoped access control
- **Model right-sizing**: Using Haiku for simple tasks and Opus for complex reasoning within multi-agent systems

---

## Purpose and Expected Results

### What You Will Learn

This document provides practical, execution-oriented guidance for building agents with Claude Code that work reliably in production. You will learn how to:

- Structure system prompts using the Role-Task-Output Format pattern for predictable Claude Code agent behavior
- Configure `CLAUDE.md` files to provide persistent project context
- Design tools that Claude Code can use effectively, following the "design for the model, not for humans" principle
- Implement Agent Skills with `SKILL.md` files for progressive disclosure
- Integrate external services using the Model Context Protocol (MCP)
- Select appropriate orchestration patterns (single agent, deterministic chains, supervisor/worker, etc.)
- Manage memory and context using Claude Agent SDK's automatic compaction, state files, and Git history
- Implement robust error handling with idempotent tools, stateful recovery, and Git rollback
- Incorporate human oversight at critical decision points
- Test and validate Claude Code agents using sandbox environments and evaluation benchmarks
- Build observable agents with tracing, metrics, and feedback logging
- Secure agents using least privilege, `allowedTools`, and sandbox execution
- Deploy to production with prompt caching, response streaming, and model right-sizing

### Expected Results

After applying these practices, you will be able to:

- Build Claude Code agents that behave predictably and reliably across complex tasks
- Maintain agent state across sessions using structured files and Git history
- Implement multi-agent systems with proper coordination and quality gates
- Create agents that recover autonomously from failures using Git rollback
- Test and validate agentic systems comprehensively in sandbox environments
- Deploy secure, observable production agents with optimized cost and latency
- Avoid the common pitfalls that cause Claude Code agent projects to fail

### Who Should Read This

This guide is designed for:

- AI engineers and developers building agentic systems with Claude Code
- Teams using Claude Code for autonomous development workflows
- Architects designing multi-agent architectures on the Anthropic platform
- DevOps and platform engineers deploying Claude Code agents
- Security professionals ensuring safe Claude Code deployments

### Prerequisites

To get the most value from this document, you should have:

- Basic understanding of large language models and prompting
- Familiarity with Claude Code and the Anthropic platform
- Experience with API-based systems and development workflows
- Awareness of software engineering best practices (Git, testing, security)

---

## Best Practices

### 1. System Prompts and Instructions

The foundation of any effective Claude Code agent is a well-crafted system prompt. This prompt defines the agent's role, goal, constraints, and personality. For Claude Code, clear and direct instructions are crucial for reliably guiding its behavior. Best practices for agent prompt engineering have evolved into robust patterns that drastically increase reliability.

| Pattern | Description | When to Use |
|---------|-------------|-------------|
| **Role, Task, Output Format** | Explicitly define the agent's persona (Role), what it should accomplish (Task), and how it should present output (Format). | Always. This is the fundamental pattern for predictability. Use it as the base structure of all system prompts. |
| **Progressive Disclosure** | Provide the agent with only the information needed at each step. Start with high-level metadata (e.g., skill names and descriptions) and allow the agent to "pull" additional details by reading files as needed. | In complex agents with many tools or skills. This prevents overloading the context with irrelevant details, improving efficiency and focus. [19] |
| **Few-Shot Prompting** | Include concrete examples of desired interactions or outputs directly in the prompt. This is more effective than just describing behavior. | When the output format is complex or non-standard. Use to teach the agent exact syntax for tool invocations or response formats. |
| **Chain of Thought (CoT)** | Instruct the agent to think step-by-step, articulating its reasoning before acting. Use XML tags like `<thinking>` to structure this process. | For complex tasks requiring planning or multiple steps. This improves reasoning quality and provides better observability into the agent's decision-making process. |
| **Project Context (`CLAUDE.md` files)** | Use a `CLAUDE.md` file at the project root to provide persistent context about the project, coding standards, architecture, and workflows. Claude Code automatically ingests this file to adapt to your project's specific conventions. | In all software development projects using Claude Code. This is a Claude Code-specific feature that enables project-aware behavior. [15] |

---

### 2. Tool Design and Integration

Tools are how Claude Code agents interact with the external world. Effective tool design is the difference between an agent that struggles and one that executes tasks reliably. The fundamental principle is to **design tools for the model, not for humans**. This means clear descriptions, simple arguments, and atomic scope.

| Pattern | Description | When to Use |
|---------|-------------|-------------|
| **Clear and Simple Interfaces** | Design tools with self-explanatory names, detailed descriptions, and simple required arguments. The description should explain what the tool does, its parameters, and what it returns. | Always. LLMs rely entirely on tool documentation to decide how and when to use it. Clarity beats brevity. [7] |
| **Atomic and Composable Tools** | Each tool should perform a single, well-defined logical task. Avoid monolithic tools that perform multiple actions. Create primitives that the agent can chain. | For most use cases. Smaller, focused tools are easier for the LLM to reason about, easier to test, and easier to compose into complex workflows. |
| **Agent Skills (`SKILL.md`)** | Group a set of related tools, prompts, and resources into a self-contained "skill" directory with a `SKILL.md` file. Use progressive disclosure to expose only skill metadata initially. | When an agent needs a complex set of domain-specific capabilities (e.g., "PDF editing" or "GCP log analysis"). This keeps the main prompt clean and allows the agent to dynamically load knowledge as needed. [19] |
| **Claude Agent SDK Tools** | Leverage the rich set of pre-built tools provided by the Claude Agent SDK, including file operations, code execution (`bash`), web search, and a text editor. | As a starting point for most Claude Code agents. Prefer these tested and optimized tools before building your own. [2] |
| **Model Context Protocol (MCP)** | For integrating third-party tools or external services, use MCP. It provides a standardized abstraction layer for interaction with external APIs. | When connecting to any service outside the agent's environment (e.g., Jira, Figma, Google Drive, internal databases). [1] [11] |
| **Scoped Tool Permissions** | Use allowlists (`allowedTools`) and blocklists (`disallowedTools`) to strictly restrict which tools an agent can access. Follow the principle of least privilege. | In all production environments. This reduces the attack surface and prevents the agent from executing unwanted or unexpected actions. [2] |

---

### 3. Agentic Loops and Orchestration

The agentic cycle (reason → decide → act → observe) is the engine of a Claude Code agent. Orchestration defines how these cycles are structured, whether as a single deliberative agent or as a multi-agent system. Choosing the correct orchestration pattern is fundamental for system efficiency and reliability.

| Pattern | Description | When to Use |
|---------|-------------|-------------|
| **Single Agent** | A single logic flow orchestrates multiple LLM and tool calls to handle requests. The agent can iterate (loop) to refine results until a goal is achieved. | The "sweet spot" for most enterprise use cases. Use when user queries are varied but within a cohesive domain, and you need more flexibility than a deterministic chain. [20] |
| **Deterministic Chain (Pipes and Filters)** | Chain agents in a predefined linear order. Each agent processes the output of the previous one, creating a pipeline of specialized transformations. The route is fixed. | For multi-step processes with clear linear dependencies and a predictable workflow (e.g., draft → review → polish). Use when steps cannot be parallelized. [14] |
| **Concurrent Orchestration (Fan-out/Fan-in)** | Execute multiple specialized agents simultaneously on the same task. Results are often aggregated to produce a final response. | For tasks that benefit from multiple independent perspectives (e.g., brainstorming, ensemble reasoning) or when parallelization can reduce latency. [14] |
| **Supervisor/Worker (Orchestrator-Workers)** | A central orchestrator agent dynamically decomposes tasks and delegates to specialized "worker" agents. The orchestrator then synthesizes results. | For complex problems where subtasks cannot be predicted in advance. Enables dynamic problem-solving. [1] |
| **Evaluator-Optimizer (Critique/Refine)** | A "generator" agent produces work, and an "evaluator" agent critiques it based on defined criteria. The process iterates until quality meets the standard. | When clear evaluation criteria exist and iterative refinement adds significant value. Useful for high-quality writing or coding tasks. [1] |

---

### 4. Multi-Agent Coordination

As task complexity increases, coordinating multiple specialized Claude Code agents often outperforms single-agent capabilities. Multi-agent systems enable specialization and parallelization but introduce coordination challenges. Choosing the correct communication and collaboration pattern is vital to avoid state synchronization failures and communication overhead.

| Pattern | Description | When to Use |
|---------|-------------|-------------|
| **Coordinator/Router (Supervisor)** | A "supervisor" agent or logical router analyzes incoming requests and routes them to the most appropriate worker agent based on the task. | When you have a set of specialized agents and need a single entry point to delegate work efficiently. Essential for multi-agent systems at scale to avoid chaos. [20] |
| **Group Chat** | Multiple agents collaborate in a shared "chat" environment. They can observe and react to each other's actions and messages to solve a task collaboratively, similar to a human team. | For complex problem-solving that benefits from debate, multiple perspectives, and iterative deliberation. Useful for tasks that are not clearly sequential. [14] |
| **Handoff** | One agent transfers control and relevant context to another specialized agent when the task crosses domain boundaries. State is packaged and passed forward. | In workflows where different stages require distinct knowledge or tools (e.g., from data analysis to code generation). Ensures the right specialist is always working on the task. [14] |
| **Reflection and Critique (Reviewer)** | One (or more) agents are dedicated to reviewing, validating, and providing feedback on another agent's work. This creates a refinement and verification loop. | For tasks requiring high accuracy or quality, where verification and iterative refinement are crucial. Reduces the probability of undetected errors. [20] |

---

### 5. Memory and Context Management

LLMs have finite context windows. Effective memory management is perhaps the most critical challenge in long-running Claude Code agent systems. Strategies must focus on providing the agent with the right context at the right time without exceeding token limits. The solution is a combination of automatic compaction and structured state artifacts that agents can read to orient themselves.

| Pattern | Description | When to Use |
|---------|-------------|-------------|
| **Automatic Context Compaction** | The Claude Agent SDK automatically manages context to ensure the agent doesn't run out of space. It summarizes or selectively removes older conversation parts to make room for new information. | Enabled by default in Claude Agent SDK. Essential for any Claude Code agent operating beyond a single context window. [2] |
| **Initializer Agent** | Use the first agent session to run a specialized "initializer agent." This agent's task is to set up the environment, create a resource/task list, and create initial state artifacts (e.g., a `claude-progress.txt` file). | For any complex, long-running task. This establishes a solid foundation for subsequent agents, preventing them from trying to solve the entire task at once. [18] |
| **Persistent State in Files** | Externalize agent state and task progress to structured files on disk (e.g., JSON for task lists, Markdown for progress logs). Agents read these files at the start of each session to orient themselves. Use files like `feature_list.json` and `claude-progress.txt`. | Whenever a Claude Code agent needs to maintain state between sessions. This is more reliable than relying solely on context window memory. [18] |
| **Git History as Memory** | Instruct Claude Code agents to commit their work to Git with descriptive commit messages after each successful incremental change. The Git log becomes reliable long-term memory of code changes. | In all coding agents. This allows agents to revert bad changes, recover known working states, and understand project history by reading `git log`. [18] |
| **Progressive Disclosure (Skills)** | Structure knowledge into "Skills" that the agent can dynamically load. The agent starts with only a brief description of each skill and reads the full `SKILL.md` content only when needed. | For agents needing access to a large body of domain-specific knowledge. This keeps the context window focused and relevant, avoiding information overload. [19] |

---

### 6. Error Handling and Recovery

Claude Code agent systems, especially multi-agent ones, can fail in complex ways. Robust failure recovery is essential for production reliability. Strategies must go beyond simple retries, incorporating understanding of system state to prevent data corruption and duplicate work. The goal is stateful recovery, not just restart.

| Pattern | Description | When to Use |
|---------|-------------|-------------|
| **Exponential Backoff with Jitter** | When retrying a failed tool or API call, use an exponential backoff strategy (increasing wait time between retries) and add a small random delay (jitter) to prevent multiple agents from retrying simultaneously. | For all network or API calls that can fail transiently. This prevents overwhelming a dependent service with coordinated retries. |
| **Tool Idempotency** | Design tools to be idempotent, meaning calling them multiple times with the same arguments has the same effect as calling them once. This is often done by passing a unique idempotency token. | For any tool that modifies state (e.g., making a payment, updating a database). This prevents action duplication caused by ambiguous retries after a timeout. [17] |
| **Stateful Recovery** | Ensure agents can recover their state after a failure. This involves using persistent storage to save agent state, allowing it to resume work from where it left off rather than starting from scratch. | For long-running or mission-critical Claude Code agents. Stateless recovery erases reasoning history, while stateful recovery allows the system to learn from failure. [17] |
| **Stopping Conditions** | Implement explicit stopping conditions, such as a maximum number of iterations or an overall time limit, to prevent agents from entering infinite loops or getting stuck in a failure cycle. | In all agentic systems, especially those with reasoning loops or multi-agent architectures where agents can pass tasks indefinitely between each other. [1] |
| **Git Rollback for Recovery** | In coding agents, instruct Claude Code to use `git` to revert bad changes. If a new feature implementation breaks the application, the agent can revert to the last known working commit. | For Claude Code agents that modify code. This provides a powerful recovery mechanism, allowing the agent to autonomously recover from broken code states. [18] |

---

### 7. Human-in-the-Loop Patterns

Even the most advanced Claude Code agents benefit from human oversight. Human-in-the-loop (HITL) patterns provide mechanisms for humans to intervene, provide feedback, or take control at critical points. The goal is not to micromanage but to provide strategic guidance and ensure alignment.

| Pattern | Description | When to Use |
|---------|-------------|-------------|
| **Confirmation Checkpoints** | The agent pauses at critical decision points (e.g., before executing a destructive action or making a purchase) and requests human confirmation before proceeding. | For high-risk or irreversible actions. This provides an essential safety barrier, ensuring the agent doesn't make costly decisions autonomously. [1] |
| **Human Input for Unblocking** | When an agent encounters a blocker it cannot resolve (e.g., a CAPTCHA, an ambiguous decision), it escalates to a human for the necessary input or decision. | When the agent encounters problems outside its current capabilities. This prevents the agent from getting stuck and allows it to continue progressing with human help. |
| **Human Review and Editing** | The agent produces a draft (e.g., a code snippet, a document), and a human reviews, edits, and approves it. The agent can then incorporate the feedback. | For tasks where quality, tone, or accuracy are subjective and benefit from human judgment. Common in content creation and coding workflows. |
| **Learning from Feedback** | The system logs human corrections and feedback and uses them to refine the agent's system prompts or adjust its behavior over time. | In systems designed to continuously improve. This creates a virtuous feedback loop where human intervention makes the agent progressively more autonomous. |

---

### 8. Testing and Validation

Testing Claude Code agent systems is challenging due to their non-deterministic nature. Effective validation requires a combination of unit tests for tools, integration tests for workflows, and evaluation-based end-to-end tests to measure performance against benchmarks.

| Pattern | Description | When to Use |
|---------|-------------|-------------|
| **Unit Testing for Tools** | Each individual tool should have robust unit tests that validate its functionality independently of the LLM. | Always. Unreliable tools lead to unpredictable agent behavior. Unit testing ensures your agent's building blocks are solid. |
| **Evaluation-Based End-to-End Testing** | Define a set of test cases (a "benchmark") representing real-world tasks. Run the agent against these test cases and measure success rates, output quality, and resource usage. | To validate overall Claude Code agent system performance. This provides a quantitative measure of how well the agent performs the tasks it was designed for. [1] |
| **Automated Regression Testing** | Whenever you change the agent's system prompt, tools, or orchestration, re-run your evaluation benchmark to ensure performance hasn't regressed. | In all development cycles. Seemingly small changes can have unexpected effects on agent behavior; automation is key to catching regressions early. |
| **Sandbox Environment Testing** | Run agents in a fully isolated sandbox environment that mirrors production but has no access to real-world systems. | For all testing, especially for Claude Code agents that can execute destructive actions or interact with external APIs. This prevents unintended consequences during development and testing. [1] |
| **Browser Testing for Web Agents** | For Claude Code agents that interact with the web, use browser automation tools (like Puppeteer) to test if features work end-to-end from a human user's perspective. | For any agent that needs to interact with web interfaces. This allows the agent to identify and fix bugs that are not apparent from code alone. [18] |

---

### 9. Observability and Debugging

Debugging Claude Code agents can be difficult because their behavior is emergent. Robust observability is fundamental. You need detailed logs that capture not just actions but also the agent's "thinking" at each step. End-to-end tracing is essential, especially in multi-agent systems.

| Pattern | Description | When to Use |
|---------|-------------|-------------|
| **Agent Tracing** | Log each step of a request's lifecycle, including the prompt, the agent's chain of thought, the selected tool, tool parameters, tool results, and the final response. | In all development and production environments. Detailed tracing is the most important tool for understanding why a Claude Code agent made a particular decision. [17] |
| **Execution Visualization** | Use tools that visualize an agent's execution tree, showing the flow of LLM calls, tool invocations, and handoffs between agents. | During debugging of complex workflows, especially in multi-agent systems. This makes it much easier to identify bottlenecks, unexpected loops, or communication failures. |
| **Performance Metrics** | Monitor key performance metrics such as end-to-end latency, task success rate, tool failure rate, and token usage. | In production, to understand the health and efficiency of your Claude Code agent system. Alert on anomalies in these metrics to detect problems proactively. [8] |
| **User Feedback Logging** | Capture explicit and implicit user feedback (e.g., thumbs up/down, manual edits to agent output) and correlate it with agent traces. | To identify systemic failure patterns and areas for improvement. User feedback is an invaluable source of ground truth for agent performance. |

---

### 10. Security and Protection

Autonomous Claude Code agents introduce new security vectors. Protection is a first-class consideration, not an afterthought. The goal is to limit the "blast radius" of an agent that misbehaves, whether through deception or error.

| Pattern | Description | When to Use |
|---------|-------------|-------------|
| **Principle of Least Privilege** | Grant a Claude Code agent only the minimum set of permissions and tool access it absolutely needs to perform its task. Use allowlists (`allowedTools`) instead of blocklists. | Always. This is the most fundamental principle of agent security. It drastically reduces the attack surface of a compromised or malfunctioning agent. [2] [9] |
| **Sandbox Execution** | Run agents in contained, isolated execution environments (e.g., Docker containers, VMs) with restricted filesystem and network access. | For any Claude Code agent that executes LLM-generated code or interacts with the filesystem. This prevents the agent from affecting the host system or other services. [9] |
| **Human Confirmation for Sensitive Actions** | Require explicit human approval for any actions that are costly, irreversible, or that modify critical data. | For Claude Code agents that can incur financial costs, delete data, or interact with production systems. Do not rely solely on LLM judgment for these actions. [9] |
| **Input and Output Validation** | Rigorously validate and sanitize all inputs from external sources and all outputs from the agent before using them. Be careful of prompt injection attacks. | For any Claude Code agent that interacts with user inputs or data from external sources. Treat all external data as untrusted. |
| **Abuse Monitoring** | Monitor tool usage and agent behavior patterns to detect anomalies that might indicate abuse or compromise (e.g., a sudden spike in API calls or costs). | In all production systems. Rapid anomaly detection can help mitigate damage from a rogue agent. |

---

### 11. Production Deployment

Deploying Claude Code agents to production requires a focus on reliability, scalability, and cost optimization. Strategies must address the non-deterministic nature of LLMs and plan for failure.

| Pattern | Description | When to Use |
|---------|-------------|-------------|
| **Start Simple, Add Complexity Gradually** | Start with the simplest architecture that can work (usually a deterministic chain or single agent). Only move to more complex multi-agent architectures when benefits clearly outweigh debugging and maintenance costs. | As a general development philosophy. Resist the temptation to build a complex multi-agent system from the start. [20] |
| **Prompt and Response Caching** | Cache responses for identical prompts and tool calls to reduce latency and costs. The Claude Agent SDK includes automatic prompt caching. | In production environments with repetitive traffic. Caching can dramatically improve perceived performance and reduce API bills. |
| **Response Streaming** | Stream agent output (especially chain-of-thought reasoning) back to the user in real-time instead of waiting for the final response. | To improve perceived latency in interactive applications. Seeing the agent "think" makes the wait less frustrating for users. |
| **Model Right-Sizing** | Use smaller, faster Claude models (like **Haiku**) for simpler tasks (e.g., classification, data extraction) and reserve larger, more capable models (like **Opus**) for complex reasoning tasks. | In multi-agent systems, where you can assign different Claude models to different agents based on their task requirements. |
| **Infrastructure as Code (IaC)** | Define and manage your entire Claude Code agent environment (prompts, tools, configurations, infrastructure) using IaC tools like Terraform or CloudFormation. | For production deployments to ensure repeatability, version control, and disaster recovery. |

---

## Implementation Checklist

### Beginner Level

- [ ] System prompt follows Role-Task-Output Format pattern
- [ ] Tools have clear descriptions and simple arguments
- [ ] Basic error handling with retries implemented
- [ ] Agent runs in a sandbox environment for testing
- [ ] Basic logging of agent actions in place

### Intermediate Level

- [ ] `CLAUDE.md` file provides project context for Claude Code
- [ ] Tools are atomic and composable
- [ ] Stopping conditions prevent infinite loops
- [ ] Human confirmation required for sensitive actions
- [ ] Unit tests exist for all tools
- [ ] Agent traces are logged for debugging
- [ ] Using `allowedTools` to restrict tool access

### Advanced Level

- [ ] Agent Skills with `SKILL.md` for progressive disclosure
- [ ] Multi-agent orchestration with appropriate pattern
- [ ] Stateful recovery using `claude-progress.txt` and similar files
- [ ] Git history serves as long-term memory with descriptive commits
- [ ] Evaluation benchmarks measure agent performance
- [ ] Performance metrics monitored in production
- [ ] MCP integration for external services

### Expert Level

- [ ] Initializer agent sets up long-running sessions with `feature_list.json`
- [ ] Idempotent tools prevent duplicate actions
- [ ] Comprehensive observability with execution visualization
- [ ] Automated regression testing on prompt changes
- [ ] Model right-sizing with Haiku for simple tasks, Opus for complex reasoning
- [ ] Claude Agent SDK automatic context compaction tuned for workload
- [ ] Infrastructure as Code for Claude Code agent deployment

---

## Common Pitfalls and Antipatterns

### Prompt Design Pitfalls

- **Vague role definitions** that don't specify agent behavior clearly
- **Missing output format** leading to inconsistent responses
- **Overloading context** with irrelevant information instead of using progressive disclosure
- **No examples** for complex output formats
- **Not using `CLAUDE.md`** to provide project-specific context

### Tool Design Pitfalls

- **Monolithic tools** that try to do too much instead of atomic, composable primitives
- **Poor documentation** that leaves Claude Code guessing about tool purpose
- **Missing error messages** that don't help the agent recover
- **Overly broad permissions** using blocklists instead of `allowedTools`
- **Not leveraging Claude Agent SDK** pre-built tools before creating custom ones

### Orchestration Pitfalls

- **Premature complexity** choosing multi-agent when single agent suffices
- **No stopping conditions** allowing infinite loops
- **Missing evaluator/judge** for quality control in multi-step workflows
- **Tight coupling** between agents preventing independent evolution

### Memory Pitfalls

- **Relying solely on context window** for long tasks without state files
- **No state persistence** between sessions (missing `claude-progress.txt`, etc.)
- **Aggressive summarization** that loses critical details
- **No checkpointing** making recovery impossible
- **Not using Git as memory** for coding agents, preventing rollback

### Security Pitfalls

- **Running agents with full permissions** instead of least privilege with `allowedTools`
- **No sandbox** for code execution
- **Trusting external inputs** without validation (prompt injection risk)
- **No human oversight** for critical actions

---

## References

1. Anthropic. (2024, December 19). *Building Effective AI Agents*. https://www.anthropic.com/research/building-effective-agents
2. Anthropic. (n.d.). *Agent SDK overview*. Claude Docs. https://platform.claude.com/docs/en/agent-sdk/overview
3. Anthropic. (2025, April 18). *Claude Code: Best practices for agentic coding*. https://www.anthropic.com/engineering/claude-code-best-practices
4. Anthropic. (n.d.). *Multi-agent research system*. https://www.anthropic.com/engineering/multi-agent-research-system
5. LangChain. (n.d.). *How to build an agent*. https://blog.langchain.com/how-to-build-an-agent/
6. LlamaIndex. (n.d.). *Multi-agent patterns*. https://developers.llamaindex.ai/python/framework/understanding/agent/multi_agent/
7. Anthropic. (2025, September 11). *Writing effective tools for AI agents*. https://www.anthropic.com/engineering/writing-tools-for-agents
8. Microsoft Azure. (2025, July 15). *Agent Factory: Top 5 agent observability best practices for reliable AI*. https://azure.microsoft.com/en-us/blog/agent-factory-top-5-agent-observability-best-practices-for-reliable-ai/
9. Microsoft Azure. (2025, August 22). *Agent Factory: Creating a blueprint for safe and secure AI agents*. https://azure.microsoft.com/en-us/blog/agent-factory-creating-a-blueprint-for-safe-and-secure-ai-agents/
10. Anthropic. (2025, September 29). *Effective context engineering for AI agents*. https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
11. Model Context Protocol. (n.d.). *Best Practices*. https://modelcontextprotocol.info/docs/best-practices/
12. Vellum. (2025, October 3). *Agentic Workflows: Emerging Architectures and Design Patterns*. https://www.vellum.ai/blog/agentic-workflows-emerging-architectures-and-design-patterns
13. Anthropic. (2025, November 12). *Building Agents with the Claude Agent SDK*. https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk
14. Microsoft Azure. (2025, July 18). *AI Agent Orchestration Patterns*. https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns
15. Claude Code Docs. (n.d.). *Claude Code overview*. https://code.claude.com/docs/en/overview
16. OpenAI. (n.d.). *A practical guide to building agents*. https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf
17. Maxim. (2025, October 9). *Multi-Agent System Reliability: Failure Patterns, Root Causes, and Production Validation Strategies*. https://www.getmaxim.ai/articles/multi-agent-system-reliability-failure-patterns-root-causes-and-production-validation-strategies/
18. Anthropic. (2025, November 26). *Effective harnesses for long-running agents*. https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents
19. Anthropic. (2025, October 16). *Equipping agents for the real world with Agent Skills*. https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills
20. Databricks. (2025, March 10). *Agent system design patterns*. https://docs.databricks.com/aws/en/generative-ai/guide/agent-system-design-patterns

