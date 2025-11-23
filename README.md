# DomCobb

**v4.2** | *Last updated: 2025-11-21*

> A metaprompt system that helps you create high-quality prompts for AI systems

---

## Description

DomCobb is a specialized metaprompt system that converts vague requests into well-structured, high-quality prompts following prompt engineering best practices. It's an AI agent that creates prompts for other AI systems through a collaborative 13-step iterative process.

This is a personal project shared with friends for feedback and improvements. All suggestions are welcome!

### Core Value

- **Structured Process**: 13-step iterative workflow from vague request to final prompt
- **Knowledge Library**: Curated collection of model-specific and technique-specific best practices
- **Personalization**: Adapts to your communication style via customizable User Patterns
- **Quality Assurance**: Built-in antipatterns detection and evaluation metrics
- **Multi-Provider Support**: Specialized guidance for ChatGPT-5, Claude, Gemini, Sora, Cursor AI, Manus.AI, Lovable.dev, and more

---

## Features

- 13-step structured process with 2-3 typical iteration cycles
- Smart document selection to minimize token usage while maintaining quality
- Comprehensive Knowledge Library organized by AI models and prompting techniques
- Automatic quality checks for antipatterns, vagueness, and best practices
- Support for simple text prompts to complex multi-agent systems
- Platform-agnostic prompts when no specific provider is mentioned

---

## Installation

### Prerequisites

- Access to an AI system that can read and execute metaprompts (Cursor, ChatGPT, Claude, etc.)
- Basic understanding of prompt engineering concepts (helpful but not required)

### Setup

**Option A: Cursor or Similar Software (Recommended)**

1. Clone or download the repository
2. Customize `Knowledge Library/User Preferences.md` (essential for personalized results)
3. Load the metaprompt (`DomCobb.md`) in your AI system
4. Start using DomCobb - Knowledge Library files are accessed automatically via file system

**Option B: Browser-Based Agent (e.g., ChatGPT)**

1. Paste the metaprompt (`DomCobb.md`) directly in a new chat
2. Provide all files from `Knowledge Library/` folder as project context
3. Specify alternative output methods (browser won't have access to `Outputs/` folder)

---

## Usage

### Basic Workflow

1. Load the metaprompt (`DomCobb.md`) in your AI system
2. Describe what you want to achieve (e.g., "I need a prompt to write professional emails")
3. DomCobb asks clarifying questions and validates understanding
4. Review draft prompts and provide feedback
5. Iterate 2-3 times typically until satisfied
6. Receive final prompt ready for production use

### Process Overview

DomCobb follows a structured 13-step process:
1. Introduction and instruction
2. Understand your request
3. Identify user parameters from `User Patterns.md`
4. Synthesize requirements (prompt type, AI provider, objective, context, tone, formats)
5. First validation (align goals before loading Knowledge Library)
6. Identify relevant Knowledge Library documents
7. Generate first draft
8. Goal alignment with critical questions and draft document
9. Adjust based on feedback (typically 2-3 cycles)
10. Generate final prompt
11. Review for inconsistencies and best practices
12. Publish final prompt to `Outputs/agent-name-yyyy-mm-dd.md`
13. Refinement if needed

### Examples

**Simple Prompt**: "I need a prompt to write professional emails in a friendly but formal tone"
- DomCobb loads: `User Patterns.md`, `Technique - Antipatterns.md`
- Output: Professional email writing prompt with tone guidelines

**Complex Agent**: "Create a ChatGPT-5 agent that searches Google stock price and relevant news from the last 24h"
- DomCobb loads: `Model - GPT-5.md`, `Technique - System Prompting.md`, `Technique - Structured Outputs.md`
- Output: Multi-step agent prompt with tool calling and error handling

---

## Project Structure

```
DomCobb/
├── DomCobb.md                    # Main metaprompt file (load this)
├── Knowledge Library/            # Reference materials
│   ├── Essential Files
│   │   ├── User Preferences.md
│   │   ├── Technique - Advanced Prompt Engineering.md
│   │   ├── Technique - Advanced Context & Token Management.md
│   │   ├── Technique - Antipatterns.md
│   │   └── Technique - Prompt Evaluation & Metrics.md
│   ├── Model-Specific Files
│   │   ├── Model - GPT-5.md
│   │   ├── Model - Cursor AI.md
│   │   ├── Model - Manus AI.md
│   │   ├── Model - Claude.md
│   │   ├── gemini-praticas.md
│   │   ├── sora-praticas.md
│   │   ├── chatgpt5-imagens-praticas.md
│   │   └── lovable-praticas.md
│   └── Technique-Specific Files
│       ├── Technique - System Prompting.md
│       ├── Technique - Advanced Metaprompting.md
│       ├── Technique - Structured Outputs.md
│       ├── Technique - RAG & Long Context.md
│       ├── Technique - Cursor Rules.md
│       ├── Technique - Reasoning Scaffolds.md
│       └── seguranca-guardrails.md
├── Outputs/                      # Generated prompts
│   ├── Drafts/                   # Draft versions during iteration
│   └── *.md                      # Final prompts (agent-name-yyyy-mm-dd.md)
└── Internal Files/               # Development documents
    ├── Roadmap.md
    ├── Pending Research Topics.md
    └── Phase 3 - DomCobb Comparison Analysis.md
```

### Knowledge Library

The Knowledge Library is DomCobb's core strength, providing curated best practices:

- **Essential Files**: Always consulted (`User Preferences.md`, `Technique - Advanced Prompt Engineering.md`, `Technique - Advanced Context & Token Management.md`, antipatterns checks, evaluation framework)
- **Model-Specific Files**: Loaded based on AI provider mentioned (ChatGPT-5, Claude, Gemini, etc.)
- **Technique-Specific Files**: Selected based on prompt requirements (system prompting, structured outputs, RAG, etc.)

DomCobb uses smart document selection: always loads Essential Files, then identifies the AI provider and task requirements to load only directly relevant documents, optimizing token usage.

---

## Customization

Customize `Knowledge Library/User Preferences.md` to personalize DomCobb's behavior:

- **Research Preferences**: Recency requirements, source reliability thresholds, citation formats
- **Communication Style**: Formality level, technical jargon tolerance, preferred language
- **Technical Proficiencies**: Programming languages, AI/ML knowledge, domain expertise
- **Output Format**: Document naming conventions, table formatting, risk indicators
- **Methodology**: Preferred reasoning frameworks (MECE, Pyramid Principle, etc.)

Review and modify `User Preferences.md` before your first DomCobb session for best results.

---

## Output Format

DomCobb generates prompts in a standardized format saved as `Outputs/agent-name-yyyy-mm-dd.md`:

1. **SUMMARY**: What the prompt does, key features, use cases (3-5 bullets)
2. **FINAL PROMPT**: Pasteable text with labeled sections (ROLE, GOALS, INSTRUCTIONS, CONTEXT, CONSTRAINTS, OUTPUT SPEC, EVAL)
3. **POSSIBLE VARIABLES**: Customization options and alternative approaches
4. **NOTES AND SOURCES**: Rationale, assumptions, and sources consulted

Drafts are saved to `Outputs/drafts/draft-agent-name-yyyy-mm-dd.md` during iteration cycles.

---

## Supported AI Models/Providers

| AI Provider | Knowledge File | Key Features |
|:------------|:---------------|:-------------|
| **ChatGPT-5** | `Model - GPT-5.md` | Tool calling, reasoning_effort, multi-step flows |
| **ChatGPT-5 Images** | `chatgpt5-imagens-praticas.md` | Cinematic language, shot lists, visual composition |
| **Claude** | `Model - Claude.md` | Extended Thinking, Structured Outputs, XML markup, prefill techniques, Constitutional AI |
| **Gemini** | `gemini-praticas.md` | Multimodal prompts, long context, Workspace integration |
| **Sora** | `sora-praticas.md` | Video generation, cinematic language, camera movements |
| **Cursor AI** | `Model - Cursor AI.md` | Workflow optimization, integration guidelines |
| **Manus.AI** | `Model - Manus AI.md` | Tool orchestration, context engineering |
| **Lovable.dev** | `lovable-praticas.md` | React/Next.js development, component architecture |

If no specific AI provider is mentioned, DomCobb creates **agnostic/portable prompts** that work across multiple platforms.

---

## Contributing

### Knowledge Library Updates

When updating Knowledge Library files:

1. Follow existing file structure and formatting conventions
2. Maintain consistency with other files in the same category
3. Include practical examples and use cases
4. Reference sources using the citation format from `User Patterns.md`
5. Cross-reference related files where appropriate

### Research Guidelines

See `Internal Files/Pending Research Topics.md` for topics to be added to the Knowledge Library, research priorities, and current status.

For detailed development roadmap, see `Internal Files/Roadmap.md`.

---

## Resources

- **Main Metaprompt**: `DomCobb.md` - Load this file to use DomCobb
- **Roadmap**: `Internal Files/Roadmap.md` - Development phases and status
- **Pending Research**: `Internal Files/Pending Research Topics.md` - Topics to be added
- **Example Outputs**: Check `Outputs/` folder for examples of generated prompts

---

## FAQ

**How do I customize DomCobb?**  
Customize `Knowledge Library/User Preferences.md` with your preferences, communication style, and technical proficiencies.

**Which Knowledge Library files should I review?**  
Start with essential files: `User Preferences.md`, `Technique - Advanced Prompt Engineering.md`, `Technique - Advanced Context & Token Management.md`, `Technique - Antipatterns.md`, and `Technique - Prompt Evaluation & Metrics.md`. Then review model-specific and technique-specific files relevant to your use case.

**Can I use DomCobb with other AI systems not listed?**  
Yes! DomCobb can create agnostic prompts that work across platforms. For optimal results, create custom Knowledge Library files following the existing file structure.

**Why does DomCobb ask so many questions?**  
The iterative approach (typically 2-3 cycles) ensures the final prompt is precisely what you need. Provide more detail in your initial request to reduce questions.

---

**Happy Prompt Engineering! 🚀**
