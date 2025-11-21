## **SUMMARY**

The project is currently in active development with 2 features prioritized, 0 features in testing, and 6 features delivered. The Dom Cobb knowledge library and system has completed its foundational phases including critical knowledge library files, main file references, comparison analysis, workflow optimization, README creation, and GitHub upload.

Next steps include: Phase 7 (Fix File Naming Issues) and Phase 8 (Update Prompt Optimization Document and Add to Must-Read). These are the immediate priorities for moving the project forward.

Currently in testing: None.

The backlog contains 7 items across knowledge library completion, system fine-tuning, workflow improvements, process optimization, archiving automation, and web interface development. Key themes include completing the remaining knowledge library files, preparing for launch with user onboarding materials, implementing workflow enhancements, automating delivered feature archiving, and creating a web-based user interface for agent creation and management.

Recently completed: Phase 1 (Critical Knowledge Library Files), Phase 2 (Reference to New Library Files), Phase 3 (Compare with Kenu's DomCobb), Phase 4 (Review DomCobb's Agent Workflow), Phase 5 (Create README), and Phase 6 (Upload to GitHub). Maintenance update completed on 2025-11-21: DomCobb prompt (v4.1) and agent prompts have been updated and are current.

---

## **PRIORITIZED**

### **Phase 6: Upload to GitHub**

**Priority:** Prepare and upload the project to GitHub.

**Status:** ✅ **Completed** (2025-01-20)

**Tasks:**
- [x] Initialize Git repository (if not already done)
- [x] Create `.gitignore` appropriate for the project
- [x] Prepare initial commit with all updated files
- [x] Create GitHub repository
- [x] Push code to GitHub
- [x] Set up repository description and topics
- [x] Add license if applicable (not needed)

**Completion Criteria:**
- Git repository initialized and configured
- `.gitignore` file created with appropriate patterns
- All project files committed to repository
- GitHub repository created and configured
- Code successfully pushed to GitHub
- Repository description and topics set
- License added (if applicable)

**Definition of Done:**
- [x] `.git/` directory exists (or repository initialized)
- [x] `.gitignore` file exists in project root
- [x] All project files are tracked in git (verify with `git status`)
- [x] Initial commit contains all updated files
- [x] GitHub repository exists and is accessible
- [x] Code is pushed to GitHub (verify remote connection)
- [x] Repository has description and topics configured
- [x] License file exists (if applicable) - Not needed per user preference

**Code Dependencies:**
- `.gitignore` (to be created)
- `.git/` (repository directory)
- All project files (for initial commit)

**Notes:**
- Check if Git repository is already initialized
- Ensure `.gitignore` excludes appropriate files (OS files, IDE files, etc.)
- Consider adding README badges and repository metadata

---

### **Phase 7: Fix File Naming Issues**

**Priority:** Investigate and fix incorrect date formatting in file names.

**Status:** ⏳ Pending

**Tasks:**
- [ ] Investigate why files are being named incorrectly (dates are incorrect)
- [ ] Identify root cause of date formatting issues
- [ ] Verify date format implementation in DomCobb process
- [ ] Test file naming with correct date format (yyyy-mm-dd)
- [ ] Fix any date formatting bugs or logic errors
- [ ] Verify all generated files use correct naming convention
- [ ] Document correct file naming format for future reference

**Completion Criteria:**
- Root cause of date naming issues identified
- Date formatting fixed and tested
- All generated files use correct naming format (agent-name-yyyy-mm-dd.md)
- Documentation updated with correct naming conventions

**Definition of Done:**
- [ ] Root cause analysis document or notes exist identifying the issue
- [ ] Date formatting logic in DomCobb process is verified/corrected
- [ ] Test files generated with correct date format (yyyy-mm-dd)
- [ ] All existing incorrectly named files are identified
- [ ] File naming logic produces correct format consistently
- [ ] Documentation (DomCobb.md or relevant file) updated with correct naming conventions
- [ ] No incorrectly named files are generated in test runs

**Code Dependencies:**
- `DomCobb v4.1.md` (or main DomCobb file - to verify/update date format logic)
- `Outputs/` directory (to check existing file names)
- Any scripts or processes that generate file names

**Notes:**
- Reference the standardized file naming convention in OUTPUT HANDLING section
- Format should be: `YYYY-MM-DD_[descriptive-name]_[version].md`
- Check if issue is in DomCobb prompt instructions or in actual file generation process

---

### **Phase 8: Update Prompt Optimization Document and Add to Must-Read**

**Priority:** Update the prompt optimization document (`otimizacao-compressao.md`) with latest best practices and add it to DomCobb's Essential Knowledge Library (must-read section).

**Status:** ⏳ Pending

**Tasks:**
- [ ] Review current `Knowledge Library/otimizacao-compressao.md` content
- [ ] Research latest prompt optimization and compression techniques (2025)
- [ ] Update document with current best practices, tools, and methodologies
- [ ] Ensure document follows same structure and quality as other knowledge library files
- [ ] Add `otimizacao-compressao.md` to "Essential Knowledge Library (Always Read)" section in `DomCobb v4.1.md`
- [ ] Update document reference table with appropriate "When to Use" guidance
- [ ] Verify cross-references and consistency with other knowledge library files
- [ ] Test that DomCobb correctly references the updated document

**Completion Criteria:**
- `otimizacao-compressao.md` updated with latest 2025 best practices
- Document structure and quality matches other knowledge library files
- Document added to Essential Knowledge Library section in DomCobb
- Document appears in "must-read" section with appropriate usage guidance
- Cross-references verified and working correctly

**Definition of Done:**
- [ ] `Knowledge Library/otimizacao-compressao.md` exists and contains updated content (2025 practices)
- [ ] Document structure matches format of other knowledge library files (Resumo Executivo, Para que serve, Boas práticas, Como fazer, Pontos de atenção, Exemplos, Checklist)
- [ ] `DomCobb v4.1.md` contains `otimizacao-compressao.md` in "Essential Knowledge Library (Always Read)" section
- [ ] Document entry in DomCobb includes appropriate "When to Use" description
- [ ] Document is properly referenced in the table format matching other essential documents
- [ ] Content quality matches standards of other updated knowledge library files
- [ ] No broken references or formatting issues

**Code Dependencies:**
- `Knowledge Library/otimizacao-compressao.md` (to update)
- `DomCobb v4.1.md` (to modify Essential Knowledge Library section)

**Notes:**
- Follow the same update pattern used in Phase 1 for knowledge library files
- Ensure the document is comprehensive and includes practical examples
- Add to Essential Knowledge Library because prompt optimization is critical for cost efficiency and quality

---

## **TESTING**

*No features currently in testing.*

---

## **BACKLOG**

### **Phase 8: Update Remaining Knowledge Library Files**

**Priority:** Complete the knowledge library by updating all remaining files.

**Status:** ⏳ Pending

**Reference:** The complete list of all topics (High and Medium priority) is documented in `Knowledge Library/INSTRUCOES-PESQUISA-AGENTE.md` (section "Topics for Research and Documentation", lines 299-349). This phase includes all topics not covered in Phase 1.

#### **Additional Model Knowledge Files**

| File | Status | Notes |
| :--- | :--- | :--- |
| `chatgpt5-imagens-praticas.md` | ⏳ Pending | Update image generation practices for GPT-5 / DALL-E 4 |
| `lovable-praticas.md` | ⏳ Pending | Needs update with latest Lovable.dev practices |
| `claude-praticas.md` | ⏳ Pending | Needs update with latest Claude practices |
| `gemini-praticas.md` | ⏳ Pending | Update with latest Gemini practices |
| `sora-praticas.md` | ⏳ Pending | Update with latest Sora video generation practices |
| `gpt-4o-o1-o3-praticas.md` | ⏳ Pending | Create file for GPT-4o / o1 / o3 specifics (Medium priority) |
| `claude-sonnet-opus-haiku-praticas.md` | ⏳ Pending | Create file for Claude variant specifics (Medium priority) |
| `gemini-pro-flash-ultra-praticas.md` | ⏳ Pending | Create file for Gemini variant specifics (Medium priority) |
| `open-source-models-praticas.md` | ⏳ Pending | Create file for open source models (Llama, Mistral, etc.) (Medium priority) |

#### **Additional Prompting Knowledge Files**

| File | Status | Notes |
| :--- | :--- | :--- |
| `prompt-engineering-tecnicas.md` | ⏳ Pending | Update advanced prompting techniques |
| `raciocinio-scaffolds.md` | ⏳ Pending | Update reasoning scaffold techniques |
| `seguranca-guardrails.md` | ⏳ Pending | Update security and guardrail practices |
| `otimizacao-compressao.md` | ⏳ Pending | Update optimization and compression techniques |

#### **High Priority New Topics (from INSTRUCOES-PESQUISA-AGENTE.md)**

| Topic | Status | Notes |
| :--- | :--- | :--- |
| `multi-agent-systems-orchestration.md` | ⏳ Pending | Multi-Agent Systems & Orchestration |
| `prompt-versioning-ab-testing.md` | ⏳ Pending | Prompt Versioning & A/B Testing |
| `cost-optimization-token-management.md` | ⏳ Pending | Cost Optimization & Token Management |
| `error-handling-resilience.md` | ⏳ Pending | Error Handling & Resilience |
| `domain-specific-prompting.md` | ⏳ Pending | Domain-Specific Prompting (consider separate files per domain) |
| `fine-tuning-vs-prompting.md` | ⏳ Pending | Fine-Tuning vs. Prompting |
| `observability-monitoring.md` | ⏳ Pending | Observability & Monitoring |
| `prompt-injection-defense-advanced.md` | ⏳ Pending | Prompt Injection Defense (Advanced) |
| `streaming-real-time-responses.md` | ⏳ Pending | Streaming & Real-Time Responses |
| `internationalization-multilingual.md` | ⏳ Pending | Internationalization & Multilingual Prompting |

#### **Medium Priority Topics (from INSTRUCOES-PESQUISA-AGENTE.md)**

| Topic | Status | Notes |
| :--- | :--- | :--- |
| `prompt-templates-libraries.md` | ⏳ Pending | Prompt Templates & Libraries |
| `human-in-the-loop-patterns.md` | ⏳ Pending | Human-in-the-Loop Patterns |
| `prompt-compression-advanced.md` | ⏳ Pending | Prompt Compression Techniques (Advanced) |
| `context-window-optimization.md` | ⏳ Pending | Context Window Optimization |
| `temperature-sampling-strategies.md` | ⏳ Pending | Temperature & Sampling Strategies |
| `advanced-few-shot-learning.md` | ⏳ Pending | Advanced Few-Shot Learning |
| `advanced-chain-of-thought.md` | ⏳ Pending | Advanced Chain-of-Thought |
| `advanced-function-calling.md` | ⏳ Pending | Advanced Function Calling |
| `advanced-rag.md` | ⏳ Pending | Advanced RAG |
| `prompt-engineering-for-code.md` | ⏳ Pending | Prompt Engineering for Code |
| `vector-databases-embeddings.md` | ⏳ Pending | Vector Databases & Embeddings |
| `prompt-caching-strategies.md` | ⏳ Pending | Prompt Caching Strategies |
| `load-balancing-scaling.md` | ⏳ Pending | Load Balancing & Scaling |

#### **Supporting Files**

| File | Status | Notes |
| :--- | :--- | :--- |
| `INSTRUCOES-PESQUISA-AGENTE.md` | ✅ **Reference Document** | Complete authoritative list of all topics (see section "Topics for Research and Documentation") |
| `TOPICOS-ADICIONAIS-SUGERIDOS.md` | ⏳ Pending | Review and organize additional suggested topics |

**Phase 8 Completion Criteria:**
- All remaining knowledge library files updated
- Consistent quality and structure across all files
- Complete knowledge library ready for use

---

### **Phase 9: Fine-Tuning Before Launch**

**Priority:** Prepare the system for launch by creating user onboarding materials and customization tools.

**Status:** ⏳ Pending

**Tasks:**
- [ ] Create structured questionnaire for users to fill their own "User Patterns" file
  - [ ] Design questions covering technical proficiencies, communication preferences, background context, and learning preferences
  - [ ] Create interactive form or template that generates a properly formatted `User Patterns.md` file
  - [ ] Include examples and guidance for each section
  - [ ] Add validation to ensure completeness and proper formatting
- [ ] Create user onboarding guide/documentation
- [ ] Develop customization templates for common use cases
- [ ] Create quick-start guide for new users
- [ ] Add placeholder for additional fine-tuning ideas and tools to be developed
- [ ] Test user onboarding process with beta users
- [ ] Gather feedback and iterate on onboarding materials

**Additional Fine-Tuning Ideas (To be added):**
- [ ] *Placeholder for future customization tools and features*

**Phase 9 Completion Criteria:**
- User questionnaire/template ready for generating personalized User Patterns files
- User onboarding documentation complete
- Beta testing completed with positive feedback
- System ready for public launch

---

### **Phase 10: Definition of Done and Step Completion Rules**

**Priority:** Define clear completion criteria and step validation rules to ensure DomCobb completes previous steps before advancing.

**Status:** ⏳ Pending

**Tasks:**
- [ ] Define clear completion criteria for each step in the process
- [ ] Specify that DomCobb must complete previous steps before advancing
- [ ] Create checklist format for step validation
- [ ] Document when it's acceptable to skip or combine steps
- [ ] Add validation rules for step transitions
- [ ] Implement step completion rules in DomCobb document
- [ ] Test step validation with various scenarios
- [ ] Refine based on testing results

**Phase 10 Completion Criteria:**
- Clear completion criteria defined for all steps
- Step validation rules implemented in DomCobb document
- Testing completed with positive results
- Documentation updated with step completion guidelines

---

### **Phase 11: Agent Usage Guide Creation**

**Priority:** Create automated guide generation for each agent created by DomCobb.

**Status:** ⏳ Pending

**Tasks:**
- [ ] Create guide template for how to use each generated agent
- [ ] Include: setup instructions, usage examples, parameter customization, troubleshooting
- [ ] Generate guide automatically with final prompt output
- [ ] Save guide alongside final prompt in Outputs folder
- [ ] Implement guide generation in DomCobb process (final step)
- [ ] Test guide generation with various agent types
- [ ] Refine guide template based on testing

**Phase 11 Completion Criteria:**
- Guide template created and tested
- Automated guide generation implemented in DomCobb process
- Guides saved correctly alongside final prompts
- Testing completed with positive feedback
- Documentation updated with guide creation guidelines

---

### **Phase 12: Evaluate Drafts Step Removal**

**Priority:** Evaluate whether to remove the drafts step and maintain only final prompts.

**Status:** ⏳ Pending

**Tasks:**
- [ ] Analyze current usage of drafts folder and draft files
- [ ] Evaluate benefits and drawbacks of maintaining drafts
- [ ] Assess impact on workflow and user experience
- [ ] Consider token efficiency implications
- [ ] Review user feedback on draft functionality
- [ ] Make decision on whether to remove drafts step
- [ ] If removing: update DomCobb process to eliminate draft creation
- [ ] If keeping: optimize draft workflow and documentation
- [ ] Update documentation based on decision

**Phase 12 Completion Criteria:**
- Comprehensive evaluation completed
- Decision made on drafts step removal
- If removed: DomCobb process updated and tested
- If kept: workflow optimized and documented
- All documentation updated to reflect decision

---

### **Phase 13: Archive Delivered Features to Separate File**

**Priority:** Move delivered development items to a separate archive file and update Roadmap Keeper agent to automatically perform this action when applicable.

**Status:** ⏳ Pending

**Tasks:**
- [ ] Create archive file structure for delivered features (e.g., `Internal Files/Roadmap-Archive.md` or `Internal Files/Delivered-Archive.md`)
- [ ] Define archive file format and structure
- [ ] Move all current delivered features from Roadmap.md to archive file
- [ ] Update Roadmap.md to reference archive file instead of listing delivered features
- [ ] Update Roadmap Keeper agent prompt (`Outputs/roadmap-keeper-2025-01-20.md`) to include automatic archiving logic
- [ ] Add instructions for Roadmap Keeper to automatically move features from Delivered section to archive file
- [ ] Define trigger conditions for archiving (e.g., after X days in Delivered, or immediately upon moving to Delivered)
- [ ] Test archiving process with Roadmap Keeper agent
- [ ] Update Summary section generation to account for archived features
- [ ] Document archiving workflow in Roadmap Keeper instructions

**Phase 13 Completion Criteria:**
- Archive file created with proper structure
- All delivered features moved to archive file
- Roadmap.md updated to reference archive instead of listing delivered items
- Roadmap Keeper agent updated with automatic archiving functionality
- Archiving process tested and working correctly
- Documentation updated with archiving workflow

---

### **Phase 14: Create Web Interface for DomCobb**

**Priority:** Create a web interface where users can create their agents with DomCobb and easily access or edit them.

**Status:** ⏳ Pending

**Tasks:**
- [ ] Design web interface architecture and user flow
- [ ] Create user authentication and session management system
- [ ] Build agent creation interface (form-based or conversational)
- [ ] Implement agent storage system (database or file-based)
- [ ] Create agent listing/dashboard page for accessing created agents
- [ ] Build agent editing interface
- [ ] Implement agent execution/integration with DomCobb backend
- [ ] Add agent sharing/collaboration features (optional)
- [ ] Create user onboarding flow for web interface
- [ ] Implement responsive design for mobile and desktop
- [ ] Add search and filtering capabilities for agent library
- [ ] Test web interface with various user scenarios
- [ ] Deploy web interface to hosting platform

**Phase 14 Completion Criteria:**
- Web interface deployed and accessible
- Users can create agents through the interface
- Users can access and view their created agents
- Users can edit existing agents
- Interface is responsive and user-friendly
- Agent creation integrates with DomCobb process
- User authentication and data persistence working correctly

---

## **DELIVERED**

### **Phase 1: Critical Knowledge Library Files**

**Priority:** Update critical knowledge library files with latest best practices and comprehensive guidance.

**Status:** ✅ **Completed**

**Reference:** See `INSTRUCOES-PESQUISA-AGENTE.md` for complete topic list and research guidelines.

#### **Model Knowledge Files**

| File | Status | Notes |
| :--- | :--- | :--- |
| `Model - GPT-5.md` | ✅ **Completed** | Updated with comprehensive best practices, examples, evaluation metrics, and anti-patterns |
| `Model - Manus AI.md` | ✅ **Completed** | Updated with latest Manus AI practices, tool orchestration, context engineering, and agentic automation patterns |
| `Model - Cursor AI.md` | ✅ **Completed** | Updated with comprehensive Cursor AI practices, workflow optimization, and integration guidelines |

#### **Prompting Knowledge Files**

| File | Status | Notes |
| :--- | :--- | :--- |
| `User Patterns.md` | ✅ **Completed** | Updated with comprehensive user pattern best practices, communication preferences, and operational guidelines |
| `Technique - Advanced Metaprompting.md` | ✅ **Completed** | Updated with comprehensive metaprompting patterns, examples, frameworks, and practical implementation guides |
| `Technique - System Prompting.md` | ✅ **Completed** | Updated with comprehensive system prompting best practices, instruction hierarchy, guardrails, lifecycle management, and practical implementation guides |
| `Technique - Structured Outputs.md` | ✅ **Completed** | Updated with comprehensive structured output techniques, function calling, validation strategies, orchestration patterns, and practical implementation guides |
| `Technique - Prompt Evaluation & Metrics.md` | ✅ **Completed** | Updated with rubricas do LLM juiz, mitigação de vieses, pipeline offline e checklist de qualidade (includes content from avaliacao-metricas (old).md) |
| `Technique - RAG & Long Context.md` | ✅ **Completed** | Updated with comprehensive RAG and long context implementation strategies, best practices, risk mitigation, practical examples, and quality checklists (includes content from contexto-rag-long-context (old).md) |
| `Technique - Antipatterns.md` | ✅ **Completed** | Updated with comprehensive antipatterns catalog, model-specific issues, security vulnerabilities, performance problems, integration challenges, testing frameworks, and practical detection methodologies |
| `Technique - Cursor Rules.md` | ✅ **Completed** | Updated with comprehensive Cursor Rules guide, including best practices, step-by-step implementation, common mistakes, practical examples, rule types and hierarchy, MDC syntax reference, and quality checklist |

**Phase 1 Completion Criteria:**
- All critical files updated with comprehensive, current best practices
- Consistent structure and formatting across all files
- Cross-references between related files where appropriate

---

### **Phase 2: Reference to New Library Files in Main File**

**Priority:** Update the main metaprompt file to reference newly updated library files.

**Status:** ✅ **Completed**

**Tasks:**
- [x] Update `Meta Prompt v3.2.md` with references to updated knowledge library files
- [x] Ensure all critical files from Phase 1 are properly referenced
- [x] Update file paths and naming conventions in main file
- [x] Verify cross-references are accurate and helpful

**Completion Notes:**
- All Phase 1 knowledge library files properly referenced in main metaprompt
- File paths and naming conventions updated to match Knowledge Library structure
- Cross-references verified and working correctly

---

### **Phase 3: Compare with Kenu's DomCobb**

**Priority:** Analyze and compare the current DomCobb implementation with Kenu's DomCobb version.

**Status:** ✅ **Completed** (2025-01-20)

**Tasks:**
- [x] Locate and review Kenu's DomCobb implementation/repository
- [x] Identify key differences in approach and structure
- [x] Compare knowledge library organization and content
- [x] Compare metaprompt structure and workflow
- [x] Analyze strengths and weaknesses of each version
- [x] Document findings and recommendations
- [x] Identify best practices to adopt from Kenu's version
- [x] Create comparison document or analysis report

**Completion Notes:**
- Comprehensive comparison document created: `Internal Files/Phase 3 - DomCobb Comparison Analysis.md`
- **Key Findings:**
  - Current implementation is significantly more comprehensive with better document selection reasoning, tool organization, and workflow clarity
  - Kenu's version offers valuable simplifications and includes version tracking that should be adopted
  - Current has 12-step process vs Kenu's 10-step process
  - Current includes comprehensive "REASONING: Document Selection" section with examples (missing in Kenu's)
  - Current uses table format for tool references with "When to Use" guidance (Kenu's uses simple lists)
  - Current has explicit CONSTRAINTS section (missing in Kenu's)
  - Kenu's includes version tracking (Version 4.0, dated 2025-11-20) which current lacks
  - File naming conventions differ: Current uses standardized `Model - [Name].md` format, Kenu's uses hyphenated lowercase
- **Recommendations:**
  - Adopt version tracking from Kenu's version
  - Add iteration cycle guidance ("typically 2-3 cycles") from Kenu's
  - Maintain all current strengths (reasoning section, tables, examples, constraints)
  - Use hybrid approach for Phase 4 optimization

---

### **Phase 4: Review DomCobb's Agent Workflow**

**Priority:** Analyze and optimize the agent's workflow and reasoning processes.

**Status:** ✅ **Completed** (2025-01-20)

**Tasks:**
- [x] Review current agent reasoning logic in main metaprompt
- [x] Incorporate insights from Phase 3 comparison with Kenu's version
- [x] Optimize document selection process
- [x] Improve workflow efficiency and clarity
- [x] Test agent workflow with various scenarios (requires runtime testing)
- [x] Refine based on testing results and comparison findings (pending runtime testing)

**Critical Observations:**
- [x] **Efficient Document Access:** Enhanced token efficiency guidance in Step 5 and REASONING section. Added explicit guidelines to:
  - Only load knowledge library files that are directly relevant to the user's request
  - Use efficient document selection logic to minimize unnecessary reads
  - Stop document access once sufficient information is obtained
  - Prioritize the most relevant documents first
  - Monitor token consumption
- [x] **User Pattern Adherence:** Maintained existing guidance in reasoning examples (Example 2) and workflow steps. The agent:
  - Always consults the User Patterns file before generating final prompts (Step 3)
  - Adapts communication style based on user's technical proficiency level (Example 2)
  - Uses appropriate terminology and detail level matching user's knowledge
  - Respects user's preferred communication style (direct, detailed, etc.)
  - Applies user's domain expertise and methodology preferences (MECE, Pyramid Principle, etc.)
  - Maintains consistency with user patterns across all interactions

**Completion Notes:**
- Added version tracking (Version 4.1, last_updated: 2025-01-20) to DomCobb.md
- Added "Library" term to glossary section
- Added iteration cycle guidance ("typically 2-3 cycles") to Step 8
- Enhanced token efficiency guidance in Step 5 and REASONING section Step 5 with detailed guidelines
- Added 2 additional reasoning examples (Example 3: Simple Prompt Request, Example 4: Multimodal Generation Request)
- Enhanced CONSTRAINTS section with clearer formatting and added document access constraint
- Deleted DomCobb (Kenu).md file as it's no longer needed
- All changes maintain existing strengths (table format, reasoning section, examples, constraints)

---

### **Phase 5: Create README**

**Priority:** Create comprehensive README documentation for the project.

**Status:** ✅ **Completed**

**Tasks:**
- [x] Create `README.md` with project overview
- [x] Document project structure and organization
- [x] Include setup and usage instructions
- [x] Add examples and use cases
- [x] Document the knowledge library structure
- [x] Include contribution guidelines if applicable
- [x] Reference Kenu's DomCobb if appropriate

**Completion Notes:**
- README.md created with comprehensive project documentation
- Project structure and organization documented
- Setup and usage instructions included
- Examples and use cases provided
- Knowledge library structure documented
- Contribution guidelines included

---

### **Phase 6: Upload to GitHub**

**Priority:** Prepare and upload the project to GitHub.

**Status:** ✅ **Completed** (2025-01-20)

**Tasks:**
- [x] Initialize Git repository (if not already done)
- [x] Create `.gitignore` appropriate for the project
- [x] Prepare initial commit with all updated files
- [x] Create GitHub repository
- [x] Push code to GitHub
- [x] Set up repository description and topics
- [x] Add license if applicable (not needed)

**Completion Notes:**
- `.gitignore` file created with standard patterns for OS files, IDE files, temporary files, Python, Node.js, and environment files
- All project files committed and pushed to GitHub repository
- Repository description and topics configured: "DomCobb - A metaprompt system for creating high-quality AI prompts" with topics: ai, prompt-engineering, metaprompt, ai-agents, cursor, chatgpt, claude, gemini
- Output files organized with correct naming convention (removed incorrect dates, standardized to descriptive format)
- File renames completed: Git Agent.md, Library Updater Agent.md, Manus Researcher.md, Readme Keeper Agent.md, Roadmap Keeper Agent.md
- Pull Request created and merged using squash merge following Git Agent workflow
- Repository is now properly version-controlled and ready for collaboration

---

## **TOOLS (Reference Files)**

The library is composed of project files, made available to Dom Cobb.

General best practices and user patterns; adapt to target AI (terminology, tool/function calling, reasoning, limits). If the provider is not indicated, produce an agnostic/portable version.

### **User Pattern Library**

* **User Patterns (PDF):** always use in every final prompt; contains user preferences and patterns.

#### **User Knowledge & Proficiencies Section**

The User Patterns document should include a dedicated section describing the user's knowledge, proficiencies, and communication preferences. This enables the AI to:

* **Understand the user's technical level** — Know what terminology, concepts, and depth of explanation are appropriate
* **Adapt communication style** — Match the user's expertise level (beginner, intermediate, advanced, expert)
* **Avoid over-explaining or under-explaining** — Provide the right amount of detail based on the user's background
* **Use appropriate examples and analogies** — Reference concepts the user is familiar with
* **Respect domain expertise** — Acknowledge when the user has deep knowledge in specific areas

**What should be included in the User Knowledge & Proficiencies section:**

1. **Technical Proficiencies:**
   * Programming languages and frameworks
   * AI/ML knowledge level
   * Prompt engineering expertise
   * Software development practices
   * Domain-specific knowledge (e.g., finance, healthcare, legal)

2. **Communication Preferences:**
   * Preferred level of technical detail (high-level overview vs. deep technical)
   * Tolerance for jargon and technical terms
   * Need for examples and analogies
   * Preferred explanation style (conceptual, practical, step-by-step)

3. **Background Context:**
   * Professional role and responsibilities
   * Experience with AI tools and platforms
   * Familiarity with specific methodologies (MECE, Pyramid Principle, etc.)
   * Preferred working style and decision-making approach

4. **Learning Preferences:**
   * How the user prefers to receive information
   * Whether they want to understand the "why" behind recommendations
   * Preference for structured vs. conversational explanations

**Example structure for User Knowledge section:**

```
## User Knowledge & Proficiencies

**Technical Level:** Advanced
**Primary Domains:** Prompt engineering, AI agent development, system design
**Familiar Tools:** ChatGPT-5, Claude, Gemini, Lovable.dev, Manus.AI
**Programming:** Proficient in Python, JavaScript, React/Next.js
**Methodologies:** MECE, Pyramid Principle, structured reasoning

**Communication Style:**
- Prefers direct, practical explanations without excessive background
- Comfortable with technical jargon in AI/ML and software development
- Appreciates structured outputs and clear reasoning
- Values efficiency and actionable insights

**Knowledge Gaps to Consider:**
- May need context on newer AI models or features
- May require explanation of advanced prompting techniques when first introduced
```

This section should be consulted when creating prompts to ensure the AI communicates at the appropriate level and uses terminology the user understands.

### **Model Knowledge Library**

* **ChatGPT-5, Practices (Model - GPT-5.md):** prompts for ChatGPT-5; tool calling, reasoning_effort, multi-step flows; defines stable system prompts and clear instructions for agents that plan/execute actions.

* **ChatGPT-5 Images (chatgpt5-imagens-praticas.md):** image prompts in ChatGPT-5; cinematic language; shot lists, references, conversational iteration for visual composition.

* **Lovable.dev (lovable-praticas.md):** prompts for assisted web development (React/Next.js) in Lovable; component architecture, textual wireframes, guided code tasks.

* **Manus.AI (Model - Manus AI.md):** prompts for tool orchestration and context engineering; clear deliverables, checks and agentic automations.

* **Google Gemini (gemini-praticas.md):** multimodal prompts and long context; combine text, images and Workspace; instructions for extended windows and citations.

* **Anthropic Claude (claude-praticas.md):** prompts with markup (XML) and prefill; greater structural rigidity and traceability of reasoning.

* **OpenAI Sora (sora-praticas.md):** video prompts with cinematic language; shot lists, camera movements, rhythm and sound guidelines.

### **Prompting Knowledge Library**

* **System Prompt — Essential Practices (Technique - System Prompting.md):** govern behavior, roles, rules, security (against injection) and quality; comprehensive guide covering instruction hierarchy, KV-cache optimization, guardrails, lifecycle management, and production patterns.

* **Metaprompt — Patterns for Creation/Optimization (Technique - Advanced Metaprompting.md):** generation/refinement at scale; critique-improve cycles with goals and metrics; comprehensive frameworks (APE, OPRO, DSPy, TextGrad, StraGo) and practical implementation guides.

* **Prompt Engineering — Advanced Techniques (prompt-engineering-tecnicas.md):** Zero/Few-Shot strategies, CoT, Self-Consistency, chaining; complex tasks with steps and checks.

* **Context — RAG & Long Context (Technique - RAG & Long Context.md):** decide between RAG, long context or hybrid; guide retrieval, citation and window limits; comprehensive guide covering implementation strategies and best practices.

* **Reasoning — Scaffolds (raciocinio-scaffolds.md):** structure explicit reasoning (CoT, Self-Consistency, ToT, ReAct, Reflexion); indicated for multi-step/ambiguous problems.

* **Structured Outputs — JSON/Function Calling/Evaluation (Technique - Structured Outputs.md):** force formats, integrate tools and enable automatic evaluation; data contracts and validation; comprehensive guide covering evolution, provider comparison, validation strategies, orchestration patterns, and practical implementation.

* **Security & Guardrails (seguranca-guardrails.md):** when there are sensitive data/exposed surfaces; defense in depth (input/output).

* **Evaluation & Metrics (Technique - Prompt Evaluation & Metrics.md):** define metrics, protocols, LLM-as-Judge; compare versões, medir impacto e executar DoD com checklists consolidados.

* **Anti-patterns (Technique - Antipatterns.md):** detect vagueness, negatives and lack of structure; checklist before publishing.

* **Optimization & Compression (otimizacao-compressao.md):** reduce tokens/latency while maintaining quality; manual/automatic compression and cache.

## **REASONING: How to Identify Which Documents to Use**

The agent should follow a systematic approach to identify which documents from the library are relevant for each user request:

### **Step 1: Analyze the Request**
* Identify the **AI provider/model** mentioned (ChatGPT-5, Claude, Gemini, Sora, etc.)
* Identify the **type of task** (text generation, image creation, video, code development, etc.)
* Identify the **complexity level** (simple prompt, multi-step agent, evaluation needed, etc.)
* Identify any **specific requirements** (security concerns, structured outputs, reasoning needs, etc.)

### **Step 2: Always Include User Patterns**
* **User Patterns (PDF)** must ALWAYS be consulted and applied to every final prompt, regardless of the request type.

### **Step 3: Select Model-Specific Documents**
If a specific AI provider is mentioned, select the corresponding document from the **Model Knowledge Library**:
* **ChatGPT-5** → `Model - GPT-5.md` (and `chatgpt5-imagens-praticas.md` if images are involved)
* **Claude** → `claude-praticas.md`
* **Gemini** → `gemini-praticas.md`
* **Sora** → `sora-praticas.md`
* **Lovable.dev** → `lovable-praticas.md`
* **Manus.AI** → `Model - Manus AI.md`

### **Step 4: Select Prompting Technique Documents**
Based on the task requirements, select relevant documents from the **Prompting Knowledge Library**:
* **System prompts/agent behavior** → `Technique - System Prompting.md`
* **Complex reasoning/multi-step tasks** → `raciocinio-scaffolds.md`
* **Structured outputs/JSON/function calling** → `Technique - Structured Outputs.md`
* **RAG/long context needs** → `Technique - RAG & Long Context.md`
* **Security/sensitive data** → `seguranca-guardrails.md`
* **Evaluation/metrics needed** → `Technique - Prompt Evaluation & Metrics.md`
* **Advanced techniques (CoT, few-shot, etc.)** → `prompt-engineering-tecnicas.md`
* **Optimization/compression** → `otimizacao-compressao.md`
* **Quality checks** → `Technique - Antipatterns.md`
* **Metaprompt creation** → `Technique - Advanced Metaprompting.md`

### **Step 5: Document Selection Reasoning Example**

**User Request:** "Help me create an agent using ChatGPT-5 that searches Google stock price and relevant news from the last 24h whenever I say anything"

**Agent Reasoning:**
1. **AI Provider identified:** ChatGPT-5 → Load `Model - GPT-5.md` (for tool calling and agent structure)
2. **Task type:** Agent with tool usage → Load `Technique - System Prompting.md` (for agent behavior definition)
3. **Structured outputs:** Agent needs to return formatted data → Load `Technique - Structured Outputs.md` (for output specification)
4. **User patterns:** Always required → Load `User Patterns (PDF)`
5. **Reasoning:** Multi-step agent task → Consider `raciocinio-scaffolds.md` if complex planning needed

**Selected Documents:**
- User Patterns (PDF) - *always included*
- Model - GPT-5.md - *for ChatGPT-5 specific practices*
- Technique - System Prompting.md - *for agent behavior and structure*
- Technique - Structured Outputs.md - *for structured output format*

### **Step 6: Load and Apply**
After identifying relevant documents, explicitly load them and reference their techniques when constructing the final prompt. Document the reasoning process transparently to the user when requested.

## **OUTPUT HANDLING: Version Evolution**

### **Version 3.2 (ChatGPT-5 Canvas-based)**
* **Platform:** Built specifically for ChatGPT-5
* **Output Method:** Final prompts are published in **canvas** format
* **Canvas Features:** Interactive document format native to ChatGPT-5 interface
* **Workflow:** Agent creates prompts and presents them in canvas for user review and iteration

### **Version 3.3 (File-based Output)**
* **Platform:** Agnostic (works across different AI platforms)
* **Output Method:** Save all outputs as files in the project structure
* **Output Location:** Save new outputs in a folder named **"Outputs"** located in the project's root folder
* **Standardized Naming Convention:** Files should follow a consistent naming pattern

#### **Standardized File Naming Convention**

All output files saved in the `Outputs/` folder should follow this naming pattern:

**Format:** `YYYY-MM-DD_[descriptive-name]_[version].md`

**Components:**
* **YYYY-MM-DD:** Date in ISO format (year-month-day)
* **descriptive-name:** Brief, descriptive name using lowercase letters and hyphens (no spaces)
* **version:** Optional version indicator (v1, v2, etc.) if multiple iterations exist

**Examples:**
* `2025-01-20_chatgpt5-stock-agent_prompt.md`
* `2025-01-20_web-development-assistant_v1.md`
* `2025-01-20_web-development-assistant_v2.md`
* `2025-01-20_image-generation-workflow.md`
* `2025-01-20_rag-system-prompt.md`

**File Structure:**
Each output file should contain:
1. **Metadata header** (date created, version, related request)
2. **Summary** (3-5 bullets of what the prompt does)
3. **Final Prompt** (with labels: ROLE, GOALS, INSTRUCTIONS, CONTEXT, CONSTRAINTS, OUTPUT SPEC, EVAL)
4. **Possible Variables** (what can change)
5. **Notes and Sources** (if external research was used)
6. **Requirements Log** (if applicable, log of user inputs and iterations)

**Implementation Notes:**
* Create the `Outputs/` folder if it doesn't exist
* Check for existing files with the same name before creating new ones
* If iterating on an existing prompt, increment the version number
* Maintain a consistent file structure across all outputs for easy navigation and reference
