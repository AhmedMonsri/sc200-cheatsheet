# Module: Introduction to generative AI and agents

> Learning path: [Mitigate threats using Microsoft Security Copilot](https://learn.microsoft.com/en-us/training/paths/sc-200-mitigate-threats-using-microsoft-copilot-for-security/) | Source: [Microsoft Learn module](https://learn.microsoft.com/en-us/training/modules/fundamentals-generative-ai/)
> Original time: ~32 min (6 units, excl. assessment) | Read time: ~3 min | Last verified: 2026-09-27

## TL;DR

- Generative AI is applied statistics and machine learning, not magic: models produce new text, code, and other content from natural-language input.
- LLMs (and smaller SLMs) learn relationships between tokens and generate a **completion** from a **prompt**, one predicted token at a time.
- Response quality depends on the prompt: system vs user prompts, conversation history, and **RAG** grounding all add context.
- **AI agents** = LLM + instructions + tools; they can act, not just answer, and can collaborate in multi-agent systems.
- This module is the conceptual foundation for the Security Copilot modules that follow in this learning path.

## Exam objectives covered

| Exam domain | Objective (paraphrased) |
|---|---|
| Respond to security incidents | Investigate with agentic AI, incl. embedded Security Copilot (foundational concepts only: LLMs, prompts, agents) |

## Large language models (LLMs)

> Unit: https://learn.microsoft.com/en-us/training/modules/fundamentals-generative-ai/3-language-models

- **LLM**: model encoding linguistic and semantic relationships across a vocabulary; **SLM** = smaller, more compact counterpart.
- LLMs are trained to produce **completions** from **prompts**, similar to a very powerful predictive-text feature.
- The model predicts the most probable next token by weighing which earlier tokens most influence it.

**Tokenization**

- Training text is split into **tokens**: whole words, sub-words (e.g. "un"), punctuation, and common character sequences.
- Each distinct token gets a unique integer ID; repeated tokens reuse their ID.
- Current LLM vocabularies contain hundreds of thousands of tokens.

**Transformer architecture**

| Block | Role |
|---|---|
| Encoder | Builds **embeddings** using attention (multi-head) and a fully connected neural network |
| Decoder | Uses embeddings to predict the next most probable token in a sequence started by a prompt; also uses attention and a feed-forward network |

- Each token starts with a **vector** of random values; real vectors have thousands of elements (dimensions).
- **Positional encoding** is added so the model knows where each token sits in the sequence.
- **Attention** weights surrounding tokens by their influence on the current token; **multi-head attention** processes vector elements in parallel.
- **Embeddings** = vectors with contextual, semantic meaning; tokens used in similar contexts point in similar directions.
- **Cosine similarity** measures how semantically close two embeddings are.
- Decoder training uses **masked attention**: tokens after the current one are hidden, and prediction errors adjust the weights.
- At inference, each predicted token is appended and the process repeats until the model predicts the end of the sequence.
- ⚠️ Exam tip: a token is not the same as a word; tokens include sub-words and punctuation.

## Prompts

> Unit: https://learn.microsoft.com/en-us/training/modules/fundamentals-generative-ai/6-writing-prompts

- **Prompt** = input to the model (question, command, or comment); the model's response is the **completion**.

| Prompt type | Purpose | Usually set by |
|---|---|---|
| System prompt | Defines behavior, tone, style, and constraints | The application |
| User prompt | Asks a specific question or gives an instruction | The user (or the app on the user's behalf) |

- The model answers user prompts while following the system prompt's guidance.
- **Conversation history**: apps include (often summarized) earlier prompts and completions in later prompts to keep context.
- **RAG (retrieval augmented generation)**: app retrieves relevant data (documents, emails) and adds it to the prompt so the answer is **grounded** in it.
- Without RAG, a model gives generic answers to organization-specific questions.

| Tip for better prompts | Meaning |
|---|---|
| Clear and specific | Explicit instructions beat vague wording |
| Context | State topic, audience, or format |
| Examples | Show the style you want |
| Structure | Ask for bullets, tables, or numbered lists |

- ⚠️ Exam tip: grounding via RAG is what turns a generic answer into one based on your organization's data.

## AI agents

> Unit: https://learn.microsoft.com/en-us/training/modules/fundamentals-generative-ai/7-agents

- **AI agent**: generative-AI app that reasons over natural language, automates tasks with tools, and acts based on context.

| Component | Role |
|---|---|
| Large language model | The "brain": language understanding and reasoning |
| Instructions | System prompt defining the agent's role and behavior ("job description") |
| Tools | How the agent interacts with the world: **knowledge** tools (search, databases) and **action** tools (send email, update calendar, control devices) |

- **Multi-agent systems**: several specialized agents collaborate (e.g. one gathers data, one analyzes, one acts).
- Agents communicate through prompts; generative AI decides which tasks are needed and which agent handles each.
- ⚠️ Exam tip: knowledge tools *retrieve information*; action tools *perform tasks*.

## Exercise - Explore generative AI

> Unit: https://learn.microsoft.com/en-us/training/modules/fundamentals-generative-ai/7a-exercise

- Hands-on lab (~15 min) in a chat playground with a generative AI model.
- Shows the effect of changing the system prompt, adding tools, and grounding the model with data.
- No new testable content beyond the previous units.

## Key terms

| Term | Meaning |
|---|---|
| Generative AI | AI that creates original content (text, code, images) from natural-language input |
| LLM | Large language model: encodes relationships between tokens to generate completions |
| SLM | Small language model: more compact relative of an LLM |
| Prompt | Input given to a language model |
| Completion | The model's generated response to a prompt |
| Token | Unit of vocabulary: word, sub-word, punctuation, or common character sequence |
| Embedding | Vector with contextual, semantic meaning for a token |
| Transformer | Model architecture with encoder and decoder blocks built on attention |
| Attention | Weights surrounding tokens by their influence on the current token |
| Multi-head attention | Attention computed over multiple vector elements in parallel |
| Masked attention | Decoder training technique that hides tokens after the current one |
| Positional encoding | Adds a token's position in the sequence to its input vector |
| Cosine similarity | Measure of semantic closeness between two vectors |
| System prompt | Sets model behavior, tone, and constraints |
| User prompt | Specific question or instruction for the model |
| RAG | Retrieval augmented generation: add retrieved data to the prompt |
| Grounding | Basing a response on data supplied in the prompt |
| AI agent | Generative-AI app with a model, instructions, and tools that can take actions |
| Multi-agent system | Specialized agents collaborating on a workflow via prompts |

## Exam traps

- System vs user prompt: system = behavior and constraints (set by the app); user = the specific request.
- Encoder vs decoder: encoder *creates embeddings*; decoder *predicts the next token*.
- Conversation history vs RAG: history reuses earlier turns; RAG retrieves *external* data to ground the answer.
- Knowledge vs action tools: retrieve information vs perform a task.
- Initial vector vs embedding: initial vectors are random; embeddings are learned and carry meaning.
- Agent instructions = a system prompt, not a separate mechanism.

## Top 3 takeaways

1. LLMs tokenize text, learn embeddings via transformer attention, and predict completions one token at a time.
2. Better answers come from better context: system prompts, conversation history, RAG grounding, and clear, specific, structured prompts.
3. An AI agent combines an LLM, instructions (system prompt), and knowledge/action tools; multiple agents can collaborate on complex workflows.
