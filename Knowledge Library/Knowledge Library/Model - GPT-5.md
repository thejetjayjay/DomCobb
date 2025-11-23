---
title: "Prompt Engineering Best Practices for GPT-5"
author: "Manus AI"
date: "2025-11-20"
---

# Prompt Engineering Best Practices for GPT-5

## 1. Introduction

OpenAI's GPT-5 represents a significant leap in large language model capabilities, moving beyond incremental improvements to introduce new architectural paradigms and a strong focus on real-world applicability and reliability [1]. This document provides a comprehensive guide to prompt engineering best practices for GPT-5, synthesizing insights from official documentation, academic research, and extensive technical analysis. Unlike its predecessors, GPT-5 is not a single model but a family of models, including `gpt-5-nano`, `gpt-5-mini`, and `gpt-5-thinking`, which are automatically routed by the Azure model-router to optimize for speed and reasoning based on task complexity [2]. This shift requires a more nuanced approach to prompt design, emphasizing clarity, control over reasoning, and effective tool integration.

Key innovations include the **Responses API**, which improves performance by persisting chain-of-thought across turns, and the **`reasoning_effort`** parameter, which gives developers granular control over the model's cognitive load. GPT-5 also demonstrates state-of-the-art performance across a wide range of benchmarks, achieving a perfect 100% on the AIME 2025 math competition and 74.9% on the SWE-bench Verified coding challenge [3]. This guide will explore the core concepts, best practices, and practical applications necessary to harness the full potential of this powerful new model series.

## 2. Core Concepts

Effective prompt engineering for GPT-5 requires understanding its fundamental architectural shifts. These concepts are crucial for designing prompts that are efficient, reliable, and capable of leveraging the model's advanced features.

### Responses API

The **Responses API** is a new stateful API that supersedes the traditional Chat Completions API. It is designed to persist the model's chain of thought across multiple turns, leading to significant performance improvements. By maintaining context and reasoning state, the API avoids redundant computation and allows the model to build upon its previous thoughts. Official benchmarks show this new API provides a tangible boost, improving performance on the Tau-Bench Retail benchmark from 73.9% to 78.2% (+4.3 points) [4]. This stateful nature makes it ideal for complex, multi-step agentic workflows where context and continuity are paramount.

### Reasoning Effort

Perhaps the most impactful new feature for prompt engineers is the `reasoning_effort` parameter. This setting allows developers to explicitly control how much the model "thinks" before responding. It accepts four levels: `minimal`, `low`, `medium`, and `high`. Lowering the effort reduces latency and cost for simple tasks, while increasing it allows the model to tackle more complex problems that require deep reasoning. The impact is dramatic; on the Aider Polyglot benchmark, enabling "thinking" (higher reasoning effort) boosted GPT-5's score by an astounding 61.3 points [3]. The official troubleshooting guide recommends using `minimal` or `low` for routine work and reserving `high` for genuinely complex problems to avoid "overthinking" on simple queries [5].

### Instruction Hierarchy

To combat prompt injection and increase reliability, GPT-5 introduces a three-level instruction hierarchy. Instructions are processed in order of precedence, ensuring that foundational directives are not easily overridden by user input [2].

1.  **System Messages:** Highest precedence. Set by the organization to enforce top-level rules and safety policies.
2.  **Developer Messages:** Middle precedence. Set by the application developer to define the model's persona, task, and constraints.
3.  **User Messages:** Lowest precedence. The input provided by the end-user.

This structure provides a robust defense against common attack vectors and ensures the model adheres to its core instructions, making it more suitable for enterprise applications where safety and control are critical.

### Safe Completions

GPT-5 moves away from the binary "hard refusal" safety model of its predecessors. Instead, it uses **safe completions**, a paradigm that seeks to maximize helpfulness while still adhering to safety policies. Rather than refusing a potentially sensitive prompt outright, the model will provide a helpful response to the safe portion of the request. For example, when asked for instructions to exploit a system for a security drill, GPT-5 will provide the high-level steps and explain how to defend against them, but will refuse to provide the exact, harmful command sequences [2]. This approach makes the model more useful in sensitive domains like cybersecurity and research without compromising safety.

## 3. Best Practices

### Prompting Inversion: Less is More

Research has identified a phenomenon known as **"Prompting Inversion,"** where complex, highly-engineered prompts that improve performance on models like GPT-4 can actually *decrease* performance on GPT-5 [6]. The study found that overly prescriptive prompts with excessive examples, constraints, and formatting rules can act as "handcuffs," limiting the model's more advanced native reasoning capabilities. For GPT-5, simpler, more direct prompts often yield better results.

> **Recommendation:** Start with clear, direct, and concise prompts. Avoid the elaborate, multi-part prompts that were common for GPT-4. Trust the model's improved instruction-following and reasoning abilities. If performance is suboptimal, gradually add constraints rather than starting with them.

### Master Reasoning Effort and Verbosity

Controlling the new API parameters is essential for optimizing performance and cost.

-   **`reasoning_effort`**: As detailed in the OpenAI Troubleshooting Guide, right-sizing this parameter is the first step to fixing many common issues. Use `minimal` for simple classification or extraction, `low` for standard Q&A, and `medium` or `high` for complex agentic tasks. This prevents both "overthinking" on simple queries and "laziness" on hard ones [5].
-   **`verbosity`**: This parameter controls the length of the model's output. If you need concise answers, explicitly set it to `low`. For tasks like code generation where detailed explanations are useful, a higher verbosity may be appropriate [5].

### System vs. User Prompt Optimization

Understanding the distinction between system and user prompts is crucial for optimizing GPT-5's performance and cost.

-   **System Prompt:** This is where the agent's identity, high-level constraints, and fundamental rules should be defined. Due to GPT-5's KV-cache optimization, the system prompt should remain as stable as possible. Avoid dynamic information like timestamps, which can invalidate the cache and significantly increase costs and latency. Think of the system prompt as the "constitution" of your agent [4].
-   **User Prompt:** Should contain the immediate task or user question. This is the dynamic part of the interaction. For agentic tasks, the user prompt can be a high-level goal, and the model will use its tools and reasoning to break it down into steps.

### Role Framing

Assigning a role to GPT-5 is more crucial than ever. However, instead of a simple "You are a helpful assistant," roles for GPT-5 should resemble job descriptions for an autonomous agent.

**Role Framing Example for a Research Agent:**
```
You are an Autonomous Research Agent. Your objective is to answer complex questions comprehensively and accurately. You must use your search tools to collect information from multiple sources, synthesize the results, and present a well-structured final report. You operate autonomously, deciding the best steps to achieve the objective, and should only ask the user for clarification if you are completely blocked.
```

### Controlling Eagerness and Task Scope

One of the most important innovations in GPT-5 is the ability to control agentic behavior through prompts.

-   **Prompting for Less Eagerness:** To limit exploration and reduce latency, instruct the model to be more direct. This is useful in scenarios where efficiency is more important than depth.
    > **Prompt:** "Solve the task with the minimum number of tool calls possible. Prioritize action over extensive research. If you have 70% confidence in the answer, proceed."

-   **Prompting for More Eagerness:** To encourage autonomy and persistence, especially in research or debugging tasks, use a prompt that incentivizes the model to continue until complete resolution.
    > **Persistence Prompt:** "You are a persistent agent. Keep working until the user's query is completely resolved. Never stop or return to the user when uncertain; instead, research or deduce the most reasonable next step. Decide what to do and act."

### Scope Delimitation and Definition of Done

For agentic workflows, it's fundamental to clearly define what "completed" means.

-   **Delimiters:** Use tags like `<context_gathering>`, `<planning>`, `<execution>` to structure the model's thought process. Within these tags, define the rules.
-   **Early Stop Criteria:** Give the model explicit conditions to stop searching or executing. This saves costs and time.
    > **Example:** "Early stop criteria for context gathering: (1) You can name the exact content to be changed. (2) Main search results converge (~70%) in a single area/path. (3) You have collected information from at least 3 primary sources."
-   **Definition of Done (DoD):** Explicitly define what constitutes a completed task. This is especially important for code generation or report creation tasks.
    > **Example:** "The task is complete when the React code is generated, all unit tests pass, and the documentation for the component is written in Markdown."

### Groundedness and Combating Hallucinations

To combat hallucinations, especially in synthesis tasks, instruct the model to base each statement on explicit sources collected during the research phase and cite them.

**Groundedness Prompt Example:**
```
For each statement in the final report, you must include a citation in the format [source_id] that corresponds to the source from which the information was extracted. Do not include any information that cannot be traced to a specific source.
```

### Encourage Self-Correction and Persistence

For complex tasks, prompting the model to reflect on its own work can significantly improve quality. This is particularly effective for coding and other structured generation tasks.

**Self-Correction Prompt Example:**
```
<self_reflection>
Internally score the draft against a 5–7 item rubric you devise (clarity, correctness, edge cases, completeness, latency). If any category falls short, iterate once before replying.
</self_reflection>
```

For agentic workflows, it's crucial to prevent the model from being overly deferential. Use a persistence prompt to instruct the agent to continue working until the task is fully resolved, only asking for clarification when absolutely necessary.

**Persistence Prompt Example:**
```
<persistence>
- You are an agent - please keep going until the user's query is completely resolved.
- Never stop or hand back when you encounter uncertainty — research or deduce the most reasonable approach and continue.
- Do not ask the human to confirm or clarify assumptions; decide the most reasonable assumption, proceed with it, and document it for the user's reference.
</persistence>
```

### Metaprompting: Creating Prompts that Generate Prompts

With GPT-5, metaprompting evolves from a refinement technique into a tool for creating agents. Instead of asking the model to simply "improve a prompt," you can ask it to "design the complete system prompt for an agent with a specific objective."

**Metaprompt Example for Creating a Debugging Agent:**
```
You are an AI Agent Architect. Your task is to design the system prompt for a software debugging agent called 'CodeHealer'.

**Agent Objective:** Identify and fix bugs in Python code snippets.

**Available Tools:**
- `execute_code(code: str)`: Executes a code snippet and returns the output or error.
- `read_file(file_path: str)`: Reads the contents of a file.
- `write_file(file_path: str, content: str)`: Writes content to a file.
- `search_web(query: str)`: Searches the web for documentation or error solutions.

**The system prompt you create must include:**
1. A clear and concise role for the 'CodeHealer' agent.
2. A step-by-step reasoning process the agent should follow (e.g., 1. Understand the error, 2. Formulate hypotheses, 3. Test hypotheses with tools, 4. Propose a fix, 5. Verify the fix).
3. Rules of engagement, including when to persist in solving and when to ask the user for more information.
4. Instructions on how to use tools effectively and safely (e.g., do not execute unknown code without analysis).
5. A guide on the final response format, which should include the corrected code and an explanation of the fix.

Generate the complete system prompt for the 'CodeHealer' agent in a Markdown code block.
```

## 4. Tooling & Integration

GPT-5's agentic capabilities are heavily reliant on its enhanced tool-calling features. The model supports two primary types of tools: **Function Tools** (structured, via JSON Schema) and **Custom Tools** (free-form, with optional Lark/CFG constraints) [7].

### Tool Preambles

Official documentation highlights the use of **Tool Preambles**, which are short, descriptive messages that explain what the model is about to do before making a tool call. These preambles have been shown to boost tool-calling accuracy and reduce perceived latency by keeping the user informed of the model's progress [4].

**Status Update Prompt Example:**
```
<status_update_spec>
Definition: A brief progress note: what just happened, what’s next, any real blockers, written in a continuous conversational style, narrating the story of your progress as you go. Always start with a brief acknowledgement of the task before getting started.
</status_update_spec>
```

### Parallel Tool Calls

To minimize latency, GPT-5 can be instructed to execute independent tool calls in parallel. The model does not do this by default and must be explicitly told when and how to parallelize operations. This is most effective for read-only operations, such as fetching multiple files or running static analysis.

**Parallelization Prompt Example:**
```
<parallelization_spec>
Definition: Run independent or read-only tool actions in parallel (same turn/batch) to reduce latency.
When to parallelize:
 - Reading multiple files/configs/logs that don't affect each other.
 - Static analysis, searches, or metadata queries with no side effects.
 - Separate edits to unrelated files/features that won't conflict.
</parallelization_spec>
```

### JSON Schema and Tool Definitions

GPT-5 has a much more robust understanding of JSON schemas. The best practice is to provide a complete JSON schema in your tool definition or prompt.

**Example Tool Definition with Schema:**
```json
{
  "name": "create_user_profile",
  "description": "Creates a new user profile in the system.",
  "parameters": {
    "type": "object",
    "properties": {
      "full_name": {
        "type": "string",
        "description": "The user's full name."
      },
      "email": {
        "type": "string",
        "description": "The user's email address, must be unique."
      },
      "age": {
        "type": "integer",
        "description": "The user's age."
      },
      "is_premium_member": {
        "type": "boolean",
        "description": "Indicates whether the user is a premium member.",
        "default": false
      }
    },
    "required": ["full_name", "email", "age"]
  }
}
```

### Validation and Retry Logic for Invalid JSON

Even with schemas, the model may occasionally generate malformed JSON. Your code should anticipate this. A good practice is to create a retry loop that captures the error, informs the model about the specific error, and requests a correction.

**Retry Prompt Example:**
```
The previous function call failed with the following JSON validation error: `[insert validation error here]`.

The generated output was:
```json
[insert invalid JSON here]
```

Please correct the JSON to match the tool schema and retry the function call. Pay special attention to data types and required fields.
```

### Guardrails and Safety Checks

For autonomous agents, having guardrails to prevent undesired actions is vital.

-   **Constraining Tool Selection:** You can use prefix completion to force the model to use a specific tool or a subset of tools. For example, in a "research" state, you can restrict the model to only use tools that start with `search_`.
-   **Confirmation Steps:** For destructive actions (such as `delete_file` or `execute_payment`), the prompt should instruct the model to always ask for user confirmation before executing the action. This can be implemented as a tool `ask_user_confirmation(question: str)`.
    > **Guardrail Prompt:** "Before using any tool marked as 'destructive' (e.g., `delete_file`), you must first call the `ask_user_confirmation` function with a clear question about the action you are about to take. Do not proceed without explicit user confirmation."

## 5. Performance & Benchmarks

GPT-5 has demonstrated state-of-the-art performance across a wide range of academic and industry benchmarks. Its capabilities in reasoning, coding, and reliability mark a significant advancement over previous models.

| Benchmark                 | Model Configuration            | Score   | Key Insight                                        |
| ------------------------- | ------------------------------ | ------- | -------------------------------------------------- |
| **AIME 2025** (Math)      | GPT-5 Pro (with Python tools)  | 100%    | First model to achieve a perfect score [3].        |
| **GPQA Diamond** (Reasoning) | GPT-5 Pro (with Python tools)  | 89.4%   | Outperforms all previous models on PhD-level science questions [3]. |
| **SWE-bench Verified** (Code) | GPT-5 (with thinking)          | 74.9%   | Surpasses specialized coding models [3].           |
| **Aider Polyglot** (Code) | GPT-5 (with thinking)          | 88.0%   | Massive +61.3 point boost from reasoning [3].      |
| **HealthBench** (Reliability) | GPT-5 (with thinking)          | 1.6%    | Extremely low error rate on hard medical cases [3]. |

Furthermore, GPT-5 has a measured hallucination rate of **under 1%** on open-source prompts and is **3-8 times better** at avoiding sycophancy and deception than its predecessors [2] [3]. These reliability improvements are a direct result of new training methodologies focused on real-world enterprise use cases.

## 6. Use Cases & Applications

The new features and enhanced reliability of GPT-5 unlock a range of production-grade applications that were previously challenging.

### Where GPT-5 Shines vs. Where to Avoid

| Where it Shines (Ideal Use Cases) | Where to Avoid (Limitations and Pitfalls) |
| :--- | :--- |
| **Complex Agentic Applications:** Orchestrating multiple tools, executing long-duration tasks, and workflows requiring planning and adaptation. | **Simple Low-Latency Tasks (with high `reasoning_effort`):** Using full reasoning capacity for trivial tasks wastes resources and increases latency. Adjust `reasoning_effort` downward. |
| **Code Generation and Refactoring:** With `GPT-5-Codex`, the model demonstrates enhanced capability for software engineering tasks such as feature creation, testing, debugging, and code reviews. | **Environments without Tool Access:** GPT-5's true power is unlocked when it can interact with the external world. Using it in a vacuum severely limits its agentic potential. |
| **Long-Context Analysis and Synthesis:** The expanded context window, combined with improved reasoning, makes it ideal for analyzing extensive documents, code repositories, or long transcripts. | **Content Generation without Verification:** Despite improvements, the model can still hallucinate. For topics requiring high factual accuracy, human verification or the use of RAG (Retrieval-Augmented Generation) remains essential. |
| **Rapid Application Prototyping:** The ability to generate code and interact with APIs enables rapid creation of functional prototypes directly from a natural language description. | **High-Risk Decisions without Supervision:** The model's autonomy does not replace human judgment. Critical decisions (financial, medical, etc.) should always have a human in the loop for final approval. |

-   **Mining Optimization:** In one documented case, GPT-5 analyzed vast amounts of data from a mining site, identified key bottlenecks in the grinding mill and truck routes, and proposed solutions that led to millions of dollars in savings. The total cost of this complex analysis was just **$0.06 USD** [2].
-   **Aviation Safety:** When tasked with generating a safety report for an aircraft with a known engine flaw, GPT-5 refused to write a deceptive report stating the plane was safe, even when pressured by a simulated CEO. This demonstrates its improved reliability in safety-critical domains [2].
-   **Agentic RAG:** By combining the managed RAG services in the OpenAI API with GPT-5's tool-calling abilities, developers can build powerful agentic research systems. The model can autonomously rewrite user queries, search vector stores, and decide when it has enough information to answer, creating a much more dynamic and effective RAG pipeline [8].
-   **Enterprise Chatbots:** The combination of the model-router, which selects the most efficient model for a given query, and the instruction hierarchy, which protects against prompt injection, makes GPT-5 an ideal foundation for secure and cost-effective enterprise customer service agents [2].

## 7. Practical Examples: Before and After

The following practical examples demonstrate prompt engineering best practices for GPT-5 with "before and after" comparisons for different task types.

### Example 1: Multi-Document Synthesis

-   **Before (Generic Prompt):**
    > "Summarize these articles about the climate crisis."
    > *(This prompt is vague, does not define the output format or target audience.)*

-   **After (Optimized for GPT-5):**
    > **System Prompt:**
    > "You are a Research Analyst specialized in environmental policy. Your task is to synthesize information from multiple sources into a clear and objective executive summary for legislators.
    > 
    > **Process:**
    > 1.  Read all documents provided in the context.
    > 2.  Identify the 3-5 main points of agreement and disagreement among authors.
    > 3.  Structure the output in Markdown format.
    > 4.  For each key point, cite the sources from which the information was extracted using the format `[source_id]`.
    > 5.  The final synthesis should not exceed 500 words."
    > 
    > **User Prompt:**
    > "Synthesize the following articles about the climate crisis: `[Article 1]`, `[Article 2]`, `[Article 3]`."
    > *(**Why it works:** Defines a role, a clear process, an output format, the need for groundedness, and size constraints, guiding the model toward a high-quality and relevant response.)*

### Example 2: Code Generation with Tests

-   **Before (Simple Prompt):**
    > "Write a Python function to check if an email is valid."
    > *(This prompt will likely generate a simple function using regex, but without tests or implementation context.)*

-   **After (Optimized for GPT-5):**
    > **System Prompt:**
    > "You are a Senior Software Engineer who follows Test-Driven Development (TDD) principles. When receiving a code request, your process is:
    > 1.  First, write a unit test suite using the `pytest` library covering success cases, edge cases, and failure cases.
    > 2.  Then, write the Python code that makes all tests pass.
    > 3.  Finally, provide the function code and test code in separate, named code blocks."
    > 
    > **User Prompt:**
    > "I need a Python function called `is_valid_email` that validates an email address. Consider emails with subdomains and newer domain extensions."
    > *(**Why it works:** The prompt defines a role and a professional workflow (TDD), forcing the model to think about test cases before writing code, resulting in a more robust and reliable solution.)*

### Example 3: Data Analysis with Tools

-   **Before (Ambiguous Prompt):**
    > "Analyze this sales data."
    > *(This prompt does not specify the analysis objective or tools to use.)*

-   **After (Optimized for GPT-5):**
    > **System Prompt:**
    > "You are a Data Analyst. Your task is to analyze datasets to extract actionable insights. You have access to the `data_analyzer` tool, which has the following functions:
    > - `data_analyzer.load_data(file_path: str)`
    > - `data_analyzer.get_summary_statistics()`
    > - `data_analyzer.find_correlations()`
    > - `data_analyzer.plot_trends(column: str)`
    > 
    > **Analysis Process:**
    > 1.  Load the data.
    > 2.  Generate descriptive statistics to understand the distribution.
    > 3.  Identify the strongest correlations between variables.
    > 4.  Plot sales trends over time.
    > 5.  Answer the user's question based on insights found, including generated charts."
    > 
    > **User Prompt:**
    > "Analyze the file `sales_data_2025.csv` and tell me which product had the highest sales growth in the last year."
    > *(**Why it works:** Clearly defines available tools and a structured analysis process, enabling the GPT-5 agent to execute a data analysis workflow autonomously and effectively.)*

### Example 4: Long-Form Writing (Article Creation)

-   **Before (Short Prompt):**
    > "Write an article about the benefits of artificial intelligence."
    > *(Generates a generic article without structure or depth.)*

-   **After (Optimized for GPT-5):**
    > **System Prompt:**
    > "You are a Technical Content Writer specialized in AI. Your task is to write detailed and well-structured articles.
    > 
    > **Article Structure:**
    > 1.  **Introduction:** Present the theme and main thesis.
    > 2.  **Section 1: Efficiency and Automation:** Detail how AI is optimizing processes.
    > 3.  **Section 2: Innovation and Discovery:** Explore how AI is accelerating research and development.
    > 4.  **Section 3: Challenges and Ethical Considerations:** Discuss risks and mitigations.
    > 5.  **Conclusion:** Recap main points and offer a vision for the future.
    > 
    > The article should be between 1,500 and 2,000 words and written in a professional but accessible tone."
    > 
    > **User Prompt:**
    > "Write a complete article about the benefits of artificial intelligence for small and medium businesses."
    > *(**Why it works:** Provides a detailed structure (a "scaffold") for the article, ensuring the result is comprehensive, well-organized, and meets depth and size requirements.)*

### Example 5: Project Planning

-   **Before (Vague Prompt):**
    > "Plan the launch of a new application."
    > *(There are not enough details to create an actionable plan.)*

-   **After (Optimized for GPT-5):**
    > **System Prompt:**
    > "You are a Senior Project Manager specialized in software launches. Your task is to create a detailed project plan using the Markdown table format.
    > 
    > **Table Columns:**
    > - `Phase`: (e.g., Pre-Launch, Launch, Post-Launch)
    > - `Activity`: The specific task to be performed.
    > - `Responsible`: The team or person responsible (e.g., Marketing, Engineering).
    > - `Estimated Duration (days)`: The estimated duration for the activity.
    > - `Dependencies`: Which activities must be completed before this one.
    > 
    > The plan should cover from the final testing phase to the first two weeks after launch."
    > 
    > **User Prompt:**
    > "Create a project plan for the launch of our new productivity application, 'TaskMaster Pro'. The launch is scheduled for 60 days from now."
    > *(**Why it works:** Requires a highly structured output (Markdown table) and defines the exact columns, forcing GPT-5 to think organized and generate a clear, detailed, and immediately usable project plan.)*

## 8. Evaluation & Metrics

Evaluating an agentic system like GPT-5 goes beyond simply checking if the answer is correct. It's necessary to measure the efficiency and robustness of the process.

### Groundedness

For knowledge-based tasks, the most important metric is groundedness. Is the response fully supported by the sources provided or retrieved? This is crucial for mitigating hallucinations. Evaluation can be done by comparing each statement in the response with the original source text.

### Factuality and Completeness

Is the response factually correct and does it address all aspects of the user's question? For this, "gold standard" datasets or the LLM-as-Judge technique can be used, where another LLM (or GPT-5 itself with an evaluation prompt) judges the quality of the response based on a rubric.

### Process Efficiency

In an agentic system, efficiency is key. Metrics to track:

-   **Number of Tool Calls:** How many tools were needed to reach the answer? Less is generally better, as long as quality is not compromised.
-   **Total Latency:** How long did it take from the user's question to the final answer?
-   **Total Cost:** What was the cost in tokens (input and output) to complete the task?

### Sanity Tests

Before a full evaluation, run some sanity tests:

-   **Contradiction Prompt Test:** Give the agent an instruction and then a contradictory one. It should identify the conflict.
-   **Invalid Tool Test:** Ask the agent to use a tool that doesn't exist. It should handle the error gracefully.
-   **Context Limit Test:** Send a prompt that exceeds the context limit to see how the agent reacts.

### Scoring Rubrics (LLM-as-Judge)

To scale evaluation, use an LLM as a judge with a clear rubric.

| Criterion | Score (1-5) | Description |
| :--- | :--- | :--- |
| **Response Relevance** | 1-5 | Does the response directly address the user's question? |
| **Source Grounding** | 1-5 | Are all statements supported by the cited sources? |
| **Clarity and Formatting** | 1-5 | Is the response well-written, structured, and easy to understand? |
| **Agent Efficiency** | 1-5 | Did the agent use the most direct path and fewest tools to reach the solution? |

## 9. Challenges & Limitations

Despite its advancements, GPT-5 has known failure modes that developers must mitigate.

-   **Overthinking:** Using a high `reasoning_effort` on a simple task can cause the model to delay its response and produce an overly verbose and circuitous thought process. **Solution:** Use the model-router or a classification layer to route simple tasks to lower reasoning effort settings [5].
-   **Laziness/Underthinking:** The opposite problem, where the model fails to reason deeply enough on a complex task. **Solution:** Increase `reasoning_effort` and use self-reflection prompts to encourage iteration [5].
-   **Malformed Tool Calls:** Errors can still occur if tool schemas are ambiguous. **Solution:** Provide clear descriptions, examples, and use strict JSON schema validation for all tool definitions [7].
-   **Cost Management:** While cost-effective, the `high` reasoning effort setting consumes more tokens. **Solution:** Track token usage carefully and implement caching for prompt, reasoning, and tool call results to minimize redundant API calls [5].

### Anti-Patterns to Avoid

| Anti-Pattern | Problem Description | Alternative / Solution |
| :--- | :--- | :--- |
| **"One-Shot" Prompts for Complex Tasks** | Trying to solve a complex agentic task with a single huge, detailed prompt. This is fragile and difficult to debug. | **Divide and Conquer:** Break the task into logical phases. Use a system prompt to define the overall objective, then use smaller user prompts to guide the agent through each phase of the plan. |
| **Including Dynamic Data in System Prompt** | Putting information that changes with each execution (like current date/time or user ID) in the system prompt. This invalidates the KV-cache, drastically increasing latency and costs. | **Keep System Prompt Stable:** The system prompt should be like a "class" of an object (stable and defining behavior), while the user prompt is the "instance" (with dynamic data). Pass variable data in the user prompt. |
| **Micro-managing the Agent** | Telling the agent exactly which tool to use and with what parameters at each step. This negates GPT-5's reasoning power. | **Define Goals, Not Steps:** Instead of saying "Call the search API with query 'X'", say "Search for the latest news about 'X' and give me a summary". Let the agent decide the best way to use its tools to achieve the goal. |
| **Lack of Guardrails for Destructive Actions** | Allowing the agent to call tools that modify state (writing files, deleting data, making payments) without a confirmation mechanism. | **Implement a Confirmation Step:** Create a tool `ask_user_confirmation(question: str)` and instruct the model in the system prompt to always call this tool before executing any destructive action, clearly explaining what it intends to do. |
| **Assuming the Model "Remembers"** | In long interactions, assuming the model will remember a detail mentioned many steps ago. Context is finite and attention can degrade. | **Summarize and Reaffirm Context:** For long-duration tasks, instruct the agent to periodically summarize the current state and key learnings. Include this summary in the context of subsequent steps to keep the model "focused". |
| **Ignoring Reasoning Cost** | Using maximum `reasoning_effort` for all tasks, even simple ones, resulting in unnecessary costs and latency. | **Calibrate `reasoning_effort`:** Use `reasoning_effort` as a control. For simple extraction or formatting tasks, use a low value. For complex planning and autonomy, use a higher value. Create configuration profiles for different task types. |

## 10. Implementation Checklist

Before deploying a GPT-5-powered application, use this checklist to ensure you've followed best practices:

-   [ ] **Stable System Prompt:** Is your system prompt stable and free of dynamic data (like timestamps) to optimize KV-cache?
-   [ ] **Role Definition (Role Framing):** Is the agent's role clearly defined with an objective, a process, and available tools?
-   [ ] **Reasoning Calibration:** Are you using the `reasoning_effort` parameter appropriately for task complexity (low for simple tasks, high for complex exploration)?
-   [ ] **Definition of "Completed" (DoD):** Are the task scope and "Definition of Done" (DoD) explicitly defined in the prompt to guide the agent to completion?
-   [ ] **Structure and Delimiters:** Are you using a clear structure (e.g., Markdown, XML tags) and delimiters to organize the prompt and expected output?
-   [ ] **Tool Schemas:** Do your tool definitions (`tools` or `functions`) include clear and well-defined JSON schemas?
-   [ ] **Retry Loop:** Does your application code have a retry loop to handle invalid JSONs or tool call failures, informing the model about the error?
-   [ ] **Safety Guardrails:** Are there guardrails and confirmation steps (e.g., using an `ask_user_confirmation` tool) for destructive or irreversible actions?
-   [ ] **Groundedness:** For knowledge-based tasks, are you instructing the model to cite sources for each statement, ensuring response grounding?
-   [ ] **Goals Instead of Micro-management:** Does the prompt define high-level goals and let the agent decide the best way to use its tools, instead of micro-managing each step?

## 11. Conclusion

GPT-5 is more than just a larger model; it is a fundamental shift in how developers can build reliable, agentic AI systems. The introduction of the **Responses API**, granular control over **reasoning effort**, and a robust **instruction hierarchy** provides the tools needed for production-grade applications. The key to success is to move away from the complex, over-engineered prompts of the past and embrace a new paradigm of clearer, more direct instructions that empower the model's native capabilities. By mastering these core concepts and best practices, developers can unlock unprecedented performance in reasoning, coding, and reliability, building the next generation of intelligent applications.

---

### References

[1] OpenAI. (2025, August 7). *Introducing GPT-5*. [https://openai.com/index/introducing-gpt-5/](https://openai.com/index/introducing-gpt-5/)
[2] Stamatescu, L. (2025, August 18). *GPT-5: The 7 new features enabling real world use cases*. Microsoft Community Hub. [https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/gpt-5-the-7-new-features-enabling-real-world-use-cases/4444839](https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/gpt-5-the-7-new-features-enabling-real-world-use-cases/4444839)
[3] Kirkovska, A. (2025, August 7). *GPT-5 Benchmarks*. Vellum AI Blog. [https://www.vellum.ai/blog/gpt-5-benchmarks](https://www.vellum.ai/blog/gpt-5-benchmarks)
[4] OpenAI. (2025). *GPT-5 Prompting Guide*. OpenAI Cookbook. [https://cookbook.openai.com/examples/gpt-5/gpt-5_prompting_guide](https://cookbook.openai.com/examples/gpt-5/gpt-5_prompting_guide)
[5] Mital, P., Fioca, B., & Mcclellan, C. (2025, September 17). *GPT-5 Troubleshooting Guide*. OpenAI Cookbook. [https://cookbook.openai.com/examples/gpt-5/gpt-5_troubleshooting_guide](https://cookbook.openai.com/examples/gpt-5/gpt-5_troubleshooting_guide)
[6] Khan, A. et al. (2025). *You Don't Need Prompt Engineering Anymore*. arXiv. [https://www.arxiv.org/pdf/2510.22251](https://www.arxiv.org/pdf/2510.22251)
[7] DataCamp. (2025). *GPT-5 Function Calling Tutorial*. [https://www.datacamp.com/tutorial/gpt-5-function-calling-tutorial](https://www.datacamp.com/tutorial/gpt-5-function-calling-tutorial)
[8] Kjosbakken, E. (2025, November 11). *How to Build Agents with GPT-5*. Towards Data Science. [https://towardsdatascience.com/how-to-build-agents-with-gpt-5/](https://towardsdatascience.com/how-to-build-agents-with-gpt-5/)
