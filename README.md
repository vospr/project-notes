# Andrey Popov: project notes

Study notes on Andrey's portfolio projects: the problem, how each part works, the design decisions and trade-offs, the general theory behind it, and a glossary. Code for the public projects is in the repos linked from https://vospr.github.io.

**How to use this index:** find the topic below and read that one file. Each file covers one chapter.

## 1. HR Matching (production ranking pipeline)

| Chapter | File | Covers |
|---|---|---|
| 1.1 The system: two-stage ranking (filter chain → multi-task neural ranker), batch vs online | `1.1-two-stage-ranking.md` | candidate generation vs ranking, multi-task learning, training/serving skew |
| 1.2 dbt on BigQuery: models, macros, tests, incremental MERGE | `1.2-dbt-on-bigquery.md` | ref/DAG, materializations, incremental strategies, bitemporal data, cost per bytes scanned |
| 1.3 Parity safety net (must explain without notes) | `1.3-parity-safety-net.md` | compile-diff, row-level parity on terminal marts, outcome stability check, why not intermediates |
| 1.4 Rule 3 and the exclusion rules (must explain without notes) | `1.4-exclusion-rules.md` | NULL-safe predicates, fail-open vs fail-closed, simulation sizing, redundancy with existing filters |
| 1.5 Feature ablation | `1.5-feature-ablation.md` | permutation importance, null control, retrain ablation, Integrated Gradients, seeds and variance |
| 1.6 What I'd do differently | `1.6-done-differently.md` | dbt unit tests, contracts, data diffs, cost caps, merge small |

## 2. Verdaca (governed AI workflow)

| Chapter | File | Covers |
|---|---|---|
| 2.1 Ports and adapters (hexagonal) | `2.1-ports-and-adapters.md` | ports, adapters, dependency rule, import-linter, contract tests per port |
| 2.2 Auth-first gateway | `2.2-auth-first-gateway.md` | OIDC, JWT/JWKS, replay protection, per-key budgets, why "spend-blind" happened |
| 2.3 LLM access and cost | `2.3-llm-access-and-cost.md` | LiteLLM, model pricing, cost per call, stop reasons, truncation |
| 2.4 The deliberation loop and receipts | `2.4-deliberation-loop-and-receipts.md` | producer / isolated reviewer / synthesizer, typed outcomes, JSON receipt, content hash vs signature |
| 2.5 MCP server and Claude Desktop | `2.5-mcp-server-and-claude-desktop.md` | MCP tools, stdio transport, FastMCP |
| 2.6 Testing and CI honesty | `2.6-testing-and-ci-honesty.md` | false green, test discovery, 1,453 kernel tests, what a test does not prove |
| 2.7 What I'd do differently | `2.7-what-id-do-differently.md` | core before scaffolding, process vs product ratio, limits found (hedge-dropping, self-critique) |

## 3. AI Adoption Measurement

| Chapter | File | Covers |
|---|---|---|
| 3.1 Why "felt faster" isn't evidence | `3.1-felt-faster-not-evidence.md` | METR-style studies, DORA metrics, counter-metrics |
| 3.2 Unit of delivery = merged change | `3.2-unit-of-delivery-merged-change.md` | why "Done" overstates, PR lifecycle |
| 3.3 Cost allocation | `3.3-cost-allocation-distributions.md` | fixed subscriptions → token share, distributions (median/IQR/p90), unattributed share |
| 3.4 Counting bugs and reconciliation | `3.4-counting-bugs-and-reconciliation.md` | double-counting, session resumes, frozen snapshots, retraction |
| 3.5 Cost anatomy of one ticket | `3.5-cost-anatomy-of-one-ticket.md` | context × turns, never-reset sessions, subagent cost |
| 3.6 The scripts | `3.6-the-scripts.md` | stdlib-only Python collectors, tests, what I'd add (step attribution) |

## 4. Seismic facies classification

| Chapter | File | Covers |
|---|---|---|
| 4.1 v1 post-mortem: why random labels broke everything | `4.1-v1-post-mortem.md` | label leakage vs noise, baseline comparison, tautological drift test |
| 4.2 The F3 benchmark and patch classification | `4.2-f3-benchmark-patch-classification.md` | facies, patches, class imbalance, macro-F1 vs accuracy |
| 4.3 sklearn Pipeline served as-is | `4.3-sklearn-pipeline-served-as-is.md` | scaler → PCA → gradient boosting, train/serve skew |
| 4.4 Leak-free evaluation | `4.4-leak-free-evaluation.md` | spatial blocks, validation vs test, selection bias, "test once" |
| 4.5 Promotion gate and MLflow | `4.5-promotion-gate-and-mlflow.md` | model registry, aliases (champion/challenger), baseline gate |
| 4.6 CI that can fail + what's missing | `4.6-ci-that-can-fail.md` | fixtures, make demo, monitoring and drift (not built) |

## 5. AI Coaching platform (team build)

| Chapter | File | Covers |
|---|---|---|
| 5.1 Architecture I joined | `5.1-architecture-i-joined.md` | Functions v4 on Container Apps, same origin, provider seam, LLM tiering |
| 5.2 Release readiness | `5.2-release-readiness.md` | go/no-go, kill switch, tenant isolation tests, encryption CI gate, smoke tests |
| 5.3 Merge safety in an AI-assisted team | `5.3-merge-safety.md` | silent-revert guard, CI gates, test wiring |
| 5.4 Human-coaching integration (my ownership) | `5.4-human-coaching-integration.md` | service-to-service auth, booking consistency, error mapping, ACS video |
| 5.5 My AI delivery method | `5.5-my-ai-delivery-method.md` | routing, separate contexts, code graphs, cost of the setup |

## 6. MCP decision memo

| Chapter | File | Covers |
|---|---|---|
| 6.1 MCP fundamentals | `6.1-mcp-fundamentals.md` | tools vs resources vs prompts, transports, structuredContent |
| 6.2 Stateless vs stateful (interview drill §6) | `6.2-stateless-vs-stateful.md` | sessions, serverless, handles, the July 2026 spec |
| 6.3 What to expose before identity and GDPR | `6.3-expose-before-identity-and-gdpr.md` | DPIA, data exposure to third-party AI, scope lines |
| 6.4 Auth for MCP today | `6.4-auth-for-mcp-today.md` | OAuth resource server, PRM, client registration |

## 7. LangGraph concierge + routing eval

| Chapter | File | Covers |
|---|---|---|
| 7.1 LangGraph StateGraph | `7.1-langgraph-stategraph.md` | nodes, edges, state, per-turn reset bug |
| 7.2 Rules-then-LLM routing | `7.2-rules-then-llm-routing.md` | confidence floor, fallback, cost per decision |
| 7.3 Building a routing eval | `7.3-building-a-routing-eval.md` | labelled set, per-route accuracy, model comparison (Haiku vs Opus) |
| 7.4 Testing without lying | `7.4-testing-without-lying.md` | tests that check docs vs behaviour, offline mocks |

## 8. Context-engineering template

| Chapter | File | Covers |
|---|---|---|
| 8.1 Context engineering | `8.1-context-engineering.md` | context window economics, memory files, role separation |
| 8.2 Subagents, skills, dispatcher | `8.2-subagents-skills-dispatcher.md` | least privilege, sizing pipeline depth |
| 8.3 Hooks that really block | `8.3-hooks-that-really-block.md` | hook contract (stdin JSON, exit 2), verification |

## 9. MSc thesis, time-series forecasting

| Chapter | File | Covers |
|---|---|---|
| 9.1 Electricity price forecasting setup | `9.1-electricity-price-forecasting-setup.md` | stationarity, ADF, Ljung-Box, HAC |
| 9.2 Models | `9.2-models-ols-ridge-lasso-arx-mlp.md` | OLS/Ridge/Lasso, ARX, MLP |
| 9.3 Forecast combination | `9.3-forecast-combination.md` | equal weights vs CLS, why the simple average won |
| 9.4 My own bugs (an interviewer will find these) | `9.4-my-own-bugs.md` | same-day leakage, in-sample weights, dummy trap, metric choice |

## 10. Interview foundations (after Courses 1–4)

| Chapter | File | Covers |
|---|---|---|
| 10.1 LLM evaluation: golden sets, LLM-as-judge, metrics, sample size, regression suites | `10.1-llm-evals.md` |  |
| 10.2 RAG fundamentals: chunking, hybrid retrieval, reranking, grounding, evaluation, when not to use it | `10.2-rag-fundamentals.md` |  |
| 10.3 LLM system design: latency, cost, caching, fallbacks, structured outputs, guardrails, observability | `10.3-llm-system-design.md` |  |
| 10.4 FINMA AI guidance, Swiss data protection and model risk management | `10.4-finma-ai-guidance-and-model-risk.md` |  |
| 10.5 Python without AI: the fundamentals an interviewer expects, a 4-week plan and 20 graded exercises | `10.5-python-without-ai.md` |  |
