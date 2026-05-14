# AB-100 Study Supplements
## Flashcards • Expanded Practice Exam • Exam-Morning Cram Sheet

This document has three independent sections. Use each separately:

- **Section A — Flashcards (50 cards)** — for spaced repetition on terminology and concepts. Cover the answer side and self-test.
- **Section B — Expanded Practice Exam (50 questions)** — scenario-style, organized by domain weight. Take it under time pressure as a dress rehearsal.
- **Section C — Exam-Morning Cram Sheet** — one page to read 30 minutes before the exam. Nothing new; just the decision-tables and traps you must have at your fingertips.

---

# SECTION A — Flashcards (50 cards)

**How to use:** read the prompt (Q), say the answer aloud, then check (A). Mark hard ones; cycle them more often. Three passes over two days locks most of this in.

---

### Card 1
**Q:** What's the prerequisite to earn the Agentic AI Business Solutions Architect certification?
**A:** Pass AB-100 **plus** hold one qualifying Associate-level cert (any Dynamics 365 Associate, any Power Platform Associate, **AI-102 (Azure AI Engineer)**, Power Automate RPA Developer). AI-102 satisfies the prerequisite.

---

### Card 2
**Q:** AB-100 exam logistics — duration, passing score, format?
**A:** 100 minutes; 700/1000 to pass; multiple choice + case studies + drag-and-drop + interactive items; proctored.

---

### Card 3
**Q:** What are the three domains and their weights?
**A:** Plan AI-powered business solutions (25–30%); Design AI-powered business solutions (25–30%); **Deploy AI-powered business solutions (40–45%)**.

---

### Card 4
**Q:** Name the four pillars of Microsoft's AI platform.
**A:** Microsoft Foundry; Microsoft Copilot Studio; Microsoft 365 Copilot; Dynamics 365 Copilot.

---

### Card 5
**Q:** When do you use Microsoft Foundry vs Copilot Studio?
**A:** **Foundry** — developers/architects, code-first, custom orchestration, model catalog, custom Python tools. **Copilot Studio** — makers/business users, low-code, topics + generative orchestration, easy M365/Teams publishing.

---

### Card 6
**Q:** What's the resource hierarchy in Microsoft Foundry?
**A:** **Foundry resource** (top-level Azure resource) → **Project** (isolated development unit) → **Deployments** (specific model instances). Existing Azure OpenAI resources can be auto-upgraded.

---

### Card 7
**Q:** Four Foundry model deployment types?
**A:** **Standard / serverless** (pay-per-token, default); **Provisioned Throughput (PTU)** (reserved capacity for predictable latency); **Managed compute** (dedicated GPU for self-hosted models); **Foundry Local** (edge/on-device).

---

### Card 8
**Q:** Name 6 Foundry Tools you can attach to an agent.
**A:** Functions (custom code); File search (built-in RAG over uploaded files); Code interpreter (Python sandbox); Bing search; Connected actions; MCP servers; OpenAPI tools.

---

### Card 9
**Q:** What is Foundry IQ?
**A:** Foundry's **knowledge grounding** capability — wires up retrieval (typically Azure AI Search with hybrid + semantic ranker) so agents answer with citations to authoritative sources. Supports ACL-aware filtering for permission propagation.

---

### Card 10
**Q:** Three agent paradigms (per AB-100 blueprint).
**A:** **Prompt-and-response** (short-turn Q&A); **Task agent** (multi-step bounded task with clear inputs/outputs); **Autonomous agent** (event-driven, long-running, self-acting within guardrails).

---

### Card 11
**Q:** What is a "topic" in Copilot Studio?
**A:** A conversation unit (dialog flow). Two types: **trigger-phrase topics** (fire on matched user utterances) and **event topics** (fire on system events like greeting, fallback, escalation).

---

### Card 12
**Q:** Classic vs generative orchestration in Copilot Studio?
**A:** **Classic** — NLU intent matching runs the matched topic's deterministic dialog (predictable, controlled). **Generative** — LLM plans across all topics, knowledge sources, and actions to satisfy user intent (flexible, conversational). Generative wins for ambiguous, multi-intent requests.

---

### Card 13
**Q:** What's a "fallback topic" and why does it matter?
**A:** The event topic that fires when no other topic matches confidently. Design it to: provide a helpful response (not "I didn't understand"), offer escalation to a human, log the unmatched utterance for improvement, optionally invoke generative answers over knowledge sources as graceful fallback.

---

### Card 14
**Q:** What's an "agent flow" in Copilot Studio (2026)?
**A:** A multi-step, often long-running, agent-driven workflow construct. Can include human approvals, branching, parallel execution. The implementation surface for **autonomous agents** in Copilot Studio.

---

### Card 15
**Q:** What is a "prompt action" in Copilot Studio?
**A:** A reusable AI prompt (built in AI hub or Copilot Studio) you call as a step inside a topic — e.g., "summarize this email" or "extract entities." Output goes to a variable for downstream use.

---

### Card 16
**Q:** What's a "declarative agent" in Microsoft 365 Copilot?
**A:** A JSON-defined agent that scopes M365 Copilot to a specific domain (instructions, knowledge sources, conversation starters, capabilities). Published to Teams / M365 Copilot. Built in Copilot Studio or as code. Low-code path to extending M365 Copilot.

---

### Card 17
**Q:** Declarative agent vs custom engine agent in M365 Copilot?
**A:** **Declarative** uses the M365 Copilot orchestrator and Azure OpenAI; you supply config (instructions, knowledge, capabilities) — low engineering effort. **Custom engine agent** uses your own model + orchestration (typically Foundry), surfaced in Teams/M365 via the Teams app + agent SDK — when you need custom models, orchestration, or compliance not satisfied by declarative.

---

### Card 18
**Q:** Difference between an M365 Copilot agent and a plugin?
**A:** **Agent** = self-contained Copilot experience (specialized scope) the user explicitly invokes. **Plugin** = extension to the default M365 Copilot (additional knowledge or actions) accessible during normal Copilot conversations. Agents specialize; plugins enrich.

---

### Card 19
**Q:** What's a Microsoft Graph connector?
**A:** An indexer that brings third-party data (CRM, wiki, file shares, Salesforce, etc.) into Microsoft Search so it becomes discoverable by M365 Copilot and other Graph-aware experiences. The way you ground M365 Copilot in non-M365 data without building an agent.

---

### Card 20
**Q:** M365 Copilot for Sales — what is it and which CRMs does it support?
**A:** Prebuilt M365 Copilot experience connecting M365 (Outlook, Teams) with CRM. Supports **both Dynamics 365 Sales and Salesforce**. Provides email drafting with CRM data, meeting prep, opportunity insights, conversational CRM access.

---

### Card 21
**Q:** What is Microsoft Agent Framework (MAF)?
**A:** Microsoft's unified agent SDK, **GA April 2026**, merging AutoGen and Semantic Kernel. Both predecessors are in maintenance mode. Combines AutoGen's multi-agent patterns with Semantic Kernel's enterprise features (type safety, middleware, observability, .NET-first). Python and .NET. Recommended path for Azure/.NET agent development.

---

### Card 22
**Q:** What is the Model Context Protocol (MCP)?
**A:** Open standard introduced by Anthropic (Nov 2024), donated to Linux Foundation (Dec 2025). Standardizes how AI applications connect to data sources and tools. Transport: JSON-RPC 2.0 over stdio or HTTP/SSE. Adopted across Microsoft, OpenAI, Google. The universal **agent-to-tool** layer.

---

### Card 23
**Q:** Three MCP primitives?
**A:** **Tools** (model-controlled functions like `query_db`); **Resources** (app-controlled context like the open file); **Prompts** (user-controlled templates / slash commands).

---

### Card 24
**Q:** What is Agent2Agent (A2A) protocol?
**A:** Open standard for **inter-agent communication** — how agents from different vendors/platforms discover each other, exchange messages, hand off tasks. Complements MCP. A2A = agent-to-agent; MCP = agent-to-tool.

---

### Card 25
**Q:** Top MCP security risks and one mitigation each?
**A:** Indirect prompt injection (treat tool output as untrusted); tool poisoning (review server descriptions, sandbox); rug pulls (pin versions, hash schemas); excessive scope (least privilege); untrusted ingestion (validate inputs).

---

### Card 26
**Q:** What is "Computer Use" in Copilot Studio?
**A:** A 2026 capability where an agent controls a browser or app via UI automation (screenshots + mouse/keyboard). For legacy apps without APIs. Risks: non-determinism (UI changes), security, audit needs. Last-resort when API integration isn't possible.

---

### Card 27
**Q:** Difference between Computer Use and Power Automate Desktop RPA?
**A:** **Power Automate Desktop RPA** is deterministic — scripted clicks on known UI elements. **Computer Use** is AI-driven — agent reasons about the screen and chooses actions, can adapt within bounds. Use RPA for stable, well-defined flows; Computer Use when AI judgment is needed.

---

### Card 28
**Q:** Cloud Adoption Framework (CAF) for AI — name the phases.
**A:** **Strategy → Plan → Ready → Adopt → Govern / Manage / Secure** (ongoing). Strategy defines outcomes; Plan assesses readiness and chooses use cases; Ready sets up the platform; Adopt scales workloads; Govern/Manage/Secure run them.

---

### Card 29
**Q:** What is the Microsoft AI Center of Excellence (CoE)?
**A:** Cross-functional team establishing standards, reusable assets, governance, enablement for AI across the org. Components: executive sponsor, governance council, technical platform team, business enablement, security/compliance liaison, prompt library, training, intake/prioritization, value tracking.

---

### Card 30
**Q:** The six Responsible AI principles?
**A:** **Fairness; Reliability and Safety; Privacy and Security; Inclusiveness; Transparency; Accountability.** Underpinned by **human oversight**. You must know them by name and what each implies.

---

### Card 31
**Q:** Which Responsible AI principle covers preventing bias against protected groups?
**A:** **Fairness.** Implementation: bias assessment across protected attributes; ongoing fairness monitoring; documented mitigation plan; human review of decisions.

---

### Card 32
**Q:** Which Responsible AI principle covers preventing prompt injection?
**A:** **Privacy and Security** (primary) and **Reliability and Safety** (secondary — preventing harmful outcomes). Defenses: prompt shields, content safety, least-privilege tools, human-in-the-loop on sensitive actions.

---

### Card 33
**Q:** Direct vs indirect prompt injection?
**A:** **Direct** — attacker manipulates input directly ("ignore previous instructions"). **Indirect** — attacker plants instructions in retrieved content (poisoned document, malicious web page). Indirect is the bigger risk in agentic systems with retrieval.

---

### Card 34
**Q:** What are "prompt shields"?
**A:** Azure AI Content Safety feature that detects direct and indirect prompt injection. Configurable thresholds; logs feed audit trail.

---

### Card 35
**Q:** What is "groundedness detection"?
**A:** Content Safety feature that flags model claims **not supported by retrieved context** — the hallucination signal for RAG outputs.

---

### Card 36
**Q:** Build vs Buy vs Extend — decision logic?
**A:** **Buy/configure** when prebuilt fits (fastest TTV). **Extend** when prebuilt is 70%+ fit; customization fits extension points (best balance). **Build custom** when no prebuilt fits, custom models/orchestration/data needed (most flexibility, highest cost).

---

### Card 37
**Q:** What is "model routing"?
**A:** Pattern where a small fast router model classifies requests and forwards each to the appropriate downstream model (small for FAQ, frontier for complex reasoning, code-specialized for code). Typical cost reduction: 40–70% without quality loss. Foundry has a built-in model router.

---

### Card 38
**Q:** Three top AI cost-optimization patterns?
**A:** **Model routing** (right-size per request); **prompt caching** (75–90% reduction on cached prefix tokens); **PTU for steady-state** workloads (predictable, cheaper at high steady volume).

---

### Card 39
**Q:** What's the unit of deployment for Copilot Studio + Power Platform customizations?
**A:** A **Power Platform solution** stored in Dataverse. Two types: **unmanaged** (for dev, editable) and **managed** (for prod, immutable). Bundles agents, flows, connectors, custom tables, environment variables.

---

### Card 40
**Q:** What are environment variables in Power Platform?
**A:** Per-environment configuration externalized from the solution package (connection strings, SharePoint URLs, API endpoints, feature flags). Same solution package deployed to dev/test/prod with environment-specific values.

---

### Card 41
**Q:** Pipelines for Power Platform — what is it?
**A:** Built-in tool for Power Platform solution deployment. Configures source-to-target environment promotion with approvals, environment variable resolution, automated checks. Successor / built-in version of the older ALM Accelerator.

---

### Card 42
**Q:** What does ALM versioning cover in an AI solution?
**A:** Code (services, functions); prompts (with eval results); **model versions pinned, never floating**; agent definitions (instructions, tools, knowledge); knowledge indexes; evaluation sets; connector configurations; Power Platform solutions; D365 configurations.

---

### Card 43
**Q:** ACL-aware grounding pattern in RAG?
**A:** Index documents with **ACL metadata** (allowed group/user IDs from Entra ID). At query time, get the user's group memberships from their token; pass as **security filters** on the search query. Azure AI Search supports natively. Model never sees content the user can't access. Without this, RAG leaks data.

---

### Card 44
**Q:** What is Microsoft Purview's role in AI governance?
**A:** **Data classification and lineage** across the data estate. Scans data sources (ADLS, SQL, Power BI/Fabric, SharePoint) classifying for PII and sensitivity. Maps lineage. Sensitivity labels propagate. Integrates with Foundry for training data and grounding source governance.

---

### Card 45
**Q:** What is the EU Data Boundary?
**A:** Microsoft commitment that EU customer data stays in EU regions. Covers Azure, M365, Dynamics. Affects Foundry deployments — for EU customers requiring residency, deploy Foundry + dependencies in EU regions; the boundary commitment applies. Critical for regulated EU workloads.

---

### Card 46
**Q:** Four layers of agent telemetry to monitor?
**A:** **Operational** (latency, error rate, throughput); **Quality** (LLM-as-judge, groundedness, citation accuracy, feedback); **Behavioral** (tool patterns, deflection vs escalation, topic distribution); **Safety** (content safety incidents, prompt shields, PII, groundedness failures); plus **Cost** (tokens, cache hit rate).

---

### Card 47
**Q:** What's the AB-100 expected pattern for generating agent test cases?
**A:** Use **Copilot in Power Platform / Copilot Studio to generate test cases** from agent specs and topics. Copilot suggests conversations covering happy paths, edge cases, adversarial inputs. Human reviews and curates. Scales test coverage faster than manual authoring. Supplement with domain-expert tests for critical paths.

---

### Card 48
**Q:** What's the architecture pattern for a Copilot Studio agent calling a Foundry agent for complex logic?
**A:** Hybrid pattern. **Copilot Studio** = user-facing surface, low-code orchestration, easy M365/Teams deployment. **Foundry agent** = custom Python tools, complex logic, custom models. Copilot Studio invokes Foundry via the agent connector / plugin pattern. Unified governance (identity, content safety, telemetry, ALM) crosses both.

---

### Card 49
**Q:** Power Platform AI hub — what's there?
**A:** **AI Builder** (prebuilt + custom AI models — form processing, sentiment, object detection); **AI prompts** (reusable Foundry-backed prompts); **agents** (Copilot Studio agents accessible from Power Apps/flows); **solution templates**. The maker's discovery surface for AI in Power Platform.

---

### Card 50
**Q:** When does a custom engine agent in M365 Copilot make sense?
**A:** When requirements exceed the M365 Copilot orchestrator: custom model selection (fine-tuned, non-OpenAI); custom orchestration (specific multi-step reasoning); proprietary safety/compliance filters; specialized response formats; strict data residency not met by M365 Copilot's defaults. Built in Foundry, surfaced in Teams/M365 via the Teams app + agent SDK.

---

# SECTION B — Expanded Practice Exam (50 questions)

**Format:** 50 scenario-style questions matching AB-100's blueprint distribution. Timing target: **100 minutes** (same as the real exam). Answer all questions first, *then* check explanations.

**Distribution:**
- Q1–Q14 — Plan domain (≈28%)
- Q15–Q28 — Design domain (≈28%)
- Q29–Q50 — Deploy domain (≈44%)

---

## PLAN DOMAIN (Q1–Q14)

---

### Q1
A multinational retailer wants AI features deployed initially to a pilot group in two countries (Germany and Brazil). The CFO requires a clear ROI demonstration before scaling. What's the recommended first-phase approach?

A. Deploy globally to all 50,000 employees and measure aggregate value.
B. Pick 2–3 high-value, measurable use cases per country; baseline current performance; run a controlled pilot; measure realized value with a clear capture-rate assumption; report quarterly.
C. Build a custom AI platform from scratch to maximize ROI.
D. Defer AI investment until competitors prove the value.

**Answer: B.** Standard CAF-aligned approach: targeted pilot with baselines, controlled measurement, realistic capture rates (typically 30–50% of theoretical), incremental scale based on demonstrated value.

---

### Q2
A customer's data scientist team built a custom NLP model. The CIO asks whether to deploy it via Foundry or Azure ML. Which factor most strongly favors Foundry?

A. The model is for batch scoring of tabular data with no LLM components.
B. The model is consumed by a Copilot Studio agent and exposed as a tool for a Foundry agent in another solution.
C. The model is a traditional decision tree with no agent integration.
D. The team prefers the Azure ML SDK.

**Answer: B.** Foundry shines when the model integrates into agentic solutions — agent service, MCP, unified RBAC, content safety, evaluation framework. For pure tabular ML with no agent context, Azure ML is equally valid.

---

### Q3
What's the CAF-aligned answer to "we have AI use cases proposed by five teams; how do we prioritize?"

A. First-come-first-served.
B. CoE intake with value (ROI projection) × feasibility (data, technical, organizational readiness) × risk × strategic alignment; high-value high-feasibility wins, others queued with prerequisites identified.
C. Engineering lead's preference.
D. Cost-only ranking.

**Answer: B.** The Plan phase explicitly addresses use-case prioritization via the CoE intake process.

---

### Q4
A customer asks "should we train our own LLM?" Architectural response?

A. Yes — proprietary data demands it.
B. Almost certainly no — start with prompt engineering and RAG; consider fine-tuning (LoRA) only after exhausting those; full pretraining of an LLM is justified for fewer than 1% of enterprises, primarily AI labs and a small number of hyperscale enterprises.
C. Train a small model for everything.
D. Only Microsoft can train models.

**Answer: B.** Hierarchy of effort: prompt engineering → RAG → fine-tuning (typically LoRA) → custom training (rare). Each step is 10–100× more expensive than the previous.

---

### Q5
A scenario: customer has unique fraud-detection patterns in their data, with 50,000 labeled examples and ongoing rapid model iteration. Build, buy, or extend?

A. Build — train a custom classification model in Foundry; deploy via managed compute; integrate with the agent solution as a tool.
B. Buy — use a prebuilt fraud agent.
C. Extend — only customize a prebuilt agent's prompts.
D. Don't use AI for fraud.

**Answer: A.** Domain-specific patterns + sufficient labeled data + ongoing iteration is exactly the build case. Prebuilt fraud agents wouldn't match the customer's unique patterns. Result wraps as a tool the agent solution invokes.

---

### Q6
A CFO of a mid-sized firm asks for ROI projection on rolling out M365 Copilot to 2,000 knowledge workers. Best approach for the architect?

A. Quote vendor case studies and assume the customer matches.
B. Identify 3–5 specific role-task combinations where Copilot can plausibly save time, estimate baseline time spent, project realistic capture (30–50% of theoretical max), monetize, contrast against license + governance costs; offer best/base/worst case sensitivity.
C. Promise 50% productivity gain — typical marketing number.
D. Refuse to estimate.

**Answer: B.** The architect's job is grounded modeling, not marketing numbers. Best/base/worst with sensitivity on the capture rate is the right artifact.

---

### Q7
A scenario describes designing a Microsoft AI Center of Excellence. Which capability is least appropriate to centralize?

A. Reusable prompt library and components.
B. Governance council and intake process.
C. Detailed business-logic implementation for each team's specific use case.
D. Security and Responsible AI review process.

**Answer: C.** The CoE centralizes platform, governance, reusables, enablement — not the specific business logic each domain team owns. Centralizing implementation creates bottlenecks and over-couples the CoE.

---

### Q8
What does TCO for an AI solution typically *under*-include?

A. License costs.
B. Compute costs.
C. Change management, ongoing evaluation/iteration, data engineering, governance overhead, and model-update fire drills.
D. Storage costs.

**Answer: C.** Licenses, compute, storage are visible. Change management and ongoing iteration are where projections most often break. Architects build these in.

---

### Q9
A customer's data is messy, inconsistently structured, and missing key fields. They want AI grounded in this data. Right next step?

A. Skip data prep; modern LLMs handle messy data.
B. Address data readiness first — cleansing, schema standardization, master data; AI quality is bounded by data quality.
C. Buy a different AI product.
D. Train a custom model on the messy data.

**Answer: B.** The Plan domain explicitly tests data readiness: "Review data for grounding, including accuracy, relevance, timeliness, cleanliness, and availability." AI on bad data fails predictably.

---

### Q10
Which platform-selection question best distinguishes between "build a Copilot Studio agent" and "build a Foundry agent"?

A. Does the customer prefer Microsoft?
B. Will makers maintain the agent (Copilot Studio) or will developers maintain it with custom Python/C# code and complex orchestration (Foundry)?
C. Is the budget large?
D. Is the agent for internal or external use?

**Answer: B.** The maker-vs-developer audience and the code complexity are the architectural decision drivers. Both platforms can serve internal or external users.

---

### Q11
A pharma customer wants an AI assistant for clinical researchers, grounded in regulatory documents (FDA, EMA), internal protocols, and trial databases. Regulatory makes hallucination unacceptable. Best architecture?

A. Foundry agent with RAG over regulatory + internal sources, hybrid search + semantic reranker, strict grounding instructions, **groundedness detection** in evaluation gating, citation required on every response, human review for novel queries.
B. M365 Copilot with no extensions.
C. A small local model with no retrieval.
D. Out-of-the-box chatbot.

**Answer: A.** Regulated, factual, hallucination-intolerant scenarios = Foundry + strong RAG + groundedness detection in evaluation + human-in-the-loop. The pattern repeats for any regulated industry.

---

### Q12
What's the model-routing pattern's primary business benefit?

A. Reduces total accuracy.
B. **Reduces total cost** by routing simple queries to cheap models and complex queries to expensive ones, often 40–70% cost reduction without quality loss.
C. Eliminates the need for evaluation.
D. Replaces the need for retrieval.

**Answer: B.** Cost. It's a fixture of cost-optimization patterns.

---

### Q13
A "build a custom small language model" use case might be justified when:

A. The team has unique reasoning patterns over a specific domain with millions of labeled examples and ongoing high-volume inference where token cost of a frontier model is prohibitive.
B. The customer wants a chatbot.
C. The customer wants email summarization.
D. The customer wants Outlook integration.

**Answer: A.** Custom small language models (typically fine-tuned/distilled from a larger model) are justified by **scale economics** — high volume + cost sensitivity + domain specialization. The everyday productivity use cases are answered by prebuilt or extended Copilot.

---

### Q14
A CoE asks "should we maintain a single prompt library or let teams write their own?" Best answer?

A. Decentralize fully.
B. Single source-controlled prompt library with version control, eval-gated changes, ownership, deprecation process, usage telemetry, contribution model from teams; CoE owns the library and curates.
C. Spreadsheet-based shared list.
D. No library — prompts inline in code.

**Answer: B.** Standard governance applied to prompts as a first-class artifact.

---

## DESIGN DOMAIN (Q15–Q28)

---

### Q15
A customer wants the customer-service Copilot agent to handle queries phrased in many different ways without enumerating every trigger phrase. Which orchestration mode?

A. Classic.
B. **Generative orchestration** — the LLM maps phrasing to the right topic; provide topic names and clear descriptions so the orchestrator routes correctly.
C. No orchestration.
D. Hybrid only.

**Answer: B.** Classic requires explicit trigger phrases per intent — burdensome at scale. Generative orchestration handles paraphrasing natively.

---

### Q16
A maker wants to embed a custom AI model in a Power Apps canvas app. Best pattern?

A. Train the model in AI Builder if it's a supported scenario (form processing, sentiment, object detection, custom classifiers); otherwise expose the model as an Azure Function and call it from a Power Automate flow invoked by the canvas; or wrap a Foundry-deployed model behind a Power Platform custom connector.
B. Embed Python code in the canvas.
C. Use Azure ML SDK in the canvas.
D. Hand-code the model.

**Answer: A.** AI Builder is the maker-native path; Functions or custom connectors expose anything else. The exam consistently favors makers-use-low-code patterns.

---

### Q17
What's the canonical Microsoft pattern for integrating a Foundry agent into Copilot Studio?

A. Inline Python in the Copilot Studio canvas.
B. Expose the Foundry agent endpoint as a tool/plugin invoked from Copilot Studio via an action; user-facing conversation stays in Copilot Studio; complex logic runs in Foundry; unified governance crosses both.
C. Disable Copilot Studio and use only Foundry.
D. Copy/paste between platforms.

**Answer: B.** The hybrid pattern named explicitly in the blueprint ("design AI solutions by using custom models in Microsoft Foundry" + "design agent extensibility in Copilot Studio").

---

### Q18
What is the correct way to constrain a Copilot Studio agent to a specific topic scope (e.g., "loans only, never small talk")?

A. Hope the LLM stays on topic.
B. Combination of: clear agent instructions scoping the domain, topic configurations covering in-scope intents, configured fallback topic that politely declines off-scope queries, content moderation, integration with Azure AI Content Safety for off-scope detection.
C. Hard-code a regex filter on input.
D. Disable content safety to allow flexibility.

**Answer: B.** Layered: instructions + topic design + fallback + content moderation. No single mechanism suffices for production-grade scope control.

---

### Q19
A scenario asks how to extend D365 Sales Copilot with a customer's proprietary sales-playbook content. Best path?

A. Hire developers to rewrite D365 Sales Copilot from scratch.
B. Add the sales playbook documents as a knowledge source for D365 Sales Copilot via Copilot Studio customizations; configure topics for playbook-driven coaching scenarios.
C. Build a separate Foundry agent with no D365 integration.
D. Stop using D365 Sales Copilot.

**Answer: B.** Extension via Copilot Studio is the supported customization surface; D365 Sales Copilot expects this pattern.

---

### Q20
A customer wants their agent to take an action — book a meeting, create a ticket, update a record. What's the difference between an "action" and "knowledge" in Copilot Studio?

A. They're the same.
B. **Knowledge** retrieves information from sources (read-only grounding). **Actions** invoke operations (Power Automate flows, connectors, custom skills, plugin actions, AI prompts, MCP actions) that often write data or call external systems.
C. Knowledge requires a license; actions don't.
D. Actions are deprecated.

**Answer: B.** Standard semantic split.

---

### Q21
What's the architectural pattern for a "field tech mobile copilot" in D365 Field Service?

A. Web-based chatbot only.
B. D365 Field Service Copilot (mobile-enabled) extended with knowledge sources (equipment manuals, work history, parts catalog) via Copilot Studio customizations; voice-enabled for hands-free; integration with Field Service work orders for context.
C. Custom mobile app, no integration.
D. M365 Copilot for Sales.

**Answer: B.** D365 Field Service Copilot is the right product; extensions add the customer's specific knowledge. Mobile + voice align with the field-tech surface.

---

### Q22
A customer wants three agents — HR, IT, and Finance — to be accessible to employees from one front door. Best pattern?

A. Three separate URLs employees must remember.
B. A single Copilot Studio orchestrator agent that delegates to HR, IT, Finance specialist agents via **multi-agent collaboration / agent-as-tool pattern**; user experiences a unified front door.
C. Mega-agent with all topics combined.
D. Each domain in a separate Power App.

**Answer: B.** Supervisor + specialist multi-agent pattern, expressed via agent collaboration in Copilot Studio.

---

### Q23
A scenario describes a Power Apps canvas app that should display AI-generated summaries dynamically based on the user's role and context. Architectural components?

A. Static UI; no AI.
B. **Code-first generative pages** for dynamic UI generation; AI prompts or a Foundry-deployed model for the summarization; grounding from Dataverse or knowledge sources; **agent feed** for displaying recommendations as cards; telemetry into Power Platform analytics; responsible AI guardrails (PII filtering, content safety).
C. Plain text only.
D. Pre-rendered static pages.

**Answer: B.** Code-first generative pages + agent feed is the named blueprint pattern for AI-driven Power Apps experiences.

---

### Q24
A bank wants Copilot Studio to handle loan applications. The agent collects info and submits to an internal API. Applications above $50k require credit approval. Design?

A. The agent decides autonomously without human review.
B. **Agent flow** that collects info → calls credit-check API → if amount < $50k, submits directly; if ≥ $50k, invokes an **Approvals action** (Power Platform Approvals or Teams approval) to the credit officer, waits, then proceeds. Full audit log.
C. Manual processing only.
D. Skip the threshold check.

**Answer: B.** Human-in-the-loop on threshold-triggered decisions = the textbook AB-100 pattern.

---

### Q25
What's the right way to design a Copilot Studio agent that needs to call multiple external systems (Salesforce, ServiceNow, SAP) for different intents?

A. Hard-code REST calls in topic dialog.
B. Use Power Platform connectors (one per external system); invoke from topic actions; each connection reference scoped via environment variables; service-principal authentication where possible; least-privilege scopes.
C. Use a single shared admin credential.
D. Have the user paste data manually.

**Answer: B.** Connectors are the supported integration surface; per-environment connection references handle ALM; least-privilege auth aligns with security best practice.

---

### Q26
A customer designs an autonomous agent that watches a sales data lake and proactively recommends actions. Which platform combination is most appropriate?

A. Power BI only.
B. Foundry agent for autonomous logic (using model + tools to query the data lake); event-driven trigger (Event Grid / scheduled timer); recommendations posted to Microsoft Teams / surface in D365 Sales via Copilot Studio agent collaboration; human-in-the-loop on actions; ALM aligned across Foundry + Copilot Studio + D365.
C. Excel macros.
D. Manual reports.

**Answer: B.** Autonomous agentic logic in Foundry; event triggers; surfacing in user tools; HITL governance. The blueprint's named pattern.

---

### Q27
Which Copilot Studio capability allows authoring multi-step flows with conditions, parallel execution, and human approvals?

A. Topics.
B. Knowledge sources.
C. **Agent flows** (the 2026 construct for multi-step, often long-running, agent-driven workflows with branching, parallel execution, and human approvals).
D. Prompt actions.

**Answer: C.** Agent flows are the implementation surface for autonomous-style flows in Copilot Studio.

---

### Q28
When designing prompt actions, which best practice applies?

A. One mega-prompt for everything.
B. Treat each prompt as a reusable, named unit with a clear input/output contract; store in a CoE-governed prompt library; version-control with eval results; reuse across topics and flows; refactor when used in more than one place.
C. Inline prompts only.
D. Random prompts per topic.

**Answer: B.** Reusable, governed, evaluated, version-controlled — standard engineering hygiene applied to prompts.

---

## DEPLOY DOMAIN (Q29–Q50)

---

### Q29
A Copilot Studio agent works in dev but fails in production after solution import with "connection error." Most likely root cause?

A. License issue.
B. Wrong region.
C. **Connection references / environment variables not configured** for the production environment after import; per-environment connections must be set manually or via pipeline automation.
D. Model deprecated.

**Answer: C.** The most common ALM mistake. Solutions move; per-environment configuration must be set per environment.

---

### Q30
A Foundry agent's `gpt-4o-2024-08-06` deployment is being deprecated. Microsoft offers `gpt-4o-2025-01-15` as successor. Correct migration sequence?

A. Switch immediately on prod.
B. Wait until forced.
C. **Deploy new version in dev → run evaluation suite → compare metrics → if no regression, promote to test with canary → monitor → promote to prod via blue/green or canary → keep old deployment available for 14–30 days for rollback → decommission old.**
D. Train a custom model.

**Answer: C.** Standard ALM lifecycle for model transitions. Plan in advance; gate on evaluation; canary; maintain rollback path.

---

### Q31
What's the right way to monitor whether an agent's responses are grounded in source content over time?

A. Manual sampling only.
B. **Groundedness detection** running continuously on a sample of production responses + offline evaluation on a versioned eval set + dashboards trending groundedness scores by week + alerts on regression beyond a threshold + failures sampled into eval set for next iteration.
C. User feedback only.
D. Hope for the best.

**Answer: B.** Layered evaluation — continuous + scheduled + alerting + feedback loop.

---

### Q32
A scenario: an autonomous agent processed 10× its normal volume one night due to a corrupted upstream feed, burning budget and creating bad records. What governance would have prevented this?

A. Disable the agent permanently.
B. Layered: rate limits / throughput caps; anomaly detection on input volume (alert + pause if deviates from baseline); budget alerts triggering pause; human-in-the-loop on high-value or unusual cases; pre-flight validation of upstream data quality; kill switch; post-run reconciliation alerts.
C. No prevention possible.
D. Manual review of every action.

**Answer: B.** Defense in depth for autonomous agents. Maps to Reliability and Safety + Accountability principles.

---

### Q33
What is the recommended approach to test cases for a complex multi-topic Copilot Studio agent?

A. Manual hand-authored only.
B. **Mixed approach: Copilot-generated test cases from agent specs and topics covering happy paths, edge cases, adversarial inputs — human reviews and curates; supplement with domain-expert-authored tests for critical paths; safety tests (jailbreaks, indirect injection); integration tests; regression-gated CI/CD.**
C. No tests; rely on production monitoring.
D. Tests in production only.

**Answer: B.** The blueprint explicitly names "build the strategy for creating test cases by using Copilot." Mixed approach with human review is the recommended practice.

---

### Q34
An autonomous agent makes irreversible decisions (e.g., refunds, account changes). The customer is regulated. What's the minimum audit posture?

A. Standard logging.
B. **Comprehensive layered audit:** Foundry tracing (per-request decisions, tool calls, model version); Application Insights (operational); Microsoft Purview (data lineage, classification); Azure Activity Log (resource changes); Microsoft Sentinel for aggregation; per-decision artifacts in immutable storage (Azure Storage with immutability policy) with retention per regulatory requirement (often 7+ years); access scoped to compliance roles.
C. Daily summary report.
D. No audit needed.

**Answer: B.** Regulated + autonomous + irreversible = highest audit posture. The blueprint specifically tests audit trail design.

---

### Q35
An EU financial services customer is deploying a Foundry agent. What residency design points apply?

A. Region doesn't matter.
B. **Foundry resource + dependent services (AI Search, storage, Application Insights) deployed in an EU region; verify model availability in that region; EU Data Boundary commitment applies; customer-managed keys for storage; private endpoints; documented data-flow diagram for compliance; audit retention per regulatory requirement; verify OpenAI processing stays within EU under EU Data Boundary; RBAC with least privilege; CoE responsible AI sign-off before go-live.**
C. Deploy in US West.
D. On-premises only.

**Answer: B.** Comprehensive regulated EU deployment. Each point matters; missing any creates risk.

---

### Q36
A scenario: 30% of conversations in a Copilot Studio agent end in fallback. Diagnostic approach?

A. Increase fallback creativity.
B. **Cluster fallback utterances; identify common intents not handled; top clusters → new topics or expanded trigger phrases; knowledge gaps → add sources; consider generative orchestration if classic is the bottleneck; A/B test improvements; measure fallback rate trend; weekly review until sustained reduction.**
C. Hide the fallback metric.
D. Disable fallback.

**Answer: B.** Fallback rate is the leading indicator of agent fit. Diagnose by clustering and addressing causes.

---

### Q37
What's the right ALM unit for moving a Copilot Studio agent + Power Automate flows + custom connector across environments?

A. Manual export of each piece.
B. **A Power Platform solution (managed solution for prod) bundling all components, exported from dev, imported via Pipelines for Power Platform or DevOps pipeline; environment variables resolve per-environment config; connection references handle credentials.**
C. ZIP file.
D. Copy/paste.

**Answer: B.** Solutions are the unit. Pipelines for Power Platform automates the promotion.

---

### Q38
A custom AI model is consumed by a Copilot Studio agent and by a Power Automate flow. Best deployment pattern?

A. Embed the model code directly in both consumers.
B. **Deploy the model as a versioned endpoint in Foundry (or Azure ML); consumers reference a stable endpoint name; backward-compatible changes transparent; breaking changes via versioned endpoints with deprecation; model registry tracks versions, training data, metrics; promotion through stages (Dev → Staging → Production) gated by quality checks; rollback by routing endpoint to previous version.**
C. Email the model weights to consumers.
D. Have each consumer maintain its own copy.

**Answer: B.** Single managed model deployment, versioned, consumed via endpoint by multiple clients. Standard MLOps applied.

---

### Q39
A scenario: production telemetry shows that some users' Foundry agent responses include claims that aren't supported by retrieved context. Diagnostic and fix?

A. Switch to a different LLM and hope.
B. Enable **groundedness detection** in production telemetry to quantify; track per-segment; investigate failure clusters (retrieval missed relevant chunks? Reranker buried them? Prompt didn't enforce grounding strictly enough? Model going outside context?); fix at the failing layer (improve retrieval, add reranker, tighten grounding instructions, switch to a stronger model on hard segments); add adversarial cases to eval set; gate future releases.
C. Disable retrieval.
D. Tell users not to trust it.

**Answer: B.** Pinpoint which RAG stage is broken via groundedness signals; fix the specific layer; lock the improvement with eval.

---

### Q40
A Copilot Studio voice agent in D365 Contact Center needs to handle PII (names, account numbers) spoken by callers. What governance?

A. Record everything in clear; trust the contact center.
B. PII detection on transcripts; redaction before logging; encryption at rest and in transit; access scoping for transcripts; consent for recording communicated to caller; voice agent identifies itself as AI; retention policies per regulatory requirement; audit of access to recordings; data residency per region.
C. No PII handling; users should redact themselves.
D. Disable voice.

**Answer: B.** Standard PII handling extended to voice. The exam tests this directly under privacy/security and accountability.

---

### Q41
A scenario describes a multi-agent system where intermediate agent outputs are not visible for audit. What's the gap?

A. None — agents are autonomous.
B. **Insufficient tracing.** All inter-agent communications, tool calls, intermediate decisions must be logged with correlated trace IDs (Foundry tracing + Application Insights + Sentinel). Each agent's input, output, and reasoning visible. Without this, debugging is impossible and audit fails. Architects must require end-to-end tracing for multi-agent systems.
C. Acceptable for performance reasons.
D. Add tracing only on errors.

**Answer: B.** Multi-agent systems demand the strongest tracing posture. Tracing is not optional.

---

### Q42
A customer's Power Platform admin asks how to enforce that all new Copilot Studio agents pass responsible-AI review before publishing. Best approach?

A. Hope makers comply.
B. **Environment strategy with policies:** development environments are isolated; agents must move via Pipelines for Power Platform (or DevOps) to a staging environment requiring CoE-approved sign-off; only then promoted to prod; review checklist includes responsible AI principles assessment, content safety configuration, audit, security review; documented approval workflow; environment policies prevent makers from publishing direct to prod environments.
C. After-the-fact audit only.
D. No enforcement.

**Answer: B.** Environment strategy + ALM-gated approval = enforcement mechanism. The exam consistently favors policy-as-code over manual processes.

---

### Q43
A scenario asks the appropriate test scope for an autonomous agent before production. Best answer?

A. Unit tests only.
B. **Comprehensive: unit tests for tools and deterministic logic; prompt regression tests on eval set; synthetic conversation tests; safety tests (jailbreaks, indirect injection, off-topic); end-to-end scenario tests; performance tests; chaos tests (tool failure, model outage); shadow mode in prod (capture actions, don't execute); limited canary with strict thresholds; full prod only after staged confidence buildup with kill switch and budget caps.**
C. Manual testing only.
D. Production traffic as testing.

**Answer: B.** Autonomous agents demand the strongest test pyramid plus staged production rollout.

---

### Q44
A scenario: a Foundry-deployed fine-tuned model has been in production for 4 months. Performance has slowly degraded. Most likely cause?

A. The model is broken.
B. **Concept drift** — the production input distribution has shifted away from what the model was fine-tuned on. Detect via input feature monitoring (PSI, KS test); compare current eval-set scores to historical; sample failing cases; consider retraining with recent labeled data; A/B against the original; promote if better; otherwise root-cause further.
C. Microsoft updated something silently.
D. Random.

**Answer: B.** Slow degradation over time + stable model + stable code = drift. The architect's diagnostic approach is the answer.

---

### Q45
A scenario: a healthcare customer's autonomous agent triages incoming patient inquiries and routes urgent ones to clinical staff. What's the minimum responsible-AI posture before launch?

A. Standard testing.
B. **Highest posture:** clinical scope validated by clinicians; fairness assessment across patient demographics; explicit AI disclosure to patients; conservative threshold for "non-urgent" classification (false negatives have clinical risk); human review on borderline cases; audit trail of every triage decision with reasoning artifacts; rollback to all-human-triage on degradation; CoE + clinical-leadership sign-off; ongoing monitoring of equity across demographic segments; regulatory compliance (HIPAA, applicable state/regional rules); incident response plan; transparent communication of limits.
C. Roll out fast and iterate.
D. Defer indefinitely.

**Answer: B.** Healthcare + autonomous + safety-critical = the highest responsible-AI standard. Map to all six principles.

---

### Q46
A customer asks how to enforce that prompts deployed to production have been evaluated. Mechanism?

A. Trust developers.
B. **CI/CD pipeline gates:** every prompt change in source control triggers evaluation against the eval set; promotion to prod blocked unless metrics meet thresholds with no regression on any segment; logged sign-off; production telemetry confirms behavior matches eval-set behavior; rollback if mismatch; weekly review.
C. Periodic manual audit.
D. None needed.

**Answer: B.** Eval gates in CI/CD = enforcement. Trust developers (A) and manual audit (C) miss regressions.

---

### Q47
What's the architectural rationale for separate Power Platform environments per stage (Dev/Test/Prod)?

A. License optimization.
B. **Isolation:** dev changes don't affect prod; testing happens against representative-but-not-prod data; promotion is controlled; rollback is via environment-level operations; blast radius of failure is bounded; permissions can be scoped tighter in prod; regulatory and compliance posture differs (audit, retention, encryption); ALM artifacts (solutions) versioned across environments.
C. Vendor recommendation only.
D. No real reason.

**Answer: B.** Environment isolation is foundational ALM. The exam tests this often.

---

### Q48
A scenario: the customer's CFO asks "how do I know our AI investment is delivering value?" Architect's response?

A. "Trust me."
B. **Define KPIs upfront tied to business outcomes:** adoption (active users, retention); quality (satisfaction, error rate, task completion); efficiency (time-to-task, automation/deflection rate); business outcomes (conversion lift, resolution time, forecast accuracy); cost trajectory; responsible AI posture (incidents, audit findings). Baseline each KPI before launch; instrument telemetry; monthly review with leadership; quarterly business reviews tying outcomes back to spend.
C. Show usage charts only.
D. Wait until year-end.

**Answer: B.** Value demonstration requires upfront baselines, instrumented KPIs, ongoing reporting. The architect's accountability.

---

### Q49
A scenario: the team needs to update grounding documents weekly. How do they manage knowledge-source freshness in a Foundry agent without disrupting users?

A. Manual re-index.
B. **Automated weekly indexing pipeline:** new documents detected → embedded → indexed in a parallel (versioned) index → tested with regression queries → cutover atomically (Azure AI Search alias swap or index alias pattern); old version retained for rollback; per-document permissions re-applied; lineage tracked (which index version contains which doc version); user-facing impact zero if cutover is atomic.
C. Stop using grounding.
D. Add documents inline to prompts.

**Answer: B.** Standard data ALM for indexes — versioned, validated, atomic cutover.

---

### Q50
The exam asks what's the architect's role in ongoing AI program success. Best answer?

A. Build the first version and hand off.
B. **End-to-end accountability across the lifecycle:** envision the strategy (CAF Strategy); plan and prioritize use cases (CoE intake, ROI modeling); design responsibly (platform selection, responsible AI, security); guide implementation (extensibility, ALM); monitor and tune (telemetry, evaluation, drift response); govern (audit, compliance, retirement); champion adoption; iterate continuously based on data; tie back to business outcomes.
C. Technical design only.
D. Documentation only.

**Answer: B.** The blueprint's "key responsibilities" section explicitly names architecture, design, implementation guidance, adoption championing, ALM strategy, environment strategy, and ongoing leadership. End-to-end is the architect's role.

---

## How to use this practice exam

1. **First pass:** answer all 50 in 100 minutes without checking explanations. Mark uncertain ones.
2. **Score yourself.** Target ≥70% (35/50) for a confident pass. Below 60% (30/50) → review weak domains hard before sitting the real exam.
3. **Review every wrong answer and every uncertain answer.** The explanations encode the patterns the real exam tests.
4. **Take a second pass on weak-domain questions** the next day. Spaced repetition locks the patterns in.

---

# SECTION C — Exam-Morning Cram Sheet

**Read once, 20–30 minutes before sitting the exam. Nothing new — just the decision tables you must have at instant recall.**

---

## Platform Selection Decision Table

| Need | Pick |
|---|---|
| Low-code agent, makers, M365/Teams deployment | **Copilot Studio** |
| Custom code, multi-agent, custom tools, model catalog | **Microsoft Foundry** |
| Augment M365 apps (Outlook/Word/Teams/PowerPoint) with internal data | **M365 Copilot agent or plugin/Graph connector** |
| AI inside Dynamics 365 workflows | **D365 Copilot features + Copilot Studio extensions** |
| Citizen-developer AI in Power Apps/flows | **Power Platform AI hub (AI Builder / AI prompts)** |
| Voice agent for contact center | **Copilot Studio + D365 Contact Center** |
| Edge/offline inference, privacy-critical | **Foundry Local** |
| RAG over regulated, ACL-controlled corpus | **Foundry + Azure AI Search with security filters** |
| Sales rep productivity in Outlook | **M365 Copilot for Sales** |
| Customer-service agent productivity | **M365 Copilot for Service / D365 Customer Service Copilot** |

---

## Three Agent Paradigms

| Paradigm | Pattern | Platform fit |
|---|---|---|
| **Prompt-and-response** | Short Q&A | Copilot Studio / M365 Copilot agent |
| **Task** | Multi-step bounded task | Copilot Studio + agent flow / Foundry agent |
| **Autonomous** | Event-driven, long-running | **Foundry agent** (preferred) or Copilot Studio agent flow with triggers; **strong governance, HITL on irreversible actions** |

---

## Build vs Buy vs Extend

| Approach | When |
|---|---|
| **Buy / configure** (prebuilt) | Prebuilt fits with light customization. Fastest TTV. |
| **Extend** (customize prebuilt) | Prebuilt is 70%+ fit; gap within extension surfaces. **Best balance.** |
| **Build custom** | No prebuilt fits; custom models/orchestration/data needed. Highest cost/time. |

**When in doubt: prefer the most prebuilt option that still meets requirements.**

---

## Six Responsible AI Principles

1. **Fairness** — no discrimination
2. **Reliability and Safety** — robust under unexpected conditions
3. **Privacy and Security** — protect data, defend against attacks
4. **Inclusiveness** — empower everyone, accessibility
5. **Transparency** — explainable
6. **Accountability** — humans responsible, oversight

**Underpinned by:** human oversight.

---

## CAF for AI — Phases

**Strategy → Plan → Ready → Adopt → Govern / Manage / Secure** (ongoing).

- **Strategy:** business outcomes, vision, success metrics.
- **Plan:** assess readiness; prioritize use cases via CoE intake.
- **Ready:** platform, identity, governance baseline.
- **Adopt:** pilot → scale → embed in processes.
- **Govern + Manage + Secure:** continuous operations.

---

## What Versioning Covers (ALM)

Code, **prompts (with eval results)**, **model versions (pinned, never floating)**, agent definitions, knowledge indexes, evaluation sets, connector configs, Power Platform solutions, D365 configurations.

---

## MCP vs A2A

- **MCP:** agent-to-tool. JSON-RPC over stdio/HTTP. Tools, resources, prompts.
- **A2A:** agent-to-agent. Cross-vendor agent collaboration.

---

## Cost Optimization Patterns

**Model routing → Prompt caching → Output length limits → Prompt compression → Retrieval optimization → PTU for steady → Small models for routine → Foundry Local for edge.**

---

## Agent Security Defenses (in order)

1. **Content safety on input and output** (categories + severity)
2. **Prompt shields** (direct + indirect injection detection)
3. **Groundedness detection** (hallucination guard)
4. **Least-privilege tools** (narrow scope, action allowlists)
5. **Human-in-the-loop** on irreversible / high-stakes actions
6. **ACL-aware retrieval** (security filters on search)
7. **Comprehensive audit** (Foundry tracing + Purview + Sentinel)

---

## Prompt Injection Quick-Ref

- **Direct:** user input manipulates model. Defense: structured prompts + prompt shields.
- **Indirect:** retrieved content carries malicious instructions. **Bigger risk in agentic systems.** Defense: treat retrieved content as untrusted data + prompt shields (indirect-attack mode) + tool restrictions + HITL.

---

## ALM Deployment Patterns

- **Power Platform / Copilot Studio:** solutions + environment variables + Pipelines for Power Platform (or DevOps + Power Platform CLI).
- **Foundry agents:** source-controlled config + per-environment projects + CI/CD with eval gates + canary → prod.
- **Custom AI models:** model registry + stage transitions gated by quality checks + canary deployments.
- **Knowledge indexes:** versioned + parallel build + atomic cutover (alias swap).

---

## Common Exam Trap Patterns

**These answers are usually wrong:**

- "Build custom" when a prebuilt fits.
- "Use only one platform" for a cross-app/cross-surface scenario.
- "Trust the LLM to stay safe."
- Skipping human-in-the-loop on irreversible actions.
- Skipping evaluation gates in CI/CD.
- PII in URLs, logs, or third-party APIs.
- Ignoring data residency in regulated scenarios.
- Floating model tags in production.
- Bidirectional permissions trust ("any user with access to the bot can read any indexed doc").
- "Just retrain the model" without diagnosing the actual layer that's broken.

**These answers are usually right:**

- The answer with the most prebuilt + least custom that still meets requirements.
- The answer with explicit governance, audit, and human oversight on sensitive actions.
- The answer that uses platform-recommended patterns (Copilot Studio for makers; Foundry for developers; M365 Copilot for productivity surfaces; D365 Copilot for CRM workflows).
- The answer with layered defense (multiple security/safety mechanisms, not one).
- The answer that includes evaluation and monitoring.
- The answer with proper ALM (environments, solutions, environment variables).

---

## Pacing for the 100-Minute Exam

- ~40–60 questions (estimate; can vary).
- ~1.5–2 minutes per question average.
- **First 60 minutes:** answer all confident questions; flag uncertain ones.
- **Next 30 minutes:** revisit flagged questions, applying decision tables above.
- **Last 10 minutes:** review flagged; finalize. Don't leave anything blank — no penalty for wrong vs blank.

---

## Mindset Reminders

- The exam tests **architectural judgment**, not configuration trivia.
- When two answers seem close, pick the one with **stronger governance, least privilege, HITL on irreversible actions, and platform-recommended patterns.**
- Trust the prep. You walked the blueprint, drilled scenarios, and reviewed the cram sheet. Trust the muscle memory.
- **Read scenarios carefully** — the answer often hinges on one detail (regulated industry, voice channel, makers vs developers, autonomous, irreversible).
- Flag and move on — never burn 10 minutes on one question.

---

**Final reminder:** AI-102 already gave you the certification credential mechanism. Pass AB-100 and you become **Microsoft Certified: Agentic AI Business Solutions Architect** the moment results post. Go pass it.
