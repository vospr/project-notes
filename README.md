# Andrey Popov: project notes

Study notes on Andrey's portfolio projects: the problem, how each part works, the design decisions and trade-offs, the general theory behind it, and a glossary. Code for the public projects is in the repos linked from https://vospr.github.io.

**How to use this index:** `index.json` has the routing data (keywords and typical questions per note). Find the topic below and read that one file. Each file covers one chapter and starts with an "In short" summary.

## 1. HR Matching (production ranking pipeline)

| Chapter | File | Summary |
|---|---|---|
| 1.1 The system: two-stage ranking (filter chain → multi-task neural ranker), batch vs online | `1.1-two-stage-ranking.md` | How the matching system works end to end: rule-based dbt filters cut about 78M candidate-position pairs to about 2M, then a multi-task neural ranker scores them. Batch nightly plus an online path with a training/serving skew risk. |
| 1.2 dbt on BigQuery: models, macros, tests, incremental MERGE | `1.2-dbt-on-bigquery.md` | How the SQL data layer was restructured: about 650 dbt models on BigQuery, shared macros removing about 900 duplicated lines, tests from none to about 148. Incremental MERGE cut bytes scanned 94% on one model but not on the other. |
| 1.3 Parity safety net (must explain without notes) | `1.3-parity-safety-net.md` | How I proved a large SQL refactor changed nothing: compile-diff, exhaustive checks of three functions, row-level parity on three final tables and an outcome check, while live sources drifted about 4 rows a minute. |
| 1.4 Rule 3 and the exclusion rules (must explain without notes) | `1.4-exclusion-rules.md` | Three business rules (skill, seniority, location) added as guarded SQL filters so pairs the staffing desk would reject are never scored. Covers NULL-safe logic, fail-open defaults and sizing by simulation because no live rows existed. |
| 1.5 Feature ablation | `1.5-feature-ablation.md` | How I tested whether a hand-built location-fit feature could be retired: permutation importance, retraining without it, segment checks and Integrated Gradients. The retrain decided it: effect -0.0012 NDCG, below the 0.002 threshold. |
| 1.6 What I'd do differently | `1.6-done-differently.md` | A candid review of what I'd change on the matching project: dbt unit tests, model contracts, pinned-input data diffs, cost caps and small merges. Budget estimates were off by 12x and 36x; four linked changes were still unmerged at departure. |

## 2. Verdaca (governed AI workflow)

| Chapter | File | Summary |
|---|---|---|
| 2.1 Ports and adapters (hexagonal) | `2.1-ports-and-adapters.md` | How the governed AI workflow is structured: ports (Protocols and frozen DTOs), Mem0 and Letta adapters, a composition root and contract tests run against each adapter. Key rule: the LLM port returns tokens, never dollars. |
| 2.2 Auth-first gateway | `2.2-auth-first-gateway.md` | The auth-first gateway: OIDC token verification with JWKS keys, nonce replay protection, per-user budgets and an allow-list, all before any model call. Explains the spend-blind bug: the budget gate read a meter that never saw real spend. |
| 2.3 LLM access and cost | `2.3-llm-access-and-cost.md` | How model cost is computed and recorded: LiteLLM calls, a per-million-token price table, Decimal arithmetic, stop reasons and truncation flags. A recorded run of four calls cost about $0.03; the reviewer calls were about 80% of spend. |
| 2.4 The deliberation loop and receipts | `2.4-deliberation-loop-and-receipts.md` | The core mechanism: producer, blind reviewer and synthesizer in four model calls, one backtrack, typed outcomes (answer, clarify, abstain, escalate) and a JSON receipt with a SHA-256 content hash. The hash is explicitly not a signature. |
| 2.5 MCP server and Claude Desktop | `2.5-mcp-server-and-claude-desktop.md` | How the workflow was exposed to Claude Desktop through an MCP server: FastMCP, six tools, stdio transport, logs redirected to a file. Called once end to end from Desktop (27 s, about $0.03); this path has no per-user auth. |
| 2.6 Testing and CI honesty | `2.6-testing-and-ci-honesty.md` | How testing and CI were audited: a false green (tests untracked, CI scoped to a sub-folder) exposed by a fresh clone with 36 failures, then fixed. Covers 1,453 CI kernel tests, AST gates and the no_waiver pin, and what green does not prove. |
| 2.7 What I'd do differently | `2.7-what-id-do-differently.md` | Honest retrospective: eight weeks of ports, gates and auth before the core ran once; the first real deliberation then needed only about 1,000 lines. Lists limits found (hedge-dropping, self-critique) and what to build first today. |

## 3. AI Adoption Measurement

| Chapter | File | Summary |
|---|---|---|
| 3.1 Why "felt faster" isn't evidence | `3.1-felt-faster-not-evidence.md` | Why 'AI made us faster' needs evidence: METR found developers believed they were 20% faster but were 19% slower, and DORA frames AI as an amplifier. Argues for a tiered metric set pairing every speed metric with a quality counter-metric. |
| 3.2 Unit of delivery = merged change | `3.2-unit-of-delivery-merged-change.md` | Defines the unit of delivery as a ticket with a merged pull request; on a first sample the Done column overstated delivery by about 20%. Covers the PR lifecycle, vote-proxy review timing and reporting distributions, not per-PR averages. |
| 3.3 Cost allocation | `3.3-cost-allocation-distributions.md` | How a fixed monthly AI tool subscription is allocated to tickets by cache-read token share, reported as median, quartiles and p90 with an explicit unattributed share. Allocated plus unattributed equals the bill; delivered tickets spanned about 170x. |
| 3.4 Counting bugs and reconciliation | `3.4-counting-bugs-and-reconciliation.md` | The token-counting bugs found before publishing any cost: Claude totals about 2x too high (usage repeated per content block) and Codex too low (counter resets on resume). Fixes, hashed frozen snapshots, an independent audit script and a public retraction. |
| 3.5 Cost anatomy of one ticket | `3.5-cost-anatomy-of-one-ticket.md` | Cost dissection of one outlier ticket: 89% of token volume came from one never-reset parent conversation; 13 subagents were only about 11%. Late turns cost about 8x early ones; fixes are phase resets, batching and disk artifacts. |
| 3.6 The scripts | `3.6-the-scripts.md` | The tooling: standard-library-only Python collectors for Claude and Codex logs, pull-request APIs and step events, with sanitised outputs, hashed snapshots and 160+ tests. Specifies a nine-field step-event contract; step attribution is mostly unbuilt. |

## 4. Seismic facies classification

| Chapter | File | Summary |
|---|---|---|
| 4.1 v1 post-mortem: why random labels broke everything | `4.1-v1-post-mortem.md` | Post-mortem of the first pipeline: labels were random numbers, so every metric sat at chance (accuracy 0.49 against 0.52 for always-majority). The drift check compared two halves of one sample and could not fail. Explains noise versus leakage. |
| 4.2 The F3 benchmark and patch classification | `4.2-f3-benchmark-patch-classification.md` | The rebuild's data and task: the public F3 North Sea facies benchmark, 32x32 patches labelled at the centre pixel. Test accuracy 0.717 but macro-F1 0.514; classes 4 and 5 are almost never found (recall 0.09 and 0.02). |
| 4.3 sklearn Pipeline served as-is | `4.3-sklearn-pipeline-served-as-is.md` | Training to serving as one artefact: a scikit-learn pipeline (scaler, PCA 32, gradient boosting) logged to MLflow and loaded unchanged by the API, so served predictions equal trained ones. Uses skops instead of pickle. |
| 4.4 Leak-free evaluation | `4.4-leak-free-evaluation.md` | How evaluation was made leak-free: contiguous section blocks with a 10-section gap, validation separate from the benchmark's test volume, the promotion gate reading validation only, and test1 scored once per run. |
| 4.5 Promotion gate and MLflow | `4.5-promotion-gate-and-mlflow.md` | The promotion gate and registry: a new model becomes champion only if it beats the majority baseline on accuracy and macro-F1 and the incumbent on macro-F1; ties lose. Uses MLflow aliases; rejected versions stay registered for audit. |
| 4.6 CI that can fail + what's missing | `4.6-ci-that-can-fail.md` | CI that can actually fail: make ci runs 27 tests plus a train, gate and serve demo on a committed fixture. Lists what is not built (drift monitoring, retraining, Docker) and what a real monitor needs (PSI, reference window). |

## 5. AI Coaching platform (team build)

| Chapter | File | Summary |
|---|---|---|
| 5.1 Architecture I joined | `5.1-architecture-i-joined.md` | The architecture of the coaching platform Andrey joined: Azure Functions v4 code on Container Apps, SPA and API on one origin behind nginx, Slack as a separate app, a provider switch with no silent fallback, and Sonnet-for-chat, Haiku-for-generation tiering. |
| 5.2 Release readiness | `5.2-release-readiness.md` | Release-readiness controls: a two-flag kill switch at the single model-call chokepoint, cross-organisation isolation tests with sentinels, an encryption-coverage census gate and post-deploy smoke tests with timer liveness. Written after all timers had silently been dead. |
| 5.3 Merge safety in an AI-assisted team | `5.3-merge-safety.md` | Merge safety for a team landing about five AI-written PRs a day: a silent-revert guard using git blame and ancestry, and wiring 575 frontend tests that never ran into CI. Both gates were proven able to fail and fail closed. |
| 5.4 Human-coaching integration (my ownership) | `5.4-human-coaching-integration.md` | Andrey's main slice: integrating human coaches from a separate partner platform via OAuth client credentials, a capability enable-list, service-aware error mapping and partner-first booking with a journal. His fix: cancel used the wrong id, so slots stayed blocked. |
| 5.5 My AI delivery method | `5.5-my-ai-delivery-method.md` | Andrey's personal AI delivery method: read-only routing of each ticket to one of seven process levels, separate contexts per phase, disposable clones, a code graph with a freshness check and a gated wiki. The effect is admitted to be unmeasured. |

## 6. MCP decision memo

| Chapter | File | Summary |
|---|---|---|
| 6.1 MCP fundamentals | `6.1-mcp-fundamentals.md` | MCP basics and how the spike works: JSON-RPC, tools versus resources versus prompts, stdio and Streamable HTTP, Zod schemas with structuredContent, and a stub coaching backend with two tools. Notes that the booking tool is not idempotent. |
| 6.2 Stateless vs stateful (interview drill §6) | `6.2-stateless-vs-stateful.md` | Why the MCP server keeps no state: a new server per request so any serverless instance can answer, versus sessions that need affinity (1/N chance of reaching the owner). Covers the July 2026 spec dropping sessions and the handle pattern. |
| 6.3 What to expose before identity and GDPR | `6.3-expose-before-identity-and-gdpr.md` | What to expose through third-party AI platforms before identity and a DPIA exist: reference data, single-turn advice and booking now; memory, progress and multi-turn deferred. Covers GDPR basics, prompt injection and a five-question scope-line test. |
| 6.4 Auth for MCP today | `6.4-auth-for-mcp-today.md` | What auth an MCP server needs today: act as an OAuth 2.1 resource server with Protected Resource Metadata, PKCE, audience-bound tokens, per-tool scopes and a client registration choice. The spike has no auth; the memo's API-key-first advice is critiqued. |

## 7. LangGraph concierge + routing eval

| Chapter | File | Summary |
|---|---|---|
| 7.1 LangGraph StateGraph | `7.1-langgraph-stategraph.md` | How the concierge works: a seven-node LangGraph StateGraph with two conditional edges and TypedDict state, and the sticky-route bug where turn 2 reused turn 1's route and no answer was printed. The fix resets 13 turn-scoped fields each turn. |
| 7.2 Rules-then-LLM routing | `7.2-rules-then-llm-routing.md` | Rules-then-LLM routing: regex rules decide clear cases free (threshold 0.72); otherwise one Haiku call must return a route with confidence of at least 0.75, else fall back to a clarifying question or human hand-off. A model call cost $0.00041 on Haiku versus $0.00134 on Opus. |
| 7.3 Building a routing eval | `7.3-building-a-routing-eval.md` | The routing evaluation: 40 labelled queries, three arms. Rules only 52.5%, with Opus 62.5%, with Haiku 65.0%; the 2.5-point gap is one row, not a result, and the whole LLM gain is 4-5 rag rows. Covers sample size and intervals. |
| 7.4 Testing without lying | `7.4-testing-without-lying.md` | How the test suite was made honest: 497 offline tests, but the first version had about 50 wording or layout tests and one test asserting the bug. Adds a three-turn session test, fakes only at the edges and README numbers pinned to data. |

## 8. Context-engineering template

| Chapter | File | Summary |
|---|---|---|
| 8.1 Context engineering | `8.1-context-engineering.md` | How the template works: a stateless dispatcher in CLAUDE.md, state in files, the write/select/compress/isolate moves and sized pipelines (Micro to Large). The full pipeline used 613,670 tokens on a small task versus an estimated under 100k for one prompt. |
| 8.2 Subagents, skills, dispatcher | `8.2-subagents-skills-dispatcher.md` | The seven subagents and their privileges: per-role tool allowlists (reviewers are read-only), a blind reviewer that sees only the diff, a 9-cell conflict matrix, size tiers and circuit breakers. A missing tool is a control; prompt rules are only requests. |
| 8.3 Hooks that really block | `8.3-hooks-that-really-block.md` | Hooks that actually block: the first version could never fire (wrong config shape, env vars instead of stdin JSON, exit 1 instead of 2). The fix has a red-then-green record (5 pass, 26 fail, then 31/31) and a live session where a write was blocked. |

## 9. MSc thesis, time-series forecasting

| Chapter | File | Summary |
|---|---|---|
| 9.1 Electricity price forecasting setup | `9.1-electricity-price-forecasting-setup.md` | The thesis setup: forecasting Swiss hourly day-ahead prices from 26,304 rows, with cleaning, lag features, an ADF stationarity test (-8.39), a chronological 70/30 split and HAC errors. Defines the day-ahead information set that same-day features violate. |
| 9.2 Models | `9.2-models-ols-ridge-lasso-arx-mlp.md` | The model ladder: OLS, Ridge, Lasso, ARX and a tuned MLP. Test MSE ran from 57.4 (tuned network) and 59.3 (Lasso) to 83.8 for ARX; gaps among the top models are smaller than the network's run-to-run spread. |
| 9.3 Forecast combination | `9.3-forecast-combination.md` | Forecast combination with equal, R2-weighted and constrained least squares weights. In-sample CLS scored MSE 55.4 against 57.4 for equal weights, but on 2024 the simple average beat every single model, illustrating the combination puzzle. |
| 9.4 My own bugs (an interviewer will find these) | `9.4-my-own-bugs.md` | The bugs Andrey found in his own thesis code: same-day maximum and production leakage (+14% MSE when fixed), combination weights fitted on the test period, the dummy-variable trap and misleading MAPE. The repository is kept frozen as submitted. |

## 10. Interview foundations (after Courses 1–4)

| Chapter | File | Summary |
|---|---|---|
| 10.1 LLM evaluation: golden sets, LLM-as-judge, metrics, sample size, regression suites | `10.1-llm-evals.md` | How to evaluate an LLM application: golden sets, programmatic scorers before LLM judges, judge biases and calibration against humans, confidence intervals and sample size (97 items for plus or minus 10 points), CI regression suites and FINMA-style tests. |
| 10.2 RAG fundamentals: chunking, hybrid retrieval, reranking, grounding, evaluation, when not to use it | `10.2-rag-fundamentals.md` | RAG fundamentals: chunking, embeddings, BM25 plus dense hybrid retrieval with RRF, cross-encoder reranking, grounding with citations and abstention, retrieval and generation metrics, failure modes and when not to use RAG. Framed for a Swiss bank. |
| 10.3 LLM system design: latency, cost, caching, fallbacks, structured outputs, guardrails, observability | `10.3-llm-system-design.md` | Whiteboard LLM system design for a bank's advisor assistant: latency and cost estimates, prompt caching, model tiering, retries and fallbacks, structured outputs, layered guardrails, observability and governance. Worked cost of about $0.016 per request. |
| 10.4 FINMA AI guidance, Swiss data protection and model risk management | `10.4-finma-ai-guidance-and-model-risk.md` | Swiss regulation for AI in finance: FINMA Guidance 08/2024 findings in seven areas, the revised FADP (DPIA, processors, automated decisions), model risk management concepts and how the EU AI Act reaches Swiss firms. |
| 10.5 Python without AI: the fundamentals an interviewer expects, a 4-week plan and 20 graded exercises | `10.5-python-without-ai.md` | Python fundamentals without an assistant: complexity table, comprehensions and generators, dict/set/collections, typing, dataclasses, exceptions, context managers, pytest and pandas, plus a 4-week 30-minute-a-day plan with 20 graded exercises. |
