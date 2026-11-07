# Model Research
## Development of Enterprise Workflow Platform with Decision Automation

**Document status:** Initial model research and proposed selection  
**Important:** Model availability, exact model IDs, free-tier quotas, pricing, and licenses can change. Verify them on the linked official pages before selecting a production model. No benchmark results are claimed in this document.

## 1. Research objective

The platform needs language-model capabilities for understanding business requests, planning tasks, summarizing research, producing structured outputs, and explaining recommendations. It also needs embedding models for semantic document retrieval and deterministic algorithms for calculations and business decisions.

A single LLM can initially power multiple agents through different prompts. Introduce separate models only when evaluation demonstrates a measurable benefit.

## 2. Large Language Model (LLM) candidates

| Candidate | Deployment | Possible role | Points to verify |
|---|---|---|---|
| Google Gemini Flash family | Hosted API | Initial model for planning, summarization, structured responses, and tool selection | Current model ID, API quotas, pricing, data policies, rate limits, and tool/function-calling support |
| Qwen3 family | Local or hosted, depending on variant/provider | Alternative for local experimentation and reasoning tasks | Model size, hardware requirements, license, context length, structured output and tool-use performance |
| Meta Llama 3.2 family | Local or hosted, depending on variant/provider | Lightweight classification, extraction, and summarization experiments | Exact variant, license, memory requirements, context length, and task performance |

### Proposed starting choice

Start with a Gemini Flash model through the API if the project has access and the current quota and data-handling terms are suitable. Keep model calls behind a provider interface so the model can be changed without rewriting agent logic.

Evaluate a small Qwen or Llama variant locally as a fallback or comparison. Select the exact variant only after checking the target computer's RAM/VRAM and running a practical latency test. Do not assume a local model will run multiple agents concurrently on limited hardware.

## 3. Embedding model candidates

Embedding models convert text into numerical vectors for semantic retrieval. They are not generative chat models and should not be used alone to make enterprise decisions.

| Candidate | Possible use | Notes |
|---|---|---|
| `sentence-transformers/all-MiniLM-L6-v2` | Lightweight English semantic-search baseline | Small and practical for a prototype; measure retrieval quality on the actual project documents |
| BGE-small English model family | Semantic document retrieval | Compare retrieval relevance and follow the chosen model card's usage instructions |
| Nomic Embed Text family | Text embeddings for retrieval | Check model-specific context limits, instructions, and license |

**Proposed starting choice:** `sentence-transformers/all-MiniLM-L6-v2` with Sentence Transformers and ChromaDB. Compare it against another candidate using the same corpus and test queries.

## 4. Model roles by agent

| Agent | Model use | Additional non-LLM logic |
|---|---|---|
| Planning Agent | Interpret request and return a structured task plan | Schema validation and allowed-task checks |
| Research Agent | Formulate retrieval queries and summarize retrieved passages | Embedding model, vector search, source tracking |
| Analysis Agent | Explain findings and identify patterns | Python, Pandas, NumPy, SQL for calculations |
| Decision Agent | Explain trade-offs and summarize recommendation | Rule engine, weighted scoring, MCDA |
| Execution Agent | Interpret tool result and produce status summary | Deterministic API clients, authorization, validation |
| Response Agent | Generate final natural-language report | Template and citation/source checks |

## 5. Decision-making methods

Do not rely on an LLM alone for calculations, access control, or mandatory business rules.

| Method | Application |
|---|---|
| Rule-based decision engine | Enforce eligibility, thresholds, and mandatory policies |
| Weighted scoring | Rank options using agreed criteria and weights |
| Multi-Criteria Decision Analysis (MCDA) | Compare options across cost, quality, risk, and time |
| Decision trees | Represent transparent branching rules |
| Random Forest (optional) | Predict outcomes only when suitable labelled data is available |

### Example: supplier selection

A supplier may be evaluated on price, quality, delivery time, and reliability. A normalized weighted score can be calculated as:

`Total Score = (w1 × Price Score) + (w2 × Quality Score) + (w3 × Delivery Score) + (w4 × Reliability Score)`

Weights should be agreed with the business stakeholder. The scoring calculation should be deterministic and recorded. The LLM can explain the result, but should not invent scores or weights.

## 6. Model comparison and evaluation plan

Run the same representative business requests against each candidate. Keep prompts, input data, tool definitions, and evaluation criteria consistent.

| Metric | Measurement approach |
|---|---|
| Answer correctness | Human review against a prepared answer key |
| Planning quality | Review completeness, ordering, and feasibility of generated plans |
| Structured output validity | Percentage of outputs passing JSON/schema validation |
| Tool selection accuracy | Correct tool selections divided by total tool decisions |
| Tool argument validity | Calls passing schema validation divided by total calls |
| Retrieval relevance | Precision@k or Recall@k on labelled document queries |
| Decision quality | Comparison with expert-reviewed recommendations or known outcomes |
| Task completion rate | Successfully completed workflows divided by total workflows |
| Latency | Time per model call and end-to-end workflow |
| Cost | API and infrastructure cost per completed workflow |
| Local resource use | Peak RAM/VRAM and tokens per second for local models |

Create a test set of approximately 20–30 representative requests as a starting point. Record model name/version, prompt version, input, output, latency, token usage, errors, and reviewer score. This is a proposed test size, not a completed experiment.

## 7. Recommended initial stack

| Component | Initial proposal |
|---|---|
| Main LLM | Gemini Flash API, subject to access and policy review |
| Local comparison | A hardware-appropriate Qwen3 or Llama 3.2 variant |
| Embeddings | `all-MiniLM-L6-v2` |
| Agent development | LangChain |
| Orchestration | LangGraph |
| Vector storage | ChromaDB |
| Relational storage | SQLite for prototype; PostgreSQL for deployment |
| Data analysis | Pandas and NumPy |
| Backend | FastAPI |
| Frontend | Streamlit |

## 8. Risks and mitigations

| Risk | Mitigation |
|---|---|
| Hallucinated facts | Ground research summaries in retrieved sources and validate outputs |
| Invalid structured responses | Use typed schemas, parsing, and bounded repair/retry |
| Wrong tool calls | Strict tool schemas, permissions, allowlists, and approval gates |
| Excessive latency or cost | Limit agent steps, set token budgets, and cache appropriate retrieval results |
| Local model does not fit hardware | Benchmark before adoption and use a hosted fallback |
| Sensitive data exposure | Minimize data, review provider terms, control access, and avoid secrets in prompts |
| Model version changes | Pin model identifiers where possible and rerun evaluation after changes |

## 9. Official research references

1. [Google Gemini API documentation](https://ai.google.dev/gemini-api/docs)
2. [Google Gemini API pricing](https://ai.google.dev/gemini-api/docs/pricing)
3. [Ollama documentation](https://ollama.com/)
4. [Ollama model library](https://ollama.com/library)
5. [Qwen model collection on Hugging Face](https://huggingface.co/Qwen)
6. [Meta Llama models on Hugging Face](https://huggingface.co/meta-llama)
7. [Sentence Transformers documentation](https://www.sbert.net/)
8. [Hugging Face model hub](https://huggingface.co/models)
9. [Chroma documentation](https://docs.trychroma.com/)
10. [LangGraph documentation](https://docs.langchain.com/oss/python/langgraph/overview)

## 10. Conclusion

The recommended prototype uses one hosted LLM for the language tasks, a compact embedding model for document retrieval, and deterministic Python logic for calculations and business rules. Local Qwen or Llama models should be treated as candidates for testing, not assumed replacements. Final selection should follow repeatable evaluation of quality, reliability, latency, cost, hardware fit, licensing, and data-handling requirements.
