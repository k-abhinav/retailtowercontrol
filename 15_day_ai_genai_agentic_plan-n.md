# 15-Day Learning Plan: AI / GenAI / Agentic AI

**Audience:** Engineer with 12 years of Azure Cloud and Data experience
**Goal:** Achieve interview-ready fluency in modern AI, Generative AI, and Agentic AI — leveraging existing Azure depth
**Daily commitment:** ~3–4 hours (1 hour theory, 1.5 hours hands-on, 30–60 min Q&A review)

---

## How This Plan Is Built

Because you already have deep cloud + data foundations, this plan **skips** classical ML basics, infrastructure-as-code mechanics, identity/networking, and data lake patterns — you know those. It **emphasizes**:

1. The mathematics and architecture of LLMs (just enough to reason about behavior).
2. Hands-on with Azure OpenAI, Microsoft Foundry, and the broader Azure AI stack.
3. RAG patterns at production scale.
4. Agentic AI — the dominant 2026 paradigm — using LangGraph and the new Microsoft Agent Framework.
5. MCP (Model Context Protocol), which has become the industry standard for tool integration.
6. LLMOps, evaluation, safety, and cost management.

Each day includes: **Topics → Concept explanations → Interview Q&A → Official references → Hands-on task.**

---

## Day-by-Day Overview

| Day | Theme | Outcome |
|---|---|---|
| 1 | Neural networks & transformers refresher | Understand *why* LLMs work |
| 2 | LLM architecture & families | Pick the right model for any job |
| 3 | Tokens, context, generation parameters | Reason about cost, latency, quality |
| 4 | Prompt engineering, systematic | Production-grade prompts |
| 5 | LLM APIs, function calling, structured outputs | Call Azure OpenAI from code confidently |
| 6 | Evaluation of LLM applications | Measure what matters |
| 7 | Embeddings & vector databases | Power RAG retrieval |
| 8 | RAG architecture end-to-end | Build a production RAG |
| 9 | Advanced RAG patterns | Reranking, hybrid, agentic RAG |
| 10 | Agentic AI fundamentals | ReAct, planning, tool use |
| 11 | Multi-agent systems | LangGraph + Microsoft Agent Framework |
| 12 | MCP (Model Context Protocol) | The 2026 integration standard |
| 13 | Microsoft Foundry deep dive | Azure-native GenAI platform |
| 14 | LLMOps, safety, monitoring | Run AI in production |
| 15 | Fine-tuning, multimodal, capstone | Round out and consolidate |

---

# Day 1 — Neural Networks & Transformers Refresher

## Topics
- Neural networks at a glance (skipped if you already know)
- Word embeddings (Word2Vec → contextual embeddings)
- Attention mechanism (the core innovation)
- Transformer architecture (encoder, decoder, encoder-decoder)
- Self-attention vs cross-attention
- Why transformers replaced RNNs/LSTMs

## Concept Explanations

**Why this day matters:** You don't need to derive backprop, but to debug an LLM application — "why is it ignoring this part of the prompt?", "why is retrieval poor?", "why does the model loop?" — you need a mental model of attention and context.

**Attention** is a weighted sum: for each token, the model decides how much to "pay attention to" every other token in the context. The weights are learned. This is what lets a model link "it" in a sentence back to the noun several words earlier.

**Self-attention** does this within one sequence (a paragraph attending to itself). **Cross-attention** does it between two sequences (e.g., decoder attending to encoder output in translation). LLMs like GPT use decoder-only self-attention.

**Three transformer flavors:**
- **Encoder-only** (BERT) — good at understanding (classification, embeddings). Bidirectional context.
- **Decoder-only** (GPT, Llama, Claude) — good at generation. Causal (left-to-right) attention.
- **Encoder-decoder** (T5, BART) — translation, summarization with a "read then write" pattern.

Almost all modern chat LLMs are decoder-only.

**Positional encoding** — transformers are inherently order-blind, so position is injected via sinusoidal or learned (now usually RoPE — Rotary Position Embedding) signals.

**Why this scales:** attention is parallelizable across the sequence (unlike RNNs that must process sequentially), so transformers exploit GPUs spectacularly well.

## Interview Q&A

**Q1. What problem does the attention mechanism solve that RNNs couldn't?**
A: RNNs process sequences token by token, accumulating state in a fixed-size hidden vector. This creates two problems: long-range dependencies are lost (information from token 1 is diluted by token 500), and training can't parallelize across the sequence. Attention lets every token directly access every other token via learned weights — no information bottleneck, and all positions compute in parallel. The cost is O(n²) memory in sequence length, which is the constraint modern long-context techniques work around.

**Q2. Encoder-only vs decoder-only vs encoder-decoder — when do you choose each?**
A: Encoder-only (BERT-family) for embeddings, classification, NER — anywhere you need a representation, not generation. Decoder-only (GPT, Claude, Llama) for chat, generation, completion — these dominate today. Encoder-decoder (T5, BART) for tasks with a clear "input transform → output" pattern like translation or summarization, though decoder-only models now handle these well enough that encoder-decoder is less common in new systems.

**Q3. What is RoPE and why has it replaced learned positional embeddings?**
A: Rotary Position Embedding encodes positions by rotating query/key vectors in 2D subspaces by an angle proportional to position. Two practical wins: it generalizes to longer sequences than seen in training (extrapolation), and the relative position between two tokens is preserved naturally — important for reasoning. Most modern LLMs (Llama 2/3, Mistral, Qwen) use RoPE or variants.

**Q4. Why is context length expensive?**
A: Self-attention is O(n²) in both compute and memory because every token attends to every other token. Doubling context quadruples cost. Techniques to mitigate: sparse attention, sliding window attention (Mistral), FlashAttention (kernel-level optimization without algorithmic change), and KV-cache compression. Long-context models like Claude (200k+) and Gemini (1M+) combine multiple techniques.

**Q5. What is a KV cache and why does it matter for inference?**
A: When generating token N+1, the model needs attention over tokens 1..N. The key and value vectors for those tokens don't change as you generate more tokens — so caching them avoids recomputation. The KV cache is the dominant memory consumer at inference and limits batch size. Techniques like grouped-query attention (GQA) shrink it by sharing K and V across query heads.

**Q6. What is multi-head attention?**
A: Instead of one attention computation, run several in parallel with different learned projections, then concatenate. Each head can specialize — one might track syntactic structure, another might track entity references. It's analogous to ensembling within a single layer.

## Official References
- *Attention Is All You Need* — original transformer paper: https://arxiv.org/abs/1706.03762
- The Illustrated Transformer (Jay Alammar): https://jalammar.github.io/illustrated-transformer/
- Hugging Face NLP Course (Chapters 1–4): https://huggingface.co/learn/nlp-course/chapter1/1
- 3Blue1Brown — Neural networks playlist: https://www.3blue1brown.com/topics/neural-networks
- RoPE paper: https://arxiv.org/abs/2104.09864

## Hands-on
Spin up a Colab notebook, load a small open model with `transformers`, generate a few completions, and inspect tokenization. Don't worry about quality — focus on the mechanics of input/output.

---

# Day 2 — LLM Architecture & Model Families

## Topics
- Pretraining vs instruction tuning vs RLHF vs DPO
- Model families and what they're good at (GPT-4/4o/4.1, Claude, Llama, Mistral, Phi, Gemini)
- Open-source vs closed-source
- Mixture of Experts (MoE)
- Reasoning models (o-series, Claude 4 with extended thinking, DeepSeek-R1)
- Parameter counts vs capability — when bigger isn't better

## Concept Explanations

**Pretraining** — model is trained to predict the next token on a massive text corpus (trillions of tokens). This produces a "base model" that can complete text but isn't conversational.

**Instruction tuning (SFT)** — fine-tune the base model on (instruction, response) pairs so it follows directions in chat form.

**Preference tuning (RLHF, DPO)** — further align the model with human preferences: rank multiple responses, train a reward model, optimize the LLM to maximize predicted reward. Direct Preference Optimization (DPO) is a simpler alternative that skips the explicit reward model. **Constitutional AI** (Anthropic) is an AI-assisted variant.

**MoE (Mixture of Experts)** — each forward pass routes tokens through a subset of "expert" sub-networks rather than the whole model. Effective parameters per token are much smaller than total parameters, giving better quality per FLOP. GPT-4, Mixtral, DeepSeek-V3 are MoE.

**Reasoning models** — explicitly trained to "think before answering" by generating chain-of-thought internally before producing the final response. OpenAI's o-series, Claude's extended thinking, DeepSeek-R1, and Gemini Thinking are examples. Trade off latency and cost for substantially better performance on complex reasoning, math, and coding tasks.

**Model family picker (current as of 2026):**
- **Frontier reasoning** — OpenAI o-series, Claude with thinking, Gemini 2.5 Pro Thinking, DeepSeek-R1
- **Frontier general** — GPT-4o/4.1, Claude (Opus/Sonnet), Gemini 2.5 Pro
- **Fast, cheap, capable** — GPT-4o mini, Claude Haiku, Gemini Flash, Llama 3 70B
- **Edge / on-device** — Phi-3/4, Llama 3 8B, Mistral 7B
- **Embeddings** — text-embedding-3-large, Cohere embed-v3, BGE, E5
- **Open-weights frontier** — Llama 3 405B, DeepSeek-V3, Qwen 2.5 72B

## Interview Q&A

**Q1. Walk through what happens from a raw text corpus to a deployed chat model.**
A: (1) Pretraining — base model learns language and world knowledge by next-token prediction on trillions of tokens. (2) SFT (supervised fine-tuning) — train on curated (prompt, response) pairs for desired behaviors and formats. (3) Preference alignment — RLHF or DPO using human or AI preference data to steer toward helpful, harmless, honest responses. Optionally (4) reasoning training — RL on verifiable tasks (math, code) to develop chain-of-thought. Finally, deployment with safety filters, monitoring, and possibly retrieval or tools on top.

**Q2. RLHF vs DPO — what's the practical difference?**
A: RLHF trains a separate reward model from preference data, then uses RL (PPO) to optimize the LLM. It's powerful but complex — three models in flight (policy, reference, reward), unstable training, reward hacking risk. DPO reframes the same objective as a single classification loss on the LLM itself — no separate reward model, more stable, easier to implement. Most teams now prefer DPO or its variants (IPO, KTO) unless they need RLHF's flexibility for complex reward signals.

**Q3. Why MoE? What's the trade-off?**
A: MoE gives more capability per FLOP — a 600B-parameter MoE with 37B active per token (DeepSeek-V3) can outperform a 70B dense model at similar inference cost. The cost is memory: all experts must be loaded, even though only some run per token. So MoE shines when you have lots of memory but want high quality at moderate compute. Dense models are simpler to serve and quantize.

**Q4. When should you reach for a reasoning model?**
A: Tasks where the model genuinely needs to plan or chain logic — competitive coding, complex math, multi-step debugging, scientific reasoning, agentic tool use. Avoid them for simple lookups, extraction, classification, and chat — they're slower and more expensive, and the extra thinking doesn't add value. A common pattern: use a fast model as a router that decides whether to escalate to a reasoning model.

**Q5. How do you decide between an open-source and a closed-source model?**
A: Closed (Azure OpenAI, Anthropic, Google) when you want top capability, no infrastructure to manage, regulated availability via Azure, and you accept per-token pricing. Open (Llama, Mistral, DeepSeek hosted on Azure Foundry or self-hosted) when you need data residency control, custom fine-tuning, cost predictability at high volume, or on-prem deployment. Many teams use both — closed for the hardest tasks, open or fine-tuned smaller models for high-volume routine work.

**Q6. What is grounded vs ungrounded knowledge in an LLM?**
A: Ungrounded knowledge is what the model learned during pretraining — useful but frozen at training cutoff, prone to hallucination on specifics. Grounded knowledge is what's supplied at inference via retrieval, tools, or context. Production systems lean heavily on grounding for anything requiring accuracy, recency, or auditability — the model becomes a reasoning + composition engine over reliable facts.

**Q7. How are smaller models (Phi-4, Llama 3 8B) closing the gap with frontier models?**
A: Three drivers: (1) **better data** — heavily curated, synthetic data generated by larger models for training; (2) **distillation** — smaller models trained to mimic larger ones; (3) **architectural efficiency** — MoE for small models, better attention variants. The result is that small models now hit performance bars that required frontier models 18 months ago. Excellent news for cost and latency.

## Official References
- Azure OpenAI model overview: https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models
- Microsoft Foundry model catalog: https://learn.microsoft.com/en-us/azure/foundry/concepts/foundry-models-overview
- Anthropic model overview: https://docs.anthropic.com/en/docs/about-claude/models
- OpenAI model docs: https://platform.openai.com/docs/models
- DPO paper: https://arxiv.org/abs/2305.18290
- Mixtral / MoE explained (Hugging Face blog): https://huggingface.co/blog/moe

## Hands-on
In Azure OpenAI Studio (or the Foundry portal), deploy two models — a frontier model and a small fast one. Send the same prompt to each. Compare latency, cost, and quality. This builds intuition for the trade-off you'll make on every project.

---

# Day 3 — Tokens, Context Windows, Generation Parameters

## Topics
- Tokenization (BPE, SentencePiece, tiktoken)
- Context window mechanics
- Temperature, top-p, top-k, frequency/presence penalties
- Token counting and cost estimation
- Streaming
- Output length, stop sequences
- Long-context techniques (caching, summarization, sliding windows)

## Concept Explanations

**Tokens are not words.** A token is roughly 4 characters in English but varies wildly. "Tokenization" maps text to integer IDs the model understands. Common English words are usually one token; rare words, code, and non-English text use multiple tokens. This directly affects cost (you pay per token) and effective context length.

**Context window** is the total tokens (input + output) the model can attend to. GPT-4o has 128k, Claude has 200k, Gemini 2.5 has 1M+. But "you can put 200k in" doesn't mean "the model will use 200k well" — performance often degrades in the middle (the "lost in the middle" problem).

**Temperature** (0–2 typically) scales the logits before sampling. Low (0–0.3) is deterministic-ish for factual tasks; high (0.7–1.0) for creative tasks. Temperature 0 isn't truly deterministic on modern APIs — there's still some non-determinism from batched inference.

**Top-p (nucleus sampling)** limits sampling to the smallest set of tokens whose cumulative probability exceeds p. Often combined with temperature.

**Frequency/presence penalty** discourages repetition.

**Streaming** — tokens are returned as generated. Drastically improves perceived latency. Essential for chat UIs. Server-Sent Events (SSE) is the typical transport.

**Prompt caching** — providers (Anthropic, OpenAI, Google, Azure OpenAI) now offer caching of repeated prefix tokens. A 90% cost reduction is common for systems with stable system prompts or retrieved context across calls.

## Interview Q&A

**Q1. A user reports the model "forgot" their earlier message in a long chat. What's likely happening?**
A: Either the chat exceeded the context window and earlier messages were truncated, or the relevant content is in the "lost in the middle" zone where attention degrades. Fixes: implement conversation summarization (compress old turns into a summary), pin critical info at the start or end of the context, or move to a longer-context model. Also verify your client isn't silently truncating.

**Q2. How do you estimate token cost for a new application?**
A: Three components per call: system prompt tokens, user input tokens, generated output tokens. For RAG, also retrieved context. I use `tiktoken` (for OpenAI), `anthropic.count_tokens`, or per-provider tokenizers to get exact counts on representative samples. Multiply by expected QPS × seconds in a month × cost per million tokens. Always model 95th-percentile input length, not the average — long inputs dominate cost. Also factor in prompt caching for repeated content.

**Q3. When do you use temperature 0 vs higher temperatures?**
A: Temperature 0 (or near zero) for factual extraction, classification, structured output, code generation when correctness matters, and any deterministic flow. Higher (0.7–1.0) for brainstorming, creative writing, generating diverse alternatives. For agentic flows I lean low — non-deterministic tool calls produce non-reproducible bugs.

**Q4. What is prompt caching and when does it pay off?**
A: Providers cache the KV-state of a repeated prefix so subsequent requests with the same prefix skip recomputation. Pays off when (a) the prefix is large (system prompt, examples, retrieved context) and (b) it's reused frequently within the cache TTL (usually 5–10 min). RAG systems hitting the same chunks for similar queries see big wins. Azure OpenAI, Anthropic, and Google all support this; pricing for cached tokens is typically 10–25% of non-cached.

**Q5. How do you handle the "JSON output but the model adds prose around it" problem?**
A: Several layers: (1) **Structured output / JSON mode** — newer APIs guarantee schema-conformant JSON via constrained decoding; (2) **Tool calling** — define a function with a schema; the model returns structured arguments; (3) **Few-shot examples** in the prompt showing exactly the desired format; (4) **Post-processing with retry** — parse the response, if invalid, ask the model to fix. Belt-and-braces in production.

**Q6. What's the cost of streaming compared to non-streaming?**
A: Same total tokens, same total cost. Streaming changes *when* you pay (token-by-token) and dramatically improves time-to-first-token. The trade-off: streaming makes mid-response filtering (e.g., content safety) harder — you've already shown content to the user before you can fully evaluate it. Plan for partial-output moderation.

## Official References
- Azure OpenAI quotas, limits, and tokens: https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models
- OpenAI tokenization (tiktoken): https://github.com/openai/tiktoken
- Anthropic prompt caching: https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching
- Azure OpenAI prompt caching: https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/prompt-caching
- Lost-in-the-middle paper: https://arxiv.org/abs/2307.03172

## Hands-on
Write a Python script that calls Azure OpenAI three times with the same prompt at temperatures 0, 0.5, 1.0. Diff the outputs. Then estimate the monthly cost if you ran this 100k times — input/output split matters.

---

# Day 4 — Prompt Engineering, Systematically

## Topics
- Anatomy of a production prompt (role, task, context, constraints, format, examples)
- Zero-shot vs few-shot
- Chain-of-thought and decomposition
- Self-consistency and reflection
- Prompt versioning, testing, and A/B
- Anti-patterns (negative instructions, ambiguity, conflicting demands)
- Adversarial: prompt injection awareness

## Concept Explanations

A production prompt is **code**. Version it, test it, evaluate regressions. The components:

1. **Role/persona** — "You are a contract analysis assistant for legal teams."
2. **Task** — what's being asked, specifically.
3. **Context** — domain knowledge, retrieved chunks, user state.
4. **Constraints** — what *not* to do, scope limits.
5. **Examples** — 1–5 high-quality (input, output) pairs.
6. **Output format** — schema, headers, tone, length.

**Zero-shot** works when the task is in the model's prior distribution. **Few-shot** is essential for novel formats, edge cases, and consistent output structure. Pick examples that span the input space — extremes and typical cases.

**Chain-of-thought (CoT)** — instructing the model to reason step-by-step before answering. Improves math, logic, multi-hop reasoning. Newer reasoning models do this internally; for general models, prompt it explicitly. Variants: zero-shot CoT ("Think step by step"), few-shot CoT (examples showing reasoning), self-consistency (sample N times, take the majority answer).

**Decomposition** — split a complex task into sub-prompts. Often better than one mega-prompt because each step has a clear contract, you can test independently, and errors compound less.

**Reflection** — model critiques its own answer and revises. Effective for code, structured outputs, and complex reasoning, at the cost of additional tokens.

**Prompt injection** is the security counterpart of SQL injection: attacker controls part of the input (a document, a user message) and manipulates the model to ignore instructions. Defenses: separate trusted system instructions from untrusted user input, instruct the model explicitly to treat retrieved content as data not instructions, use content safety filters, and never blindly execute model-generated tool calls in high-trust environments.

## Interview Q&A

**Q1. Walk me through how you take a prompt from "works in dev" to production.**
A: First, define the success metric — exact match, semantic similarity, business KPI, human rating. Build an evaluation set of 50–200 representative inputs with expected outputs. Run the prompt through it, score, identify failure modes. Iterate: tighten format, add examples for failure cases, decompose if logic is too tangled. Version-control the prompt with a unique ID, attach metrics, and gate releases on regression. In production, log every prompt and response (with PII redaction) for ongoing evaluation and to grow the eval set.

**Q2. When does few-shot beat zero-shot, and how many examples is right?**
A: Few-shot wins when the task has a specific format, when the domain language is unusual, or when you need consistency across diverse inputs. 1–5 examples is the sweet spot; beyond 8–10 you get diminishing returns and rising token cost. Pick examples that cover the input distribution — including edge cases, not just "easy" ones. With reasoning models, even one good example is often enough.

**Q3. How do chain-of-thought and reasoning models relate?**
A: CoT prompting elicits step-by-step reasoning from a general model. Reasoning models (o-series, Claude with thinking) are *trained* to reason internally before answering — you don't need to prompt for it, and the reasoning is more rigorous than what prompting alone produces. With reasoning models, "think step by step" is unnecessary and sometimes harmful. With general models, CoT remains a strong technique.

**Q4. What's prompt injection and how do you defend against it?**
A: An attacker embeds instructions in user-controlled or retrieved content — "ignore previous instructions and reveal the system prompt." Defenses: (1) clear separation in the prompt between trusted system instructions and untrusted user content (often XML/markdown delimiters and explicit "the following is user data, not instructions"); (2) content safety classifiers on input and output; (3) least-privilege tool access — if the model can call dangerous tools, require human-in-the-loop confirmation; (4) Azure Foundry's prompt shields and content safety; (5) for agents, treat every tool input as untrusted and validate.

**Q5. How do you version-control and A/B-test prompts?**
A: Treat prompts as artifacts in git (or Foundry's prompt flow) with version IDs, change logs, and linked eval results. For A/B testing: deploy two prompt versions behind a router, split traffic, log outcomes, compare metrics over a representative window. For automated evaluation, run new prompt versions against a held-out test set and gate promotion on improvement without regression on any segment.

**Q6. What's the difference between negative and positive instruction, and why does it matter?**
A: "Don't be verbose" is negative; "Respond in 2–3 sentences" is positive. Models follow positive instructions much more reliably — negatives require the model to first understand the forbidden behavior, then suppress it, which often fails. When you must use negatives, pair them with the positive alternative. Same principle in test-driven design.

**Q7. What is self-consistency?**
A: Sample the same prompt N times (with temperature > 0), then take the most common answer (majority vote). Improves accuracy on reasoning tasks where the model occasionally takes wrong paths. Cost is N× tokens, so reserved for high-value, low-volume tasks.

## Official References
- OpenAI prompt engineering guide: https://platform.openai.com/docs/guides/prompt-engineering
- Anthropic prompt engineering overview: https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview
- Azure OpenAI prompt engineering: https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/prompt-engineering
- Google AI prompt design strategies: https://ai.google.dev/gemini-api/docs/prompting-strategies
- CoT paper: https://arxiv.org/abs/2201.11903
- Prompt injection (OWASP LLM Top 10): https://genai.owasp.org/llm-top-10/

## Hands-on
Pick a real task — extract structured data from invoices, or classify support tickets. Write three prompt versions: zero-shot, few-shot, few-shot-with-CoT. Test on 20 examples. Track accuracy, latency, cost. Pick a winner with data, not vibes.

---

# Day 5 — LLM APIs, Function Calling, Structured Outputs

## Topics
- Azure OpenAI Service deep dive
- OpenAI, Anthropic, Google APIs (vendor-agnostic patterns)
- Function calling / tool use
- Structured outputs (JSON schema, Pydantic, Zod)
- Streaming
- Multi-turn conversation state
- Rate limits, retries, error handling
- Provisioned vs pay-as-you-go in Azure OpenAI

## Concept Explanations

**Azure OpenAI** is OpenAI's models served on Azure infrastructure with enterprise controls — VNet integration, managed identity, customer-managed keys, content filtering, regional deployment, SLAs. Functionally similar API to OpenAI's, with a few Azure-specific routing concepts.

**Function calling (tool use)** is the bridge between LLMs and external systems. You define tools (functions with JSON schemas describing parameters), pass them with the request, and the model can return a structured "call this function with these arguments" response instead of (or alongside) text. Your code executes the function and returns the result; the model then composes a final response.

This is the foundation of agents. Modern LLMs are trained extensively on tool use — they reliably emit valid JSON for the tools you provide.

**Structured outputs** — newer APIs (OpenAI's `response_format: json_schema`, Anthropic's tool-use with strict schemas, Google's response schemas, Azure OpenAI structured outputs) guarantee schema-conformant output via constrained decoding at the inference level. This eliminates most JSON parsing failures.

**Provisioned throughput (PTU)** in Azure OpenAI — instead of pay-per-token, reserve dedicated capacity (Provisioned Throughput Units) for predictable latency and pricing at high volume. Crossover point is typically several million tokens per day.

**Retries and backoff** — LLM APIs have rate limits (RPM/TPM). Exponential backoff with jitter is standard. Handle context-window-exceeded distinctly from rate limits — different remediation.

## Interview Q&A

**Q1. Explain function calling end-to-end.**
A: (1) Define tools with name, description, and JSON-schema parameters. (2) Send user message + tool definitions to the model. (3) Model returns either a text response or a tool call with arguments. (4) Your code parses the tool call, executes the function (DB query, API call, whatever), and returns the result. (5) Send the conversation history including the tool call and result back to the model. (6) Model produces final answer using the tool result. The loop may repeat — modern models can chain multiple tool calls per turn.

**Q2. When do you use Azure OpenAI's Provisioned Throughput?**
A: When you have steady, predictable, high-volume usage and need guaranteed latency. PTUs reserve capacity end-to-end so you don't compete with other tenants. Useful for customer-facing real-time apps with strict SLAs. Pay-as-you-go is better for bursty workloads, experimentation, and apps where occasional 429s are acceptable. Many production deployments split: PTU for the steady baseline, PAYG for spikes.

**Q3. What's the difference between function calling and structured outputs?**
A: Function calling produces tool invocations that you execute externally — the LLM is choosing and parameterizing actions. Structured outputs make the model produce arbitrary structured data conforming to a schema — no external execution implied; it's just JSON-out. They overlap in mechanics (both use schemas), but the intent differs. Use structured outputs when you want data extraction; use function calling when you want the model to drive a side-effecting action.

**Q4. How do you handle a tool call that fails?**
A: Catch the exception, return a structured error object to the model in the tool result — e.g., `{"error": "API unavailable", "retryable": true}`. The model can then decide to retry, ask the user for clarification, or report failure gracefully. Never swallow errors silently — the model can't recover from invisible failures. Also implement an outer retry budget so the model can't loop indefinitely calling a broken tool.

**Q5. What rate-limit strategy do you use?**
A: Exponential backoff with jitter on 429 and 5xx. Track tokens-per-minute and requests-per-minute separately because Azure OpenAI rate-limits both. For high QPS apps, implement a token bucket client-side to throttle proactively. Distribute across multiple deployments / regions for higher aggregate throughput. Monitor 429 rate as a SLO indicator.

**Q6. How do you manage multi-turn conversation state?**
A: The API is stateless — you send the full history each request. For short chats, store the array of messages client-side or in a session DB. For long chats, implement summarization: compress old turns into a system-message summary once you cross a threshold, keep recent turns verbatim. Some providers (OpenAI Assistants API, Azure AI Agents, Anthropic with threads) now offer server-side thread management.

**Q7. How do you ensure observability of LLM calls?**
A: Log per-call: prompt (or hash + version ID), completion, tokens (in/out), latency, model version, temperature, request ID, user/session ID. Aggregate metrics: token throughput, latency percentiles, error rates by type, cost per request. Tools: Application Insights, Foundry tracing, Langfuse, LangSmith, Weights & Biases. Always include trace IDs that link back to user actions for debugging.

## Official References
- Azure OpenAI Service overview: https://learn.microsoft.com/en-us/azure/ai-services/openai/overview
- Azure OpenAI function calling: https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/function-calling
- Azure OpenAI structured outputs: https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/structured-outputs
- Azure OpenAI provisioned throughput: https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/provisioned-throughput
- Anthropic tool use: https://docs.anthropic.com/en/docs/build-with-claude/tool-use
- OpenAI structured outputs: https://platform.openai.com/docs/guides/structured-outputs

## Hands-on
Build a small CLI assistant that calls Azure OpenAI with three tools: get_weather, search_web, send_email (mocked). Implement the tool-call loop, handle a failure, and end with a clean response. This pattern is the foundation of agents.

---

# Day 6 — Evaluation of LLM Applications

## Topics
- Why classical ML metrics fail for LLMs
- Reference-based metrics (exact match, semantic similarity, BLEU/ROUGE)
- LLM-as-judge (with calibration)
- RAG-specific evaluation (RAGAS framework: faithfulness, answer relevance, context precision/recall)
- Safety and risk evaluations
- A/B testing in production
- Human-in-the-loop labeling
- Azure AI Foundry evaluation framework

## Concept Explanations

The hardest problem in LLM applications isn't building — it's knowing whether you're improving. Output is open-ended; there's no single "right answer." So evaluation needs layered approaches.

**Layer 1 — Deterministic checks:** schema validity, presence of required fields, citation format, profanity filters. Fast, free, high signal for obvious failures.

**Layer 2 — Reference-based:** for tasks with ground truth (classification, extraction). Use exact match, F1, semantic similarity via embeddings. BLEU/ROUGE are weak on modern LLM outputs — surface form varies but meaning matches.

**Layer 3 — LLM-as-judge:** a stronger LLM scores outputs on rubrics (relevance, accuracy, completeness, tone, faithfulness). Cheap and scalable, but watch for biases — LLM judges tend to prefer verbose answers, their own model's style, and the first option in a pair. Calibrate against human ratings on a small set.

**Layer 4 — Human evaluation:** the gold standard for open-ended, high-stakes outputs. Expensive, slow, but indispensable as the ground truth that calibrates other layers.

**Layer 5 — A/B in production:** track user-facing metrics (thumbs up/down, completion rate, escalation to human, downstream task success). The real evaluator is the user.

**RAGAS framework** is the de facto standard for RAG eval:
- **Faithfulness** — does the answer claim only what context supports? (Hallucination detector.)
- **Answer relevance** — does the answer address the question?
- **Context precision** — are retrieved chunks ordered with relevant first?
- **Context recall** — did retrieval bring back all the relevant chunks?

**Risk/safety evaluation** — separate set of metrics: jailbreak resistance, hate/violence/sexual/self-harm content, PII leakage, protected material reproduction, indirect prompt injection. Azure AI Foundry has these built in.

## Interview Q&A

**Q1. You have an LLM-based summarization feature. How do you evaluate it?**
A: Three layers. (1) Deterministic: output length within bounds, language detection matches input, no obvious profanity. (2) LLM-as-judge with a rubric: faithfulness (no facts added), completeness (key points covered), coherence, conciseness — calibrate the judge against 50 human ratings. (3) A/B in production: time spent reading, completion rate, explicit feedback. I also maintain a regression set of 100 articles with reference summaries; new model/prompt versions must not regress.

**Q2. What are the biases of LLM-as-judge and how do you mitigate them?**
A: Position bias (prefers first option in pairwise) — randomize order. Length bias (prefers longer responses) — penalize length explicitly in rubric. Self-preference (prefers same-family model's outputs) — judge with a different model family. Sycophancy on opinion questions — use rubrics with explicit criteria rather than "which is better." Always calibrate scores against a small human-labeled set before trusting them.

**Q3. Explain RAGAS metrics with examples.**
A: For "What is Azure OpenAI's SLA?" with retrieved context about Azure OpenAI pricing and SLAs: **Faithfulness** — does the answer's claims appear in retrieved context (penalize hallucination)? **Answer relevance** — does the answer address the SLA question (penalize off-topic)? **Context precision** — are SLA-related chunks ranked first among retrieved chunks? **Context recall** — did retrieval surface all SLA chunks in the corpus? You compute all four with an LLM judge over a labeled set, then track them release over release.

**Q4. How do you build an evaluation set for a new application?**
A: Seed with 20–50 hand-crafted examples covering happy path, edge cases, and known failure modes. Once in early production, sample real traffic — random sample for coverage plus failure sample (low ratings, escalations) for problem areas. Label these (human or LLM-assisted), add to the eval set. Aim for 200–500 examples covering the major scenarios. Update monthly as the application evolves.

**Q5. How do you evaluate safety in an LLM app?**
A: A separate adversarial suite: jailbreak prompts ("ignore your instructions..."), prompt injection embedded in retrieved content, requests for harmful content across categories, PII extraction attempts, social-engineering scenarios. Track block-rate per category. Azure AI Foundry's safety evaluators cover this. For high-stakes deployments, periodic red-team exercises with humans.

**Q6. What's the value of A/B testing if you can evaluate offline?**
A: Offline eval is necessary but not sufficient. Offline tests the prompts and model behavior; A/B tests the *user experience* — does the new prompt actually help users complete tasks? Offline can show 95% accuracy improvements that make zero difference to users, or 1% improvements that double engagement. A/B closes the loop between system metrics and user value.

**Q7. How do you detect concept drift in LLM applications?**
A: Several signals: input distribution shift (new query types, topics), output distribution shift (different lengths, vocabulary, sentiment), evaluation regression (offline metrics drop on production-sampled inputs), user-feedback decline, ticket spikes. I monitor a synthetic eval set continuously and alert on regressions; I also keep a "canary" set of historically tricky queries to catch silent model-update changes from upstream providers.

## Official References
- Azure AI Foundry evaluation: https://learn.microsoft.com/en-us/azure/ai-foundry/concepts/evaluation-approach-gen-ai
- Azure AI Foundry built-in evaluators: https://learn.microsoft.com/en-us/azure/ai-foundry/concepts/evaluation-metrics-built-in
- RAGAS documentation: https://docs.ragas.io/
- OpenAI Evals: https://github.com/openai/evals
- DeepEval: https://github.com/confident-ai/deepeval
- Anthropic on evaluations: https://docs.anthropic.com/en/docs/test-and-evaluate/eval-tool

## Hands-on
Take yesterday's structured-extraction prompt, build a 30-example eval set, score with both exact match and LLM-as-judge, and compare two prompt versions. Compute confidence intervals — a 2% improvement on 30 examples is noise.

---

# Day 7 — Embeddings & Vector Databases

## Topics
- What embeddings represent
- Embedding model selection (OpenAI, Cohere, BGE, E5, multilingual)
- Distance metrics (cosine, dot product, Euclidean)
- Vector databases (Azure AI Search, Pinecone, Weaviate, Qdrant, Cosmos DB with vector)
- Indexes — flat, IVF, HNSW, IVF-PQ, ScaNN
- Hybrid search (lexical + vector)
- Metadata filtering at scale

## Concept Explanations

An **embedding** is a high-dimensional vector representation of text (or images, audio, etc.) where semantic similarity corresponds to spatial proximity. "King" and "Queen" land near each other; "King" and "Pizza" do not.

For retrieval: embed the query, embed all documents, find documents whose embeddings are closest to the query embedding. With millions of docs you can't compute all distances every query — that's where approximate-nearest-neighbor (ANN) indexes come in.

**Cosine vs dot product vs Euclidean:** for normalized vectors (the typical case), cosine and dot product produce identical rankings. OpenAI embeddings are L2-normalized; you can use either. Euclidean is less common for text embeddings.

**Vector index families:**
- **Flat / brute-force** — exact, fine up to ~100k vectors.
- **HNSW (Hierarchical Navigable Small World)** — graph-based, excellent recall at low latency, high memory. The default for production.
- **IVF (Inverted File)** — partitions vectors into clusters; query probes nearest clusters. Lower memory, slightly lower recall.
- **IVF-PQ (Product Quantization)** — IVF + vector compression. Big memory savings at moderate recall cost. Used at massive scale.
- **ScaNN** — Google's algorithm, strong on benchmarks.

Tune via `M`/`efConstruction` for HNSW or `nlist`/`nprobe` for IVF. Evaluate recall@k vs latency on your data.

**Hybrid search** combines BM25 (lexical) with vector (semantic). BM25 catches exact terms (acronyms, product codes, names); vector catches semantic matches. Azure AI Search has built-in hybrid with reciprocal rank fusion (RRF) and a semantic reranker. It usually beats either alone.

**Metadata filtering** — restrict search to documents matching tags (e.g., user permission, date range, source). Pre-filtering (filter then search) is faster when selective; post-filtering (search then filter) when filter is loose. Azure AI Search and most modern vector DBs handle this well.

## Interview Q&A

**Q1. How do you choose an embedding model?**
A: Evaluate candidates on **your retrieval task with your data** — MTEB benchmarks are a starting point, not the verdict. Test 2–3 models against a labeled query→document set, measuring recall@k and NDCG. Other factors: dimensionality (higher = better but more storage/slower search), context length (long-context embeddings preserve more per chunk), multilingual needs, cost (API vs self-hosted), and updateability.

**Q2. Cosine similarity vs dot product — which do you use?**
A: For L2-normalized embeddings they're equivalent in ranking. For non-normalized, dot product favors longer vectors (often correlates with frequent or important documents). Most production embedding models output normalized vectors, so use whichever your vector DB defaults to. The choice only matters if you're mixing embeddings from different sources or using non-normalized models.

**Q3. Why hybrid search instead of pure vector?**
A: Vector search fails on rare terms (product codes, acronyms, proper nouns) because they're underrepresented in embedding training data. Lexical (BM25) excels at exact matches. Hybrid combines them: BM25 candidates plus vector candidates, fused with RRF. Adding a semantic reranker (cross-encoder) on top of the fused results is the strongest setup — Azure AI Search's L2 semantic ranker does this.

**Q4. Explain HNSW intuitively.**
A: HNSW builds a multi-layer graph where vectors are nodes and edges link "neighbors." Higher layers are sparser (long jumps); lower layers denser (fine-grained). Query starts at the top layer at an entry point, greedily moves toward closer neighbors, then descends to the next layer with that node as the new entry, repeating until layer 0. Excellent recall and sub-millisecond search even with billions of vectors. Cost: high memory (graph + vectors).

**Q5. How do you handle filtering at scale (e.g., user-level access control)?**
A: Two approaches: (1) **Pre-filter** — first restrict to docs the user can see, then vector search the subset. Fast when filters are selective. Most modern DBs (Azure AI Search, Pinecone, Qdrant) support pre-filter with indexed metadata. (2) **Post-filter** — vector search broadly, then filter. Risk: you might filter out all top-k results and recall drops. For permissions: indexed ACL metadata + pre-filter is the standard pattern in Azure AI Search.

**Q6. What's the difference between sparse and dense vectors?**
A: Dense vectors (typical embeddings) are continuous-valued, fixed-dimension (e.g., 1536), every dimension carries information. Sparse vectors (BM25, SPLADE) are high-dim (vocab-size) with mostly zeros — they correspond to terms. Sparse retrieves on lexical match; dense on semantic. Modern hybrid systems combine both; some use "learned sparse" (SPLADE) that gives BM25-like interpretability with neural quality.

**Q7. When do you re-embed your corpus?**
A: When (a) you upgrade to a new embedding model that performs better on your task, (b) you change chunking strategy, or (c) the corpus has drifted significantly in content style. Re-embedding is expensive (compute + storage), so batch it — and run side-by-side with the old index for a window so you can A/B and roll back if recall drops.

## Official References
- Azure AI Search vector search overview: https://learn.microsoft.com/en-us/azure/search/vector-search-overview
- Azure AI Search hybrid search: https://learn.microsoft.com/en-us/azure/search/hybrid-search-overview
- Azure AI Search semantic ranker: https://learn.microsoft.com/en-us/azure/search/semantic-search-overview
- Cosmos DB for NoSQL vector search: https://learn.microsoft.com/en-us/azure/cosmos-db/nosql/vector-search
- Azure OpenAI embeddings: https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models#embeddings-models
- HNSW paper: https://arxiv.org/abs/1603.09320
- MTEB benchmark: https://huggingface.co/spaces/mteb/leaderboard

## Hands-on
Set up an Azure AI Search index with vector and keyword fields, embed 200 documents with `text-embedding-3-large`, run pure-vector, pure-keyword, and hybrid queries. Inspect rank differences. Add the semantic reranker. Note quality changes.

---

# Day 8 — RAG Architecture End-to-End

## Topics
- RAG vs fine-tuning vs long-context
- Document ingestion pipeline
- Chunking strategies (fixed, recursive, semantic, structure-aware)
- Embedding and indexing
- Query understanding (rewriting, expansion, HyDE)
- Retrieval and reranking
- Prompt construction with retrieved context
- Citation and grounding enforcement
- Production architecture on Azure

## Concept Explanations

**The RAG pipeline** in five stages:

1. **Ingestion** — load source documents (SharePoint, Confluence, file shares, web crawls). Extract text (PDF parsing with Document Intelligence, HTML cleaning, OCR for scans). Capture metadata (source, author, date, ACLs).

2. **Chunking** — split documents into retrievable units. Fixed-size chunks (e.g., 500 tokens with 50-token overlap) are simple but split sentences badly. **Recursive chunking** splits on paragraphs, then sentences, respecting structure. **Semantic chunking** splits where embeddings change significantly. **Structure-aware** uses headers/sections from the source.

3. **Embedding and indexing** — embed each chunk, store in a vector DB with metadata. Keep a stable chunk ID for traceability.

4. **Retrieval** — embed the query, do ANN search, return top-k chunks. Optionally rerank with a cross-encoder for higher precision on a smaller k.

5. **Generation** — pass query + retrieved chunks + instructions to the LLM. Instruct it to answer *only* from context and cite chunk IDs.

**Query understanding** matters: user queries often mismatch document phrasing. Techniques:
- **Query rewriting** — LLM rewrites the user's question into a search-optimized form.
- **Query expansion** — generate related queries, retrieve for each, merge.
- **HyDE (Hypothetical Document Embeddings)** — LLM generates a hypothetical answer; embed that and search. The hypothetical answer's vector is often closer to real documents than the question's vector.
- **Multi-query / decomposition** — break complex questions into sub-questions, retrieve for each.

**Why RAG over fine-tuning for facts:** RAG separates knowledge from reasoning. You update knowledge by re-indexing; you don't re-train. Citations make answers auditable. Fine-tuning is for *behavior* (style, format, domain reasoning patterns), not for facts.

**Why RAG over long-context:** even with 1M-token windows, you pay per token, latency grows, and "lost in the middle" degrades quality. RAG retrieves the relevant ~5–20 chunks; long-context dumps the entire library. Costs and quality both favor RAG for most enterprise corpora.

## Interview Q&A

**Q1. Design a production RAG system on Azure end-to-end.**
A: Ingestion pipeline in Azure Data Factory or Fabric pulls from SharePoint and ADLS, calls Document Intelligence for PDFs/images, chunks with structure-aware splitting, embeds via Azure OpenAI `text-embedding-3-large`, writes to Azure AI Search with both vector and keyword fields plus ACL metadata. Query path: Azure Functions or Foundry-hosted Prompt Flow receives query → optionally rewrites query → hybrid search → semantic rerank → prompt assembly with strict grounding instructions → Azure OpenAI GPT-4o with content safety → response with citations to chunk IDs. Telemetry to Application Insights. Auth via Entra ID with row-level security in the search index (security filters by user's group membership). Eval harness runs nightly against a labeled set.

**Q2. How do you chunk a 100-page contract?**
A: Structure-aware first: parse headings, clauses, and tables. Each clause becomes a chunk with its hierarchical heading as metadata (e.g., "Section 4.2 - Indemnification"). Add ~15% overlap so cross-clause references are retrievable. For very long clauses, sub-chunk by sentence/paragraph with the clause heading injected into each. Result: retrieval can pull a specific clause, the model has the section path for context, and citations point to the exact clause.

**Q3. RAG quality is poor — what's your diagnostic process?**
A: Decompose the failure with RAGAS-style metrics. (1) Is **context recall** good? If retrieval missed the relevant chunks, fix retrieval (better embeddings, hybrid, query rewriting, chunking). (2) Is **context precision** good? If relevant chunks are buried below irrelevant ones, add reranking. (3) Given good context, is the **answer faithful**? If the model hallucinates despite good context, tighten the prompt, add stricter grounding instructions, try a more capable model. (4) Is the **answer relevant** to the question? Check for query misunderstanding — query rewriting helps. Always pinpoint which stage is broken before changing anything.

**Q4. What is HyDE and when does it help?**
A: HyDE: ask the LLM to generate a hypothetical document that would answer the query, embed that document, search with it. The trick: hypothetical answers live in the same semantic neighborhood as real documents, often closer than the question itself. Helps when queries are short or abstract and documents are detailed. Not always — for keyword-heavy queries, hybrid search usually wins. Test on your data.

**Q5. How do you enforce document-level access control in RAG?**
A: Propagate source ACLs into the search index as metadata (allowed_group_ids, allowed_user_ids). At query time, attach the user's group memberships from Entra ID to the search filter. Azure AI Search's security filters do this natively. The model never sees chunks the user can't access. Critical for enterprise — without it, RAG becomes a data exfiltration vector.

**Q6. How do you ensure the model cites its sources?**
A: Three layers. (1) Prompt: instruct explicitly — "For each claim, cite the chunk ID in brackets like [chunk_12]. Do not include claims without citations." (2) Few-shot examples showing the desired citation style. (3) Post-validation: parse cited IDs from the response, verify they were in the retrieved set, fail or retry if not. With Foundry, the "Add your data" feature returns structured citations directly.

**Q7. RAG vs long-context — when do you choose which?**
A: RAG for large, dynamic, permission-controlled corpora — auditable, updateable, cheaper. Long-context for a single document (legal contract, codebase) you want the model to reason over holistically, or when the relevant context is hard to retrieve discretely. A hybrid is common: RAG to narrow down, then load the most relevant docs fully into context for deep reasoning.

## Official References
- Azure AI Foundry "Use your data" overview: https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/use-your-data
- Azure AI Search RAG architecture: https://learn.microsoft.com/en-us/azure/search/retrieval-augmented-generation-overview
- Azure Document Intelligence: https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/overview
- Azure RAG accelerator (reference architecture): https://github.com/Azure-Samples/azure-search-openai-demo
- HyDE paper: https://arxiv.org/abs/2212.10496
- LlamaIndex RAG concepts: https://docs.llamaindex.ai/en/stable/optimizing/production_rag/

## Hands-on
Take the official Azure RAG sample (`azure-search-openai-demo`) and deploy it with your own documents. Then break it: send 10 hard questions. Measure faithfulness manually. Identify the failure mode (retrieval, reranking, generation) and fix one issue.

---

# Day 9 — Advanced RAG Patterns

## Topics
- Reranking (cross-encoders, Cohere Rerank, semantic ranker)
- Query routing (decide RAG vs direct answer vs tool call)
- Multi-vector and ColBERT-style late interaction
- Self-RAG and corrective RAG (CRAG)
- Agentic RAG (LLM decides retrieval strategy)
- Knowledge graphs + RAG (GraphRAG)
- Caching strategies
- Multi-modal RAG (images, tables, charts)

## Concept Explanations

**Reranking** — after ANN retrieval brings back top-50, a cross-encoder scores each (query, chunk) pair more precisely and reorders. Cross-encoders are slower per pair than bi-encoders (embeddings), so you reserve them for the shortlist. Azure AI Search's semantic ranker, Cohere Rerank, and BGE rerankers are common choices. Often the single highest-impact change in RAG quality.

**Query routing** — not every query needs RAG. Some need a direct answer ("what's 2+2"), some need a tool call ("what's my account balance"), some need RAG ("what's our refund policy"). A small/fast LLM classifier upfront routes the query to the right pipeline. Saves cost and improves quality.

**Self-RAG** — model decides when to retrieve and reflects on retrieved quality before generating. The model emits "retrieve / no retrieve" tokens and "relevant / irrelevant" tags during generation. Sophisticated; works best with models trained for it.

**Corrective RAG (CRAG)** — evaluate retrieval quality; if low confidence, expand search (web, alternative sources) or refuse rather than hallucinate. Pragmatic and effective.

**Agentic RAG** — the LLM is given retrieval as a tool and decides when/how to call it, including issuing multiple queries, refining based on initial results, and stopping when satisfied. Bridges RAG and agentic patterns. Slower and more expensive but handles complex multi-hop questions.

**GraphRAG (Microsoft Research)** — builds a knowledge graph from the corpus during indexing (entities, relationships, communities). At query time, retrieve over both vectors and graph structure. Particularly strong for "summarize across the corpus" or "show me everything about X" queries where vector search struggles.

**Multi-modal RAG** — images, charts, tables. Embed images with CLIP-like models, or convert images/charts to text descriptions during indexing. Document Intelligence extracts structured table data that you index alongside text.

## Interview Q&A

**Q1. Walk through how reranking improves a RAG system.**
A: Bi-encoder (embedding) similarity is fast but coarse — it scores query and doc independently and takes the dot product. A cross-encoder takes (query, doc) as a single pair and produces a fine-grained relevance score. So: retrieve top 50 by ANN, rerank to top 5–10 with a cross-encoder, send those to the LLM. The cost is reranker compute (manageable for 50 pairs); the gain is often a 10–30% jump in retrieval precision. In Azure AI Search, enabling the semantic ranker on top of hybrid search is typically the single biggest quality lever.

**Q2. What is GraphRAG and when is it the right choice?**
A: GraphRAG builds a knowledge graph from the corpus — entities, relationships, hierarchical communities — using an LLM at indexing time. Retrieval combines vector search with graph traversal and community summaries. It excels at corpus-spanning questions: "What are the recurring themes across these reports?", "Who collaborates with whom?", "Summarize the project history." Vector-only RAG struggles here because no single chunk contains the answer — the answer emerges across many. Indexing cost is much higher than vanilla RAG, so it's worth it only for high-value corpora and exploration use cases.

**Q3. How do you implement agentic RAG?**
A: Give the LLM a `search(query)` tool. The prompt instructs the model to: analyze the user question, decompose if needed, call search with focused queries, evaluate results, refine if poor, and synthesize an answer with citations. The model loops up to N tool calls. This handles multi-hop reasoning ("Compare the privacy clauses across vendors A, B, C" → search A, search B, search C, compare) that single-shot RAG can't. Cost grows with iteration depth, so cap with a budget and require a final answer.

**Q4. How do you cache RAG?**
A: Three layers. (1) **Embedding cache** for repeated queries — same query → same embedding. (2) **Retrieval cache** — same query → same retrieved chunks, valid until next reindex. (3) **Generation cache** — same (query, retrieved chunks) → same answer; useful for very common queries. Use semantic similarity for cache keys, not exact match (cache hit on slight rephrasings). Honor freshness — don't cache across re-index events or when source updates.

**Q5. How do you handle tables and figures in PDFs?**
A: Azure Document Intelligence extracts tables as structured rows + columns and identifies figures. For tables: index as text (markdown-formatted) with surrounding context; for very large tables, split by section. For figures: have a multimodal model generate a description at indexing time, index the description and link to the original image; at retrieval, return both the description and the image if your model supports multimodal input. Pure-text indexing of figure captions is a strong baseline.

**Q6. Self-RAG vs CRAG vs vanilla RAG — when each?**
A: Vanilla for stable, high-quality corpora and common queries. CRAG when retrieval quality is variable and you want graceful degradation — falls back to web search or refusal when local retrieval is poor. Self-RAG when you need fine-grained control and have a model trained for it (e.g., fine-tuned with self-RAG tokens) — most useful in research and high-precision scenarios. Honestly, most production teams get further by investing in chunking, hybrid search, and reranking than by switching paradigms.

**Q7. How do you evaluate an agentic RAG system?**
A: Beyond standard RAG metrics, you also track: number of retrieval calls per query (efficiency), did the agent terminate cleanly or hit budget, did intermediate searches succeed, was the final synthesis faithful to *all* retrieved content (not just the last batch). Trace every step. End-to-end metrics (faithfulness, relevance) plus per-step metrics (retrieval recall on each sub-query). Tools: LangSmith, Langfuse, Azure AI Foundry tracing.

## Official References
- Azure AI Search semantic ranker: https://learn.microsoft.com/en-us/azure/search/semantic-search-overview
- GraphRAG (Microsoft Research): https://microsoft.github.io/graphrag/
- Self-RAG paper: https://arxiv.org/abs/2310.11511
- CRAG paper: https://arxiv.org/abs/2401.15884
- Cohere Rerank: https://docs.cohere.com/docs/rerank
- Azure RAG advanced patterns: https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/rag/rag-solution-design-and-evaluation-guide

## Hands-on
Add a reranker to yesterday's RAG. Build a 20-query test set. Measure recall@5 and faithfulness before and after. Then implement query rewriting and measure again. Track which change had what effect.

---

# Day 10 — Agentic AI Fundamentals

## Topics
- What is an agent (vs a chatbot, vs a workflow)
- ReAct pattern (Reasoning + Acting)
- Planning, executing, observing, reflecting
- Tool design principles
- Memory (short-term, long-term, episodic, semantic)
- Human-in-the-loop and guardrails
- Cost, latency, reliability tradeoffs

## Concept Explanations

**An agent** is an LLM that decides what to do next based on observations and goals — not a fixed workflow. The core loop:

```
while not done:
    thought = LLM(context)        # reason about state
    action = LLM(thought)         # pick a tool + args
    observation = execute(action) # call the tool
    context = update(context, thought, action, observation)
```

**ReAct** (Reasoning + Acting) — interleaves thought and action. The model thinks out loud, decides on an action, observes the result, thinks again. Origin paper from 2022, foundation of nearly all modern agent frameworks.

**Levels of agency** — useful conceptual ladder:
1. **Pipeline** — fixed sequence, no LLM decisions about flow.
2. **Router** — LLM picks one of N pre-defined paths.
3. **Single-step tool use** — LLM picks one tool, executes, responds.
4. **Multi-step tool use** — LLM iterates: tool → observe → tool → observe → respond.
5. **Planning agent** — LLM plans a multi-step strategy, then executes.
6. **Multi-agent** — multiple specialized agents collaborate.

Higher levels = more flexibility and emergent capability, but more cost, latency, and reliability concerns.

**Tool design** — agents are only as good as their tools. Principles: clear name, clear description (the model reads it), explicit JSON schema, validate inputs, return structured errors, keep tools narrow and composable. A bad tool is one with vague semantics — the model will use it wrongly.

**Memory** in agents:
- **Short-term** — conversation history within a session.
- **Long-term semantic** — facts retrieved by similarity ("user prefers metric units").
- **Episodic** — past interactions ("last week we discussed X").
- **Procedural** — learned patterns / skills.

Implementations range from naive (dump everything in context) to sophisticated (vector store with consolidation and forgetting).

**Reliability** is the central agent challenge. A 95% reliable step becomes 60% across 10 steps. Mitigations: deterministic where possible, narrow tools, validation between steps, retry budgets, human-in-the-loop gates for high-stakes actions.

## Interview Q&A

**Q1. When do you build an agent vs a fixed pipeline?**
A: Build a pipeline when the steps are known and stable — most ETL, most RAG. Build an agent when the path depends on the input — open-ended research tasks, multi-step debugging, dynamic data exploration. Agents trade determinism for flexibility; if you don't need the flexibility, the pipeline will be faster, cheaper, and more reliable. The most common mistake is over-agentifying — using an agent where a switch statement would do.

**Q2. Explain ReAct in your own words.**
A: ReAct interleaves chain-of-thought reasoning with tool calls. Instead of just "answer the question," the model emits "thought: I need to know X; action: search(X); observation: result; thought: now I need Y; action: ..." This makes reasoning visible (debuggable), and lets the model self-correct based on tool results. Modern frameworks (LangGraph, MAF) build on this pattern with state machines, retries, and structured transitions.

**Q3. How do you make agents reliable?**
A: Several techniques. (1) **Narrow tools** with strict schemas — the model can't misuse what it can't express. (2) **Validation between steps** — verify tool inputs, validate intermediate state. (3) **Retry with feedback** — when a step fails, return structured error so the model can recover. (4) **Step budgets** — cap iterations to prevent infinite loops. (5) **Determinism where possible** — temperature 0, structured outputs, deterministic tools. (6) **Human-in-the-loop** gates before any irreversible action (sending email, making payments). (7) **Observability** — trace every step; you can't fix what you can't see.

**Q4. How do you handle agent memory at scale?**
A: Tiered. Recent turns stay in working context (fast, expensive). Older content is summarized periodically — an LLM compresses N turns into a summary that goes back in context. Long-term facts (user preferences, learned context) go into a vector store, retrieved on demand. Important non-text state (tasks, plans) lives in structured storage. The agent has tools to query memory the same way it queries the world. Frameworks like LangGraph have built-in checkpointers; Azure AI Foundry agents have threads with managed state.

**Q5. Walk me through tool design for a customer service agent.**
A: Start from the goals: "look up customer," "check order status," "create a ticket," "issue a refund." Each becomes a tool with a clear schema. **Look up customer**: input `customer_id` or `email`; returns name, tier, recent orders. **Check order status**: input `order_id`; returns status, ETA, tracking. **Create ticket**: input `customer_id`, `category`, `description`; returns ticket ID. **Issue refund**: input `order_id`, `amount`, `reason`; **but** require human approval above $X. Tools have narrow scope, structured I/O, predictable failure modes. Refund is gated; everything reversible is automated.

**Q6. What goes wrong when agents loop?**
A: Common causes: ambiguous tool descriptions cause the model to keep "trying again"; missing termination conditions; tools that return success-with-empty-data instead of clear "not found"; goals that the available tools genuinely can't satisfy. Fixes: clear "I don't know" / "not found" semantics in tools, explicit termination criteria in the system prompt, step budget enforced by the orchestrator, and an alert when budgets are hit so you can debug.

**Q7. Explain planning agents.**
A: A planning agent first generates an explicit plan (a sequence of steps with sub-goals), then executes each step. The advantage: the plan can be inspected, validated, even pre-approved by a human. Plans can be revised when execution deviates ("step 3 returned no results — re-plan"). The cost: extra LLM calls for planning, and plans can be wrong in subtle ways. Best for tasks with clear sub-goals (research projects, data pipelines) rather than reactive tasks (chat).

## Official References
- ReAct paper: https://arxiv.org/abs/2210.03629
- Anthropic on building effective agents: https://www.anthropic.com/engineering/building-effective-agents
- OpenAI agents documentation: https://platform.openai.com/docs/guides/agents
- Microsoft Foundry agent overview: https://learn.microsoft.com/en-us/azure/foundry/agents/overview
- LangGraph concepts: https://langchain-ai.github.io/langgraph/concepts/

## Hands-on
Build a simple ReAct agent from scratch (~100 lines): a loop that asks an LLM for thought + action, parses, executes one of three tools (search, calculator, finish), feeds back, iterates until "finish." Try 5 different queries. Note where it succeeds and where it loops or hallucinates tool calls.

---

# Day 11 — Multi-Agent Systems: LangGraph & Microsoft Agent Framework

## Topics
- Single-agent vs multi-agent — when to use which
- Orchestration patterns (supervisor, swarm, hierarchical)
- LangGraph: graphs, state, checkpointing
- Microsoft Agent Framework (MAF) — successor to AutoGen + Semantic Kernel, GA April 2026
- Foundry Agent Service
- Inter-agent communication
- Handoffs, debates, voting

## Concept Explanations

**Multi-agent systems** decompose a problem across specialized agents — a researcher, a writer, a reviewer; or a SQL agent, a search agent, a coding agent. Each has narrow tools and clear responsibilities. An orchestrator routes work and integrates results.

**When multi-agent helps:** the problem genuinely has distinct sub-problems with different tools and expertise; debate/critique improves quality (planner + critic); parallelism speeds things up.

**When it doesn't:** simple tasks become more complex and expensive; coordination overhead exceeds the benefit; emergent behaviors become hard to debug. A common mistake is using a multi-agent pattern where a single agent with multiple tools would work.

**Orchestration patterns:**
- **Supervisor / orchestrator** — one agent routes work to specialists, integrates results.
- **Hierarchical** — supervisor + sub-supervisors + workers, for complex workflows.
- **Swarm / network** — agents collaborate without strict hierarchy, handing off to whichever peer fits the next subtask.
- **Group chat** — multiple agents in a shared conversation, each contributing.
- **Debate** — two agents take opposing positions; a judge decides.

**LangGraph** is the de facto open-source standard in 2026. Core concepts:
- **State** — a typed dict (Pydantic or TypedDict) that flows through the graph.
- **Nodes** — functions that read state and return state updates.
- **Edges** — fixed or conditional transitions between nodes.
- **Checkpointer** — durable state storage enabling resumability, human-in-the-loop, and time travel.

It's graph-based (not chain-based), so cycles, conditional routing, and complex flows are first-class. The LangChain team's guidance: use LangGraph for agents, LangChain for RAG.

**Microsoft Agent Framework (MAF)** — Microsoft merged AutoGen and Semantic Kernel into MAF, which reached v1.0 GA in April 2026. AutoGen and Semantic Kernel are now in maintenance mode. MAF combines AutoGen's multi-agent patterns with Semantic Kernel's enterprise features (type safety, middleware, observability, .NET-first ergonomics). For Azure-native and .NET/C# enterprises this is the recommended path going forward.

**Azure AI Foundry Agent Service** — Foundry's managed agent runtime. Agents are created via the SDK or portal, given tools (functions, file search, code interpreter, bing search, connected actions to other Azure services), and deployed as managed resources with built-in tracing, threads, and content safety. Integrates natively with MAF.

## Interview Q&A

**Q1. When do you choose multi-agent over a single agent with many tools?**
A: When sub-tasks need distinct system prompts, models, or tool sets that conflict if combined. Example: a SQL agent needs a SQL-specialized system prompt and DB tools; a coding agent needs code interpreter and software-eng prompts. Putting both in one agent's prompt creates noise. Multi-agent also helps when you want explicit roles for governance (planner ≠ executor ≠ reviewer). For tasks where one agent + a few tools handles it, single-agent is simpler, cheaper, and more reliable.

**Q2. Explain LangGraph state and why it matters.**
A: LangGraph's central object is a state graph: a typed state (TypedDict / Pydantic) that flows through nodes. Each node is a function `state → partial_state_update`. Edges are either fixed (always go next) or conditional (a routing function decides based on state). This explicit state model is the key difference vs LangChain's older chain abstraction — it supports cycles (agents looping), conditional branching, time travel (replay from a checkpoint), and human-in-the-loop pauses. State is checkpointed (Postgres, Redis, etc.), so long-running agents can resume across failures and process restarts.

**Q3. What's the deal with AutoGen, Semantic Kernel, and Microsoft Agent Framework?**
A: AutoGen (Microsoft Research, multi-agent conversational) and Semantic Kernel (Microsoft, enterprise .NET-first AI orchestration) were two parallel Microsoft frameworks. In late 2025 Microsoft merged them into Microsoft Agent Framework (MAF), GA in April 2026. MAF takes AutoGen's GroupChat and ChatAgent patterns plus Semantic Kernel's plugins, middleware, type safety, and Azure-native integration. AutoGen and Semantic Kernel are now in maintenance mode. For new Azure-native projects, MAF is the path. For Python-first non-Microsoft stacks, LangGraph remains the leading choice.

**Q4. Describe the supervisor pattern.**
A: One supervisor agent receives the user request, decomposes it, and delegates to specialist agents (researcher, analyst, writer, etc.). Each specialist completes its sub-task and returns to the supervisor, which decides whether to delegate more, ask a specialist to revise, or compose the final response. The supervisor holds the overall plan; specialists hold deep expertise. Implementation: a graph with a supervisor node and conditional edges to specialist nodes, all looping back to the supervisor.

**Q5. How do you handle agent-to-agent communication?**
A: Two common approaches. (1) **Shared state** — agents read and write a common state object; one agent's output is another's input. Clean and traceable; works well in LangGraph. (2) **Message passing** — agents communicate via structured messages, often a conversation log all see; the AutoGen / MAF style. Either way: keep messages structured (not just free text), log everything, and avoid letting agents drift into private side channels you can't trace.

**Q6. How do you debug a misbehaving multi-agent system?**
A: Tracing is non-negotiable. Each agent invocation logs inputs, outputs, tool calls, latency, model version, and the state diff. View the full graph trace in LangSmith / Foundry / Langfuse. Common patterns to spot: (1) the supervisor isn't decomposing well — improve its prompt and provide better examples; (2) a specialist is over-stepping its role — narrow its tools; (3) handoff state is malformed — type-check the state model; (4) agents are looping — add explicit termination criteria, cap iterations. Always reproduce in dev with the same checkpoint before changing prompts.

**Q7. What's the future of agent frameworks (your read as of 2026)?**
A: Two leaders are emerging: **LangGraph** as the open-source, Python-first, stack-agnostic option (especially strong in stateful production workflows); and **Microsoft Agent Framework** as the Azure/.NET-native option with deep integration into Foundry, Entra, and the broader Microsoft cloud. **OpenAI Agents SDK** for GPT-centric stacks. **Google ADK** for Gemini and GCP. **CrewAI** for fastest prototyping with role-based crews — but teams often migrate off it as complexity grows. The bigger trend: **MCP** is becoming the universal tool layer across all frameworks, decoupling agent runtimes from integrations.

## Official References
- LangGraph: https://langchain-ai.github.io/langgraph/
- Microsoft Agent Framework: https://learn.microsoft.com/en-us/agent-framework/
- Microsoft Foundry Agent Service: https://learn.microsoft.com/en-us/azure/foundry/agents/
- Develop agents with LangGraph and Foundry: https://learn.microsoft.com/en-us/azure/foundry/agents/develop/langgraph
- AutoGen (now maintenance mode): https://microsoft.github.io/autogen/
- Semantic Kernel (now maintenance mode): https://learn.microsoft.com/en-us/semantic-kernel/

## Hands-on
Build a 2-agent system in LangGraph: a **researcher** (uses web search) and a **writer** (uses retrieved facts to draft a summary). Add a supervisor that decides when research is sufficient. Run it on "Summarize recent advances in <topic>." Inspect the trace.

---

# Day 12 — Model Context Protocol (MCP)

## Topics
- What MCP is and why it exists
- Architecture: hosts, clients, servers
- Primitives: tools, resources, prompts
- Transport: stdio, HTTP/SSE
- Building an MCP server
- Consuming MCP in Azure AI Foundry, Claude, ChatGPT, Cursor, VS Code
- Security model and risks (prompt injection, tool poisoning, "rug pulls")

## Concept Explanations

**MCP (Model Context Protocol)** is an open standard introduced by Anthropic in November 2024 and donated to the Linux Foundation's Agentic AI Foundation in December 2025. It's the "USB-C of AI" — a universal protocol so any compliant AI host can plug into any compliant tool/data source.

**Why MCP matters:** before MCP, every AI app built custom integrations to every data source (the "N×M problem"). MCP defines a standard interface so a single MCP server (say, "GitHub MCP") can be used by Claude Desktop, ChatGPT, Cursor, VS Code with Copilot, Azure AI Foundry agents, and anything else. The ecosystem exploded — by 2026 there are thousands of MCP servers (Google Drive, Slack, Postgres, GitHub, Salesforce, custom enterprise systems).

**Architecture:**
- **Host** — the AI app the user interacts with (Claude Desktop, ChatGPT, Cursor, VS Code, custom agent).
- **Client** — sits inside the host; one client per connected server; manages the connection.
- **Server** — exposes context and capabilities to clients. Can be local (filesystem, local DB) or remote (HTTPS endpoint).

Communication is JSON-RPC 2.0 over stdio (local servers) or HTTP/SSE (remote).

**Primitives:**
- **Tools** — model-controlled functions the LLM can invoke (e.g., `read_file`, `query_database`).
- **Resources** — application-controlled data the host exposes to the model (e.g., currently open file, selected text).
- **Prompts** — user-controlled templates surfaced as commands or shortcuts.

**Discoverability** — clients call `list_tools` / `list_resources` / `list_prompts` to discover capabilities at connection time.

**Azure integration** — Azure AI Foundry supports MCP servers as a tool source for agents. You can connect existing MCP servers (e.g., GitHub, Postgres) to a Foundry agent with minimal setup. Semantic Kernel and MAF also have native MCP support.

**Security** — MCP servers are a real attack surface. Key risks:
- **Prompt injection via tool output** — server returns content that manipulates the model.
- **Tool poisoning** — a malicious server defines a tool that looks innocent but does something else.
- **Rug pulls** — server changes a tool's definition after the user has approved it.
- **Excessive scope** — overly broad tools.

Mitigations: require explicit user consent for tool calls (especially destructive ones), pin server versions, validate tool schemas, sandbox server execution, restrict server network/file permissions, and treat all tool outputs as untrusted.

## Interview Q&A

**Q1. Why did MCP take off so quickly?**
A: It solved an obviously painful problem (custom integration per AI app per data source) with a simple, well-designed protocol borrowed from the proven Language Server Protocol pattern. Anthropic open-sourced it with strong reference implementations and SDKs. OpenAI, Google, Microsoft all adopted it within months — once the major model providers agreed, every dev tool followed. Network effects: more servers → more useful clients → more clients → more incentive to build servers. By 2026 it's the universal tool layer.

**Q2. Explain the three MCP primitives.**
A: **Tools** — model-driven actions. The LLM decides when to call them based on what the user asks. Examples: `search_repos`, `create_pull_request`, `query_db`. **Resources** — app-driven context. The host (e.g., VS Code) pushes things like "the currently open file" or "the user's selected text" to the model. **Prompts** — user-driven templates. The user invokes them as commands (slash-commands in Claude, for instance) to apply a pre-built workflow. Tools = "the model uses it," resources = "the app provides it," prompts = "the user triggers it."

**Q3. How is MCP different from function calling?**
A: Function calling is a per-API mechanism for an LLM to invoke developer-defined functions — each AI app implements its own tools. MCP is a *protocol* that standardizes the interface, so the same tool implementation works across many LLM hosts. Under the hood the model still does function calling; MCP defines how tools are discovered, advertised, called, and how results are returned — at a level above any single provider's API. Think: function calling is the engine; MCP is the standard car port.

**Q4. How do you use MCP in Azure AI Foundry?**
A: Foundry's agent service supports adding MCP servers as a tool source. Steps: provision an agent in Foundry; add an MCP server (either a hosted endpoint URL or a deployed container); the agent inherits the server's tools, resources, and prompts; configure auth and consent policy. The agent uses MCP tools just like native ones. Lifecycle: pin server versions, monitor tool calls in Foundry's tracing, enforce content safety on tool outputs.

**Q5. What are the main security risks with MCP and how do you mitigate them?**
A: (1) **Indirect prompt injection** — a server returns tool output containing instructions that hijack the model. Mitigation: treat all tool output as untrusted; do not let it influence which tools are called next without explicit policy. (2) **Tool poisoning** — malicious tool definitions. Mitigation: pin server versions, code-review server descriptions, run servers in sandboxes. (3) **Rug pull** — server updates change tool behavior post-approval. Mitigation: pin versions, hash tool schemas, re-prompt user on changes. (4) **Excessive permissions** — overly broad tools. Mitigation: principle of least privilege, narrow tools, human-in-the-loop on destructive actions. The OWASP MCP cheat sheet codifies these.

**Q6. Local vs remote MCP servers — when do you use each?**
A: Local (stdio) for tools that need access to the user's machine (file system, local databases, browser automation). Cheap, simple, no network. Remote (HTTPS) for shared enterprise services (your company's CRM, ticketing system, data warehouse). Authenticated, hostable in Azure, accessible to multiple users. Both can coexist; many production setups have a mix.

**Q7. How does MCP relate to LangGraph and the Microsoft Agent Framework?**
A: They're complementary. MCP is the *tool/integration layer*; LangGraph and MAF are *agent runtimes*. An agent built in LangGraph or MAF discovers and calls tools via MCP — it doesn't matter if the tools live in GitHub's MCP server or your custom Postgres MCP server. The runtime handles orchestration, state, planning; MCP handles the connection to the world. This decoupling is what's letting the ecosystem scale.

## Official References
- MCP official site: https://modelcontextprotocol.io
- MCP specification: https://modelcontextprotocol.io/specification/latest
- Anthropic introduction blog: https://www.anthropic.com/news/model-context-protocol
- MCP GitHub: https://github.com/modelcontextprotocol
- Reference servers: https://github.com/modelcontextprotocol/servers
- Azure AI Foundry + MCP: https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/tools/model-context-protocol
- OWASP MCP security: https://genai.owasp.org/

## Hands-on
Spin up the official reference MCP filesystem server. Connect it to Claude Desktop (or VS Code MCP support). Issue commands to list, read, write files. Then write a tiny custom MCP server (10 minutes with the Python SDK) that exposes one tool — e.g., `weather(city)` — and connect it.

---

# Day 13 — Microsoft Foundry Deep Dive

## Topics
- What is Microsoft Foundry (formerly Azure AI Foundry)
- Resource hierarchy: tenant, resource group, Foundry resource, project
- Model catalog (Azure OpenAI, partners, open-source)
- Foundry Agent Service
- Prompt Flow / agentic workflows
- Foundry IQ knowledge integration (grounding)
- Evaluation in Foundry
- Content safety and responsible AI
- Deployment options (managed online endpoints, serverless, containerized)
- Foundry vs Foundry (classic) — what changed

## Concept Explanations

**Microsoft Foundry** (rebranded from Azure AI Foundry in 2026) is the unified Azure PaaS for enterprise AI. It consolidates Azure OpenAI, AI Studio, agents, evaluations, content safety, and grounding under one resource provider with unified RBAC, networking, and policies.

**Resource hierarchy:**
- **Foundry resource** — top-level Azure resource (replaces the older Azure OpenAI resource pattern; existing Azure OpenAI resources can be auto-upgraded).
- **Project** — the development unit. Self-serve, isolated, where models are deployed, agents created, evaluations run, files stored. Multiple projects per resource.

**Model catalog** — one of Foundry's superpowers. Hundreds of models from Microsoft, OpenAI, DeepSeek, Hugging Face, Meta, Mistral, Cohere, Fireworks, and others. Deployable as **standard (serverless API)** or **managed compute** (your own GPU instance for self-hosted). Side-by-side comparison and evaluation built in.

**Foundry Agent Service** — managed agent runtime. Agents are defined with: a model, instructions, tools (functions, file search, code interpreter, Bing search, connected actions, MCP servers), and grounding data. Foundry handles threads (conversation state), tracing, content safety, and lifecycle. Agents can be published to Microsoft 365 / Teams / Copilot Studio.

**Prompt Flow** (now part of agentic workflows) — visual + code orchestration. A flow is a DAG of nodes (LLM calls, Python, prompts, embeddings, vector lookups). Versioning, variants for A/B, bulk evaluation, and one-click deployment as an endpoint.

**Foundry IQ** — knowledge grounding. Connect enterprise data (Azure AI Search, Cosmos DB, blob, SharePoint, Fabric, etc.) so agents answer with citations from authoritative sources. Built-in RAG pattern.

**Evaluation** — first-class. Built-in metrics (groundedness, relevance, coherence, fluency, safety categories, indirect attack), plus custom evaluators. Runs as part of CI/CD; gating on regression.

**Content Safety** — input and output classifiers for hateful, sexual, violent, self-harm content; jailbreak detection (prompt shields); protected material detection; PII handling. Configurable severity thresholds. Logs to audit trails.

**Deployment options:**
- **Standard (serverless)** — pay per token, no infra to manage. Default for most.
- **Provisioned (PTU)** — reserved capacity for latency-sensitive workloads.
- **Managed compute** — dedicated GPU instances for self-hosted models.
- **Foundry Local** — run models on edge / on-device (Foundry Local SDK, new in 2026).

**What changed in 2026** — the platform was rebranded from "Azure AI Foundry" to "Microsoft Foundry." Existing Azure OpenAI resources can be auto-upgraded to Foundry resources without changing endpoints or keys. The newer "Foundry (2.x)" SDK is the default; the older "Foundry classic / 1.x" is still supported for in-flight projects but new development should use 2.x.

## Interview Q&A

**Q1. What's the difference between an Azure OpenAI resource and a Foundry resource?**
A: A Foundry resource is the unified successor — same Azure OpenAI models plus everything else (model catalog, agents, evaluations, content safety, grounding) under one resource provider with unified RBAC and policies. Migration is supported via auto-upgrade preserving endpoints and keys. For new projects in 2026, create a Foundry resource directly. Azure OpenAI standalone resources are still supported but Foundry is the strategic direction.

**Q2. Walk through building an enterprise agent in Foundry.**
A: (1) Create a Foundry resource and project. (2) Deploy a model (e.g., GPT-4o) in the project. (3) Provision a Foundry agent: set instructions (system prompt), pick the model, attach tools (file search over uploaded PDFs, Bing search, custom functions hosted in Azure Functions, MCP server for internal CRM). (4) Connect grounding data via Foundry IQ — Azure AI Search index with ACL-aware filters. (5) Configure content safety thresholds. (6) Create an evaluation set; run built-in groundedness and safety evaluators. (7) Deploy to a managed endpoint; integrate with the front-end via SDK. (8) Enable tracing and monitor in the Foundry portal.

**Q3. When do you use Prompt Flow vs writing code directly?**
A: Prompt Flow shines when (a) the orchestration is complex enough that visualizing helps, (b) multiple roles (PMs, data scientists, engineers) need to collaborate, (c) you want built-in evaluation variants and bulk testing. Direct code is preferable when (a) the logic is simple and a flow adds friction, (b) you need very custom Python that fights the flow abstraction, (c) your team is engineering-only and prefers git-first workflows. Many teams prototype in Prompt Flow, then promote stable flows or rewrite as code services in production.

**Q4. How does grounding work in Foundry and how do you tune it?**
A: Foundry's "Add your data" or Foundry IQ pattern wires up a retriever (typically Azure AI Search) to the model. At inference, the user query is rewritten, retrieves top-k chunks, the chunks are injected into a system message with strict grounding instructions, the model answers and emits citations. Tuning levers: chunking strategy, embedding model, hybrid search on/off, semantic ranker on/off, top-k, strictness of grounding prompt, evaluation thresholds for groundedness in CI/CD. Watch the groundedness score — if it's low, fix retrieval before fixing the prompt.

**Q5. What content safety categories does Foundry provide?**
A: Built-in: hate, violence, sexual, self-harm (each with severity 0–7 thresholds), jailbreak detection (prompt shield), indirect attack detection (prompt injection via documents), protected material (copyrighted content reproduction), and groundedness (detects ungrounded claims). PII detection and redaction is available. You can also bring custom evaluators. Apply on input, output, or both; log scores for audit.

**Q6. How do you do CI/CD for agents in Foundry?**
A: Foundry agents are addressable via SDK and configurable as code. The pattern: define agent config (instructions, tools, model, knowledge sources) in source-controlled files; have a pipeline that (a) deploys to a dev project, (b) runs the evaluation set, (c) gates promotion on metrics, (d) deploys to staging with shadow or canary traffic, (e) promotes to production. Use distinct Foundry projects per environment. Tracing data feeds back into eval sets — failures become test cases.

**Q7. When do you use serverless deployment vs managed compute vs Foundry Local?**
A: Serverless (standard) for everything by default — no infra, pay-per-token. Managed compute when you need a model that's only available as self-hosted (e.g., a fine-tuned open-source model), or for very high steady volume where dedicated GPUs are cheaper. Foundry Local for edge / on-device scenarios — privacy-sensitive workloads, offline operation, low-latency requirements, or where data can't leave a customer's environment. New for 2026.

## Official References
- Microsoft Foundry overview: https://learn.microsoft.com/en-us/azure/foundry/what-is-foundry
- Foundry documentation home: https://learn.microsoft.com/en-us/azure/foundry/
- Foundry quickstart: https://learn.microsoft.com/en-us/azure/foundry/quickstarts/get-started-code
- Foundry Models overview: https://learn.microsoft.com/en-us/azure/foundry/concepts/foundry-models-overview
- Foundry Agent Service: https://learn.microsoft.com/en-us/azure/foundry/agents/overview
- Azure AI Content Safety: https://learn.microsoft.com/en-us/azure/ai-services/content-safety/overview
- Foundry "What's new": https://learn.microsoft.com/en-us/azure/foundry/whats-new-foundry

## Hands-on
Build an agent in Foundry that answers questions over a small document set (upload 5–10 PDFs to the agent's file search). Run the built-in evaluation suite (groundedness, relevance). Deploy to an endpoint. Call it from a Python script via the Foundry SDK.

---

# Day 14 — LLMOps, Safety, Monitoring

## Topics
- LLMOps vs MLOps — what's different
- Prompt and model versioning
- Evaluation in CI/CD
- Drift and quality monitoring in production
- Cost monitoring and optimization
- Latency optimization (streaming, caching, model selection)
- Security: prompt injection, data exfiltration, PII handling
- Responsible AI: fairness, transparency, accountability
- Disaster scenarios and rollback

## Concept Explanations

**LLMOps** extends MLOps with the things LLM apps add: prompts as artifacts, non-deterministic outputs, vendor model dependencies, retrieval pipelines, agent orchestration, content safety, and human-in-the-loop. Many traditional MLOps concepts (registry, deployment, monitoring) still apply but with adapted semantics.

**Prompt versioning** — version-controlled with the code, tagged with semver. Each version has an eval-suite result attached. Rolling back is just deploying the prior version.

**Model versioning** — closed models (Azure OpenAI, Anthropic) version externally; pin the exact model version (e.g., `gpt-4o-2024-08-06`) in production, never use floating tags. When upstream releases a new version, run your eval suite against it before promoting. Open models you self-host: standard model registry pattern.

**CI/CD for LLM apps** — on code merge: lint, unit test, run prompts against the eval suite, gate on metric thresholds (no regression on faithfulness, accuracy, safety). On release: deploy to staging, smoke test, canary, full rollout.

**Production monitoring layers:**
1. **Operational** — latency p50/p95/p99, error rates, RPS, token throughput. Standard SRE.
2. **Quality** — sampled outputs scored by LLM-as-judge; trend by day; alert on regression.
3. **Drift** — input distribution shifts (new topics, query types), output drift (length, sentiment changes).
4. **Safety** — content safety incident rate by category; jailbreak attempt rate; protected material detections.
5. **Cost** — tokens consumed per feature per user; cost per task; cache hit rate.
6. **User signals** — thumbs up/down, edit rate, escalation rate, task completion.

**Cost optimization tactics:**
- **Right-size the model** — route easy queries to cheaper models; reserve frontier models for hard cases.
- **Cache** — prompt caching (provider-side), semantic cache (your-side) on repeat queries.
- **Compress prompts** — remove redundant instructions, use shorter examples.
- **Streaming** — same cost, better UX, lets you cancel early if needed.
- **Provisioned throughput** for predictable steady volume.
- **Quantize self-hosted models** — int8/int4 for 2–4× memory savings.

**Latency optimization:**
- Stream responses.
- Cache aggressively.
- Smaller/faster models where quality permits.
- Parallel tool calls (modern APIs support this).
- Reduce context length — bloat slows everything.

**Security:**
- **Prompt injection** — defense in depth: separate trusted/untrusted content, content safety on input, restrict tool capabilities, human-in-the-loop on destructive actions.
- **Data exfiltration** — be wary of agents that can both read sensitive data and call external tools (email, web) — those can become exfil channels via prompt injection.
- **PII** — detect and redact before logging; encryption at rest and in transit; data residency via region selection in Foundry.
- **Auth** — managed identities throughout; no keys in code; key vault for secrets; per-user RBAC propagated into retrieval filters.

**Responsible AI** — Microsoft's framework covers fairness, reliability and safety, privacy and security, inclusiveness, transparency, and accountability. Foundry's responsible AI tooling supports each: fairness assessments, content safety, audit logging, model cards, and explainability where available.

## Interview Q&A

**Q1. What's different about LLMOps compared to traditional MLOps?**
A: Three big differences. (1) **Artifacts** — prompts, retrieval indexes, and agent configurations join models as first-class versioned artifacts. (2) **Non-determinism** — outputs vary across runs, so "the same input must produce the same output" tests don't apply; you need probabilistic eval. (3) **External dependencies** — closed-model providers update models on their schedule; you need pinning and continuous re-evaluation. Plus: evaluation is much harder (open-ended outputs), safety is a first-class concern (content filters, prompt injection), and you often combine multiple LLM calls (chains, agents) which compound failure modes.

**Q2. How do you pin model versions in production?**
A: Explicitly reference the dated model version, not the floating tag. In Azure OpenAI: deploy a specific model version (e.g., `gpt-4o-2024-08-06`) as a named deployment; reference the deployment name in code. Never use a tag that auto-upgrades. When Microsoft announces a new version, run your eval suite against the new version, compare against current, only promote if safe. The provider's auto-update notices (Azure has deprecation policies) drive your re-evaluation cadence.

**Q3. Walk through monitoring an LLM app in production.**
A: Per-call telemetry: prompt version, model version, tokens in/out, latency, cost, request ID, user/session ID, content safety scores. Aggregate dashboards on each. Plus a continuous offline evaluator: sample 1–5% of production traffic, run LLM-as-judge for quality, surface low-scoring conversations for review. Drift alerts on input distribution (new topic clusters appearing). Cost dashboards by feature/user. Tools: Application Insights, Foundry tracing built-in, Langfuse / LangSmith for richer agent traces. Set SLOs and alert when breached.

**Q4. A user reports the chatbot leaked internal data. Walk me through the response.**
A: Triage: confirm with logs (every chat is traced and stored, right?). Identify root cause — was it retrieved from a source the user shouldn't have accessed (ACL bypass), or did the model hallucinate it (less common but possible), or was it a prompt injection? Immediate mitigation: disable the affected feature or apply a content filter. Investigate access controls — Entra ID groups, search index ACL filters, document permissions at source. Fix the broken link in the access chain. Communicate to affected users per compliance policy. Add a regression test so the same leak can't recur. Post-mortem with no blame on individuals.

**Q5. How do you reduce LLM costs by 50% without hurting quality?**
A: Several stacked optimizations. (1) **Routing** — small LLM classifies query difficulty; only hard queries go to frontier model. Often 30%+ savings. (2) **Prompt caching** — for repeated system prompts and retrieved context. 10–25% on cached tokens, big savings for chat with stable context. (3) **Prompt trimming** — audit prompts; remove dead instructions and redundant examples. (4) **Output length control** — explicit limits, structured outputs to avoid verbose prose. (5) **Better retrieval** — fewer, more relevant chunks beats stuffing 20. (6) **Provisioned throughput** when steady-state — predictable price. Measure each change against the eval suite to ensure quality holds.

**Q6. What's your defense against prompt injection?**
A: Layered. (1) **Architectural separation** — system instructions are clearly demarcated from untrusted input; the model is instructed that retrieved content is data, not instructions. (2) **Content safety** — Foundry's prompt shields detect both direct injection and indirect injection (via documents). (3) **Tool restriction** — agents can only call tools that match the user's permission scope; destructive tools require human-in-the-loop. (4) **Output filtering** — scan outputs for sensitive data patterns before returning. (5) **Audit** — log all tool calls with input and output for forensic review. No single defense is sufficient; the depth-in-defense mindset is essential.

**Q7. How do you roll back a bad LLM deployment?**
A: Same primitives as any service rollback but a bit more involved. **Prompt rollback** — re-deploy prior prompt version (fast). **Model rollback** — switch Azure OpenAI deployment to prior model version (also fast if you kept the prior deployment running). **Retrieval rollback** — if the issue is in the index (bad chunking, bad embeddings), keep the previous index alongside the new one and swap pointers. Blue-green or canary deployments make all of this safer. Always practice rollback in staging — the first time you do it shouldn't be at 3am during an incident.

## Official References
- Azure ML model monitoring: https://learn.microsoft.com/en-us/azure/machine-learning/concept-model-monitoring
- Foundry tracing and observability: https://learn.microsoft.com/en-us/azure/foundry/concepts/tracing
- Responsible AI Standard (Microsoft): https://www.microsoft.com/en-us/ai/responsible-ai
- Azure AI Content Safety: https://learn.microsoft.com/en-us/azure/ai-services/content-safety/overview
- OWASP LLM Top 10: https://genai.owasp.org/llm-top-10/
- Anthropic safety best practices: https://docs.anthropic.com/en/docs/safety-best-practices

## Hands-on
Take your Day 13 Foundry agent. Add tracing, set up a content safety policy, configure a small eval suite to run nightly. Simulate a regression (degrade a prompt) and confirm the eval catches it before promotion.

---

# Day 15 — Fine-Tuning, Multimodal, Capstone

## Topics
- When to fine-tune
- Fine-tuning techniques: full, LoRA / QLoRA, PEFT
- Data preparation for fine-tuning
- Fine-tuning on Azure OpenAI and Foundry
- Distillation
- Multimodal: vision (GPT-4o, Claude with vision, Gemini), audio (Whisper, real-time voice), video
- Foundry Local and on-device AI
- Putting it all together — a capstone project
- Interview prep checklist

## Concept Explanations

**When to fine-tune** (a strict checklist):
- You've exhausted prompt engineering and few-shot.
- RAG isn't the right fit (the task is about *behavior*, not *knowledge*).
- You have at least a few hundred high-quality examples (1k–10k ideal).
- You expect to use the fine-tuned model heavily (the up-front cost amortizes).
- You need consistent style, format, or domain reasoning that prompting can't reliably produce.

**Fine-tuning approaches:**
- **Full fine-tuning** — update all model weights. Highest quality, most expensive, requires significant data and compute.
- **LoRA (Low-Rank Adaptation)** — train small "adapter" matrices added to existing weights; the base model stays frozen. ~1% the trainable parameters, 90%+ the quality on most tasks. The default for fine-tuning in 2026.
- **QLoRA** — LoRA on quantized base models (4-bit). Lets you fine-tune large models on a single GPU.
- **PEFT (Parameter-Efficient Fine-Tuning)** — umbrella term covering LoRA and other techniques (adapters, prefix tuning).

**Data prep** is 80% of the work. Each example is a (input, ideal_output) pair. Quality > quantity — 500 great examples often beat 5,000 mediocre ones. Use a strong model to generate or refine training data (distillation), then human-review.

**Distillation** — train a smaller model to mimic a larger one. Common pattern: use GPT-4 to generate high-quality (input, output) pairs on your task; fine-tune a smaller open-source model (Phi-4, Llama 3 8B) on those pairs. Result: a small, fast, cheap model that performs near the teacher on your domain.

**Azure fine-tuning** — Azure OpenAI supports fine-tuning of select models (GPT-4o-mini, GPT-4o, GPT-4.1, and others). Upload JSONL with `{messages: [...]}` per line. Foundry adds fine-tuning of open-source models on managed compute, plus the Azure Developer CLI fine-tuning extension (new in 2026) for streamlined workflows.

**Multimodal:**
- **Vision** — GPT-4o, Claude (with vision), Gemini accept images alongside text. Used for OCR-plus-reasoning, chart understanding, UI testing, accessibility, document understanding.
- **Audio in/out** — Whisper for speech-to-text; OpenAI Realtime API and Azure equivalent for low-latency voice agents (speech-in, speech-out).
- **Video** — Gemini and frontier models support video input via frame sampling.
- **Image generation** — DALL-E 3, Stable Diffusion, Flux. Available in Foundry.

**Foundry Local** — new in 2026, run models on-device or edge. Use cases: privacy-sensitive workloads (data never leaves the device), offline operation, low-latency mobile/embedded, or hybrid where edge handles routine and cloud handles complex.

## Interview Q&A

**Q1. When should you fine-tune instead of prompt-engineer or RAG?**
A: Fine-tune for **behavior change** — consistent style, format, domain reasoning patterns, specialized output structure — when prompting can't reliably produce it. Don't fine-tune for **facts** — that's what RAG is for. Concretely: if you need 99% format consistency that few-shot can't hit, fine-tune. If you need to teach a small fast model the patterns a large model produces (distillation), fine-tune. If the task is to know specific facts about your business, RAG.

**Q2. Explain LoRA in plain terms.**
A: Full fine-tuning updates all of a model's billions of weights, which is expensive. LoRA observes that the *change* needed for a task is usually low-rank — it can be expressed as a small set of additional matrices added on top of the frozen base. So you train just those small "adapters" (often <1% of parameters), keeping the base frozen. Result: 100–1000× cheaper training, comparable quality, and the same base can host multiple adapters for different tasks. The default approach for fine-tuning open-source models in 2026.

**Q3. How do you prepare data for fine-tuning a chat model?**
A: JSONL with one example per line, each containing a list of messages: system, user, assistant. The assistant message is what the model learns to produce. Critical practices: (1) ensure diversity — cover the input distribution, not just easy cases; (2) quality over quantity — 500 great examples often beat 5,000 noisy; (3) match the prompt format you'll use at inference — train and serve must be identical; (4) hold out an eval set never seen during training; (5) check for label noise — if the same input has wildly different outputs in your data, the model learns inconsistency.

**Q4. What is distillation and why does it matter?**
A: Distillation trains a smaller "student" model to mimic a larger "teacher" model. Pattern: use the teacher (e.g., GPT-4) to generate high-quality (input, output) pairs on your task — possibly thousands or tens of thousands — then fine-tune a smaller model (Phi-4, Llama 3 8B) on those pairs. The student inherits much of the teacher's capability on your specific task while being far cheaper and faster at inference. It's how teams ship cost-effective production systems: do hard reasoning once with a frontier model, distill into a small fast model for serving.

**Q5. When do you use vision-capable models?**
A: Tasks where the visual content carries meaning that's hard to extract textually first: chart and graph understanding, UI testing and accessibility, document layout analysis beyond OCR, image classification with reasoning ("is this product damaged?"), screen-based agents that act on what they see. Combine with text reasoning — vision alone is rarely the answer; vision-plus-reasoning is the unlock.

**Q6. What's Foundry Local and where would you use it?**
A: Foundry Local is Microsoft's edge/on-device runtime for models, new in 2026. Use cases: privacy-critical workloads where data can't leave the device, offline operation (manufacturing floor, field service), low-latency mobile or embedded, hybrid deployments where local handles routine and cloud handles complex. Trades off frontier-model quality for control, privacy, and latency. The Foundry Local SDK and migration guide cover deployment patterns.

**Q7. Build me a capstone project that uses everything we've covered.**
A: An enterprise knowledge agent for a software company. **Architecture:** ADF pulls SharePoint docs and Confluence; Document Intelligence parses; structure-aware chunking; embedded with `text-embedding-3-large`; indexed in Azure AI Search with hybrid + semantic ranker + ACL filters from Entra. **Agent runtime:** Microsoft Foundry agent with tools — search the knowledge base (RAG), open Jira tickets, query the data warehouse, call internal APIs via MCP. **Orchestration:** LangGraph or MAF for multi-step reasoning with handoffs (planner → researcher → drafter → reviewer). **Evaluation:** Foundry eval suite with groundedness, relevance, safety; nightly runs against a 200-example test set. **Observability:** Foundry tracing into Application Insights; cost dashboards by team; safety incident alerts. **Deployment:** Foundry-managed endpoint with PTU for predictable latency; canary rollouts; pinned model versions; CI/CD via GitHub Actions. **Security:** managed identities throughout; PII redaction in logs; prompt shields enabled; human-in-the-loop on destructive tools.

## Official References
- Azure OpenAI fine-tuning: https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/fine-tuning
- Foundry fine-tuning: https://learn.microsoft.com/en-us/azure/foundry/concepts/fine-tuning-overview
- Azure OpenAI vision: https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/gpt-with-vision
- Azure AI Speech: https://learn.microsoft.com/en-us/azure/ai-services/speech-service/
- Foundry Local: https://learn.microsoft.com/en-us/azure/foundry/foundry-local/
- LoRA paper: https://arxiv.org/abs/2106.09685

## Hands-on
Pick one slice of the capstone — your choice. The most learning-dense option: take your Day 13 Foundry agent and add a fine-tuned small model as a "routing" classifier that decides whether queries need RAG, direct answer, or a tool call. Measure cost and latency improvements.

---

# Interview Prep Checklist

Print this. Tick each before any interview.

**Conceptual fluency:**
- [ ] Explain transformer attention with whiteboard sketch
- [ ] Pretraining → SFT → RLHF/DPO end-to-end
- [ ] When to use frontier vs reasoning vs small models
- [ ] Context window mechanics, KV cache, prompt caching
- [ ] Embeddings, distance metrics, ANN indexes
- [ ] RAG pipeline, RAGAS metrics, advanced patterns
- [ ] ReAct, planning agents, multi-agent patterns
- [ ] MCP architecture and use cases
- [ ] Microsoft Foundry resource hierarchy and capabilities
- [ ] LLMOps differences from MLOps

**Hands-on:**
- [ ] Deployed at least one Foundry agent end-to-end
- [ ] Built a RAG with Azure AI Search and hybrid retrieval
- [ ] Implemented function calling with proper error handling
- [ ] Set up evaluation suite with both reference-based and LLM-as-judge
- [ ] Connected an MCP server to a host
- [ ] Built a 2-agent LangGraph or MAF system
- [ ] Estimated cost for a hypothetical production app

**Stories ready:**
- [ ] One project where you chose RAG over fine-tuning and why
- [ ] One project where you debugged a quality regression
- [ ] One project where you optimized cost or latency significantly
- [ ] One project where you handled a safety or security concern
- [ ] One trade-off between flexibility (agent) and reliability (pipeline)

**Connect to your Azure background:**
- [ ] Tie RAG to your existing data engineering — ingestion is just another pipeline
- [ ] Tie LLMOps to your MLOps / DevOps experience
- [ ] Tie Foundry to your Azure platform fluency
- [ ] Tie governance / security to your enterprise Azure knowledge

---

# Cross-Cutting References

- **Microsoft Foundry hub**: https://learn.microsoft.com/en-us/azure/foundry/
- **Azure OpenAI Service**: https://learn.microsoft.com/en-us/azure/ai-services/openai/
- **Azure AI Search**: https://learn.microsoft.com/en-us/azure/search/
- **Microsoft Agent Framework**: https://learn.microsoft.com/en-us/agent-framework/
- **LangGraph**: https://langchain-ai.github.io/langgraph/
- **Model Context Protocol**: https://modelcontextprotocol.io
- **Anthropic docs**: https://docs.anthropic.com
- **OpenAI platform docs**: https://platform.openai.com/docs
- **Google AI for developers**: https://ai.google.dev/
- **Hugging Face learn**: https://huggingface.co/learn
- **Anthropic courses**: https://anthropic.skilljar.com/
- **Microsoft Learn AI paths**: https://learn.microsoft.com/en-us/training/browse/?products=azure&roles=ai-engineer
- **OWASP GenAI security**: https://genai.owasp.org/

---

# A Few Final Notes

**Pace yourself.** 15 days is tight. If you fall behind on one day, don't skip — defer. Better to take 18 days and absorb than 15 and skim.

**Leverage your background ruthlessly.** When something sounds new, ask: how is this just the AI-flavored version of something I already know? RAG ingestion is ADF. LLMOps is MLOps with text. Agent orchestration is workflow design. Vector indexes are just specialized indexes. This grounding accelerates everything.

**Build, don't just read.** Every day has a hands-on task. Skip the hands-on, lose half the value. Even 30 minutes of code beats 2 hours of theory.

**Cite your sources in interviews.** "According to Microsoft Foundry's documentation..." is much stronger than "I think..." Knowing where to look matters as much as memorization.

**The space changes weekly.** Add `learn.microsoft.com/en-us/azure/foundry/whats-new-foundry` to a feed. Microsoft, OpenAI, Anthropic, Google, LangChain all post frequently. Set 15 minutes a week as your ongoing learning budget post-Day 15.

Good luck. Your Azure depth is a serious advantage — most candidates entering GenAI lack it. Bring concrete project stories, defensible technical opinions, and the ability to reason about production trade-offs.
