# OSS Repository Audit for an Automated E-Book / Digital-Product Factory

**Audit date:** 2026-10-11 (all dates `YYYY-MM-DD`, Europe/Paris workstation)
**Auditor:** delegated worker agent `OssRepoAudit`
**Scope:** real open-source repositories that can be *reused* by an automated e-book / digital-product factory,
plus a recommended composition (what to import, what to copy as a pattern, what to avoid).
**Method:** `git clone --depth 1 --single-branch` into `C:/Users/DELL/AppData/Local/Temp/oss/repos/<owner>_<name>`,
then inspection of the **actual source tree** with `git log -1`, `ls`, `find`, `grep -n`, `awk`, `python` — file paths
and line numbers quoted below are from those working copies. Licences were read from the repository's **LICENSE /
LICENSE.md / license.txt file**, never from a badge. Every maintenance claim is backed by a commit SHA + committer date
from the clone. **No application code in `C:/Users/DELL/ipf-factory` was modified.**

## Method limits (read this before trusting any row)

- Clones are **shallow** (depth 1): history, release cadence and "last commit touching file X" were **not** measured except
  through the GitHub REST metadata endpoint (`/repos/{owner}/{repo}` → `pushed_at`, `open_issues_count`, `archived`).
- **Nothing was executed.** No repository was `pip install`ed, no pipeline was run, no test suite was executed.
  Claims about behaviour are *code-read* findings (VERIFIED-CODE), not runtime findings.
- Two network incidents: DNS resolution failures mid-batch (8 metadata fetches retried), and a broken WSL `bash`
  wrapper (`bash script.sh` → `execvpe(/bin/bash) failed`) that forced all inspection commands to run inline.
- `promptfoo/promptfoo` (~426 MB working tree) **failed to clone twice** (curl 56 / "early EOF" / invalid index-pack)
  in the first batches; a **third, isolated attempt succeeded** (`repos/promptfoo_reclone`, HEAD `579043a` @ 2026-10-11),
  so promptfoo *is* code-inspected below. The failed attempts stay in the ledger because they explain why it is
  documented late in this report.
- 38 directories exist under `repos/`: **36 unique repositories**, one duplicate re-clone (`docling_reclone`, after the
  first docling clone reported a broken branch) and the successful promptfoo retry. 7 scored rows are **metadata-only**
  (n8n, AutoGPT, Flowise, pandoc, quarto, chroma, firecrawl).
- The GitHub REST API was hit unauthenticated and hit its **60 req/h rate limit** once (blocking further API
  tree listing for `microsoft/agent-framework` and `letta-ai/letta-code`). Where that mattered it is flagged UNKNOWN.
- One accidental inclusion: `input-output-hk/iohk-nix` (Nix tooling, unrelated) — **discarded, not scored**.
  `IBM/ACE` was queried by mistake (0 stars, empty); the real EPUB accessibility checker is **`daisy/ace`**, which was
  then cloned and inspected.

---

## Scoring rubric (1–5, stated explicitly)

| Axis | 5 | 4 | 3 | 2 | 1 |
|---|---|---|---|---|---|
| **F — Technical fit** | Directly implements a factory-critical capability with reusable code | Partially covers it, or covers it with an adapter | Adjacent / pattern-only value | Only inspiration value | No fit |
| **M — Implementation maturity** | ≥300 tests or equivalent hardening, typed, versioned, stable API | Tests + stable core, some churn | Working but thin tests or single-maintainer | Prototype / alpha / notebook-grade | Not implemented |
| **N — Maintainability** | Commit on the default branch within ~30 days *and* ≥2 maintainers | Within ~3 months | 3–12 months stale | >12 months stale | Repo archived / gutted / dead |
| **L — Licence compatibility** | MIT / BSD / Apache-2.0, no field-of-use clause | MPL-2.0 (file-level copyleft, dependency-fine) | Source-available with commercial restrictions (BSL/SUL/Polyform/modified Apache) or CC-BY attribution obligations | AGPL-3.0 (network copyleft) | Non-free / no usable licence for a proprietary product |
| **I — Integration cost** | One `pip install`, in-process, no service | Library + a few transitive deps | Library + heavy ML deps **or** needs a daemon | Requires external service/cluster/jar/JVM/chromium | Requires multi-service infrastructure (DB+queue+workers) |
| **D — Demonstrated functionality** | Entry point → output traced in code *and* the path is covered by tests in-repo | Traced in code, tests exist elsewhere/sparse | Partially traced, gaps | Claimed in README, not traceable | Nothing runnable in the repo |

Score = sum of F, M, N, L, I, D (max 30). **F and L are non-substitutable** for the factory: a repo with AGPL (L=1)
cannot enter the proprietary product regardless of its total.

---

## (i) Ranked shortlist — 43 repositories scored (35 cloned & code-read, 7 metadata-only, 1 discarded)

| # | Repo | Licence (verified from file) | HEAD sha @ date | F | M | N | L | I | D | **Σ/30** |
|---|---|---|---|---|---|---|---|---|---|---|
| 1 | [openai/openai-agents-python](https://github.com/openai/openai-agents-python) | MIT | `125efa0` @2026-10-08 | 5 | 5 | 5 | 5 | 5 | 5 | **30** |
| 2 | [567-labs/instructor](https://github.com/567-labs/instructor) | MIT | `035c77b` @2026-10-10 | 5 | 5 | 5 | 5 | 5 | 5 | **30** |
| 3 | [Kozea/WeasyPrint](https://github.com/Kozea/WeasyPrint) | BSD-3-Clause | `369b153` @2026-10-01 | 5 | 5 | 5 | 5 | 5 | 5 | **30** |
| 4 | [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) | MIT | `799c2f0` @2026-10-11 | 5 | 5 | 5 | 5 | 4 | 5 | **29** |
| 5 | [promptfoo/promptfoo](https://github.com/promptfoo/promptfoo) | MIT | `579043a` @2026-10-11 | 4 | 5 | 5 | 5 | 4 | 5 | **28** |
| 6 | [assafelovic/gpt-researcher](https://github.com/assafelovic/gpt-researcher) | Apache-2.0 | `3582a68` @2026-10-10 | 5 | 4 | 5 | 5 | 4 | 4 | **27** |
| 7 | [deepset-ai/haystack](https://github.com/deepset-ai/haystack) | Apache-2.0 | `c5e1335` @2026-10-09 | 4 | 5 | 5 | 5 | 4 | 4 | **27** |
| 8 | [MrBitBucket/reportlab-mirror](https://github.com/MrBitBucket/reportlab-mirror) | BSD-3-Clause | `c4c8334` @2026-08-19 | 4 | 5 | 4 | 5 | 5 | 4 | **27** |
| 9 | [pydantic/pydantic-ai](https://github.com/pydantic/pydantic-ai) | MIT | `69ea1e5` @2026-10-10 | 5 | 4 | 5 | 5 | 3 | 4 | **26** |
| 10 | [stanfordnlp/dspy](https://github.com/stanfordnlp/dspy) | MIT | `f1260ff` @2026-10-08 | 4 | 4 | 5 | 5 | 4 | 4 | **26** |
| 11 | [confident-ai/deepeval](https://github.com/confident-ai/deepeval) | Apache-2.0 | `4598fe8` @2026-10-09 | 4 | 4 | 5 | 5 | 4 | 4 | **26** |
| 12 | [microsoft/markitdown](https://github.com/microsoft/markitdown) | MIT | `4cc9fa1` @2026-10-03 | 4 | 4 | 4 | 5 | 5 | 4 | **26** |
| 13 | [temporalio/sdk-python](https://github.com/temporalio/sdk-python) | MIT | `fc5299b` @2026-10-09 | 4 | 5 | 5 | 5 | 2 | 5 | **26** |
| 14 | [jgm/pandoc](https://github.com/jgm/pandoc) | GPL-2.0 (badge only, not file-verified) | metadata @2026-10-10 | 4 | 5 | 5 | 3 | 4 | 5 | **26**¹ |
| 15 | [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) | MIT | `6b93fa0` @2026-10-09 | 4 | 4 | 5 | 5 | 3 | 4 | **25** |
| 16 | [dottxt-ai/outlines](https://github.com/dottxt-ai/outlines) | Apache-2.0 | `c52af84` @2026-08-24 | 4 | 4 | 4 | 5 | 4 | 4 | **25** |
| 17 | [docling-project/docling](https://github.com/docling-project/docling) | MIT | `21588f5` @2026-10-11 | 4 | 4 | 5 | 5 | 3 | 4 | **25** |
| 18 | [w3c/epubcheck](https://github.com/w3c/epubcheck) | BSD-3-Clause | `0cc28fb` @2026-10-01 | 4 | 5 | 4 | 5 | 3 | 4 | **25** |
| 19 | [dbos-inc/dbos-transact-py](https://github.com/dbos-inc/dbos-transact-py) | MIT | `d3f2de7` @2026-10-09 | 4 | 4 | 5 | 5 | 3 | 4 | **25** |
| 20 | [mem0ai/mem0](https://github.com/mem0ai/mem0) | Apache-2.0 | `b7ad69a` @2026-10-07 | 3 | 4 | 5 | 5 | 4 | 4 | **25** |
| 21 | [stanford-oval/storm](https://github.com/stanford-oval/storm) | MIT | `fb951af` @2025-09-30 | 5 | 4 | 3 | 5 | 4 | 4 | **25** |
| 22 | [quarto-dev/quarto-cli](https://github.com/quarto-dev/quarto-cli) | MIT (`COPYING.md` fetched via API) | metadata @2026-10-09 | 4 | 5 | 5 | 4 | 3 | 4 | **25**¹ |
| 23 | [rust-lang/mdBook](https://github.com/rust-lang/mdBook) | MPL-2.0 | `d465899` @2026-10-05 | 2 | 5 | 5 | 4 | 4 | 4 | **24** |
| 24 | [microsoft/agent-framework](https://github.com/microsoft/agent-framework) | MIT | `fd52de7` @2026-10-09 | 3 | 4 | 5 | 5 | 3 | 4 | **24** |
| 25 | [guidance-ai/guidance](https://github.com/guidance-ai/guidance) | MIT | `21b1d90` @2026-05-21 | 4 | 4 | 3 | 5 | 4 | 4 | **24** |
| 26 | [chroma-core/chroma](https://github.com/chroma-core/chroma) | Apache-2.0 (badge) | metadata @2026-10-10 | 3 | 4 | 5 | 5 | 3 | 4 | **24**¹ |
| 27 | [langgenius/dify](https://github.com/langgenius/dify) | Modified Apache-2.0 (**multi-tenant ban**) | `5fe7f018` @2026-10-11 | 4 | 4 | 5 | 3 | 2 | 5 | **23** |
| 28 | [aerkalov/ebooklib](https://github.com/aerkalov/ebooklib) | **AGPL-3.0** | `37e2a19` @2026-10-07 | 5 | 3 | 5 | 1 | 5 | 4 | **23** |
| 29 | [explodinggradients/ragas](https://github.com/vibrantlabsai/ragas) | Apache-2.0 | `298b682` @2026-02-24 | 4 | 4 | 2 | 5 | 4 | 4 | **23** |
| 30 | [guardrails-ai/guardrails](https://github.com/guardrails-ai/guardrails) | Apache-2.0 | `06d0ff2` @2026-08-26 | 3 | 4 | 4 | 5 | 3 | 4 | **23** |
| 31 | [daisy/ace](https://github.com/daisy/ace) | MIT | `dfa87b5` @2026-05-01 | 4 | 4 | 3 | 5 | 3 | 4 | **23** |
| 32 | [langfuse/langfuse](https://github.com/langfuse/langfuse) | MIT-Expat core + separate `ee/` commercial | `cb2ceba` @2026-10-10 | 4 | 5 | 5 | 3 | 2 | 4 | **23** |
| 33 | [restatedev/restate](https://github.com/restatedev/restate) | **BSL 1.1** (not OSI) | `752283e` @2026-10-09 | 4 | 5 | 5 | 2 | 2 | 4 | **22** |
| 34 | [Unstructured-IO/unstructured](https://github.com/Unstructured-IO/unstructured) | Apache-2.0 (+ in-code upsell/telemetry) | `e2f22b3` @2026-10-09 | 4 | 4 | 4 | 4 | 2 | 4 | **22** |
| 35 | [microsoft/autogen](https://github.com/microsoft/autogen) | **CC-BY-4.0** root; **MIT** in `LICENSE-CODE` | `027ecf0` @2026-04-06 | 3 | 4 | 2 | 4 | 4 | 4 | **21** |
| 36 | [firecrawl/firecrawl](https://github.com/firecrawl/firecrawl) | **AGPL-3.0** (file fetched via API) | metadata @2026-10-10 | 4 | 4 | 5 | 1 | 2 | 4 | **20**¹ |
| 37 | [google-deepmind/long-form-factuality](https://github.com/google-deepmind/long-form-factuality) | MIT (FactScore-derived) | `9d27158` @2024-04-07 | 4 | 3 | 1 | 5 | 3 | 3 | **19** |
| 38 | [n8n-io/n8n](https://github.com/n8n-io/n8n) | **Sustainable Use License** + `.ee` files | metadata @2026-10-11 | 2 | 5 | 5 | 1 | 2 | 4 | **19**¹ |
| 39 | [1rgs/jsonformer](https://github.com/1rgs/jsonformer) | MIT | `bfad031` @2023-05-30 | 2 | 2 | 1 | 5 | 4 | 3 | **17** |
| 40 | [mshumer/gpt-author](https://github.com/mshumer/gpt-author) | MIT | `179d86f` @2024-04-02 | 3 | 1 | 1 | 5 | 2 | 2 | **14** |
| 41 | [Significant-Gravitas/AutoGPT](https://github.com/Significant-Gravitas/AutoGPT) | **Polyform Shield** (`autogpt_platform/`) + MIT elsewhere | metadata @2026-10-11 | 1 | 3 | 5 | 1 | 1 | 2 | **13**¹ |
| 42 | [FlowiseAI/Flowise](https://github.com/FlowiseAI/Flowise) | Mixed: commercial licence for `enterprise/` | metadata @2026-08-13, **archived** | 2 | 2 | 1 | 3 | 2 | 3 | **13**¹ |
| 43 | [letta-ai/letta](https://github.com/letta-ai/letta) | Apache-2.0 (file exists; **repo contains no code**) | `5bcdd17` @2026-09-10 | 1 | 1 | 2 | 5 | 1 | 1 | **11** |

¹ **Metadata-only row** — repository was *not* cloned (size/network), so `M`/`D` rest on the GitHub API and the project's
public surface, not on code inspection. Treat as hypothesis, not finding.

**Deep-inspected (clone + code read):** **35 repositories** (every scored repo except the 7 metadata-only rows in
footnote ¹ and the 1 discarded accidental inclusion). Of these, **22 produced quoted file:line evidence**:
langgraph, openai-agents, instructor, pydantic-ai, dify, haystack, crewai, outlines, guidance, gpt-researcher, storm,
weasyprint, reportlab, markitdown, docling, **promptfoo**, epubcheck, ace, temporalio sdk, dbos, mem0, guardrails,
deepeval, ragas, unstructured, autogen, agent-framework, langfuse, mdBook, gpt-author, jsonformer,
long-form-factuality, **ebooklib**, restate, epubcheck/ace (validation pair).

---

## (iv) Licence classes vs a proprietary commercial product (read first)

| Class | Meaning for a closed-source, paid product | Repos found here |
|---|---|---|
| **MIT / BSD-2 / BSD-3 / Apache-2.0** | Safe to depend on and to ship inside a proprietary product (keep the notice/`NOTICE` file). Apache-2.0 adds a patent grant. | langgraph, openai-agents, instructor, pydantic-ai, dspy, markitdown, docling, weasyprint, reportlab, temporalio sdk, dbos, mem0, deepeval, ragas, gpt-researcher, haystack, crewai, outlines, guidance, storm, agent-framework, epubcheck, ace, mdBook(MPL), langfuse core |
| **MPL-2.0** | File-level (not project-level) copyleft. Fine as an *unmodified dependency*; if you copy mdBook source files into your product, those files stay MPL. | mdBook |
| **CC-BY-4.0** | **Not a software licence.** Attribution obligation on the licensed material. Applying it to *code* is unusual and legally murky; do not copy code licensed this way into a product without review. | microsoft/autogen root `LICENSE` (its code is separately **MIT** in `LICENSE-CODE`) |
| **Source-available with field-of-use restrictions** (BSL 1.1, Sustainable Use License, Polyform Shield, modified Apache) | **Not open source.** Usually forbid offering the software as a competing/managed service, or require a commercial licence above a threshold. Cannot be assumed safe for a commercial SaaS/e-book factory without legal review. | restate (BSL 1.1), n8n (SUL + `.ee`), AutoGPT `autogpt_platform/` (Polyform Shield), **dify (modified Apache: forbids multi-tenant use and logo removal)**, Flowise `enterprise/`, langfuse `ee/` |
| **AGPL-3.0** | Strong network copyleft. If your factory is a network service, the "offer it to users over a network" clause can be read as requiring you to publish the corresponding source of the **combined work**. Treat as **incompatible** with a closed-source product unless you (a) run the AGPL component as a genuinely separate process with no linking and no shared data structures, or (b) buy a commercial licence. | **ebooklib**, firecrawl |
| **GPL-2.0/3.0 (binaries invoked as separate processes)** | Invoking a GPL *CLI* (`pandoc`, `calibre`, `ebook-convert`) via `subprocess` on your own files is generally treated as aggregation, not derivation — but this is a legal judgement, not a technical fact. **`[INFERENCE]` — needs counsel before it becomes a product dependency.** | pandoc (badge-verified only) |

**Concrete consequence for this factory:** the single most attractive-looking repo for the "export to EPUB" step,
`aerkalov/ebooklib` (5.2k LOC, clean API, active), is **AGPL-3.0**, and the popular `mshumer/gpt-author` reference
implementation *imports it* (`from ebooklib import epub`). Copying gpt-author's approach drags AGPL code into the
product. See §(iii) for the recommended alternative.

---

## Top-5 entry-point → output traces (required evidence)

### Trace 1 — langgraph: graph execution → durable checkpoint
```
libs/langgraph/langgraph/pregel/main.py:3871   def invoke(            # sync entry
libs/langgraph/langgraph/pregel/main.py:2709   def stream(            # streaming entry
libs/langgraph/langgraph/pregel/main.py:1773   next_config = checkpointer.put(...)      # write checkpoint (run path)
libs/langgraph/langgraph/pregel/main.py:1849/1880/2099  further checkpointer.put(...)   # interrupt/update/loop paths
libs/langgraph/langgraph/pregel/main.py:2109   checkpointer.put_writes(next_config, push_writes, task_id)
libs/checkpoint/langgraph/checkpoint/base/__init__.py:301  def put(       # storage-agnostic interface
libs/checkpoint/langgraph/checkpoint/base/__init__.py:324  def put_writes(
libs/checkpoint-sqlite/langgraph/checkpoint/sqlite/__init__.py:49   class SqliteSaver(BaseCheckpointSaver[str])
libs/checkpoint-sqlite/langgraph/checkpoint/sqlite/__init__.py:147  CREATE TABLE IF NOT EXISTS checkpoints (
libs/checkpoint-sqlite/langgraph/checkpoint/sqlite/__init__.py:157  CREATE TABLE IF NOT EXISTS writes (
libs/langgraph/langgraph/types.py:427         class RetryPolicy(NamedTuple)
libs/langgraph/langgraph/types.py:433/439/441 initial_interval=0.5, max_attempts=3, jitter=True
```
**Output:** a `CheckpointTuple` per graph step keyed by thread; `update_state` (`main.py:2617`) and resume-from-checkpoint
are the *same* mechanism. **Factory relevance:** this is the reference implementation for "long book generation crashed at
chapter 9 of 20 — resume, don't restart". The distinct idea worth copying is not the graph engine but that **state is
written after every superstep, including pending writes, through a pluggable saver interface**.

### Trace 2 — openai-agents-python: agent run → persisted conversation
```
src/agents/run.py:275      class Runner: / async def run(
src/agents/run.py:623      async def _run_impl(          # single-run orchestration
src/agents/run_internal/run_loop.py                  # turn loop (tool calls/results)
src/agents/run_internal/agent_runner_helpers.py:488  result = RunResult(   # final object assembly
src/agents/result.py       class RunResult            # output + raw responses + usage + last_agent
src/agents/retry.py:16     class ModelRetryBackoffSettings
src/agents/models/_openai_retry.py                   # provider-level retry policy
src/agents/memory/session.py:53      class Session(Protocol)        # pluggable memory contract
src/agents/memory/sqlite_session.py:18  class SQLiteSession(SessionABC)
src/agents/memory/sqlite_session.py:233 CREATE TABLE IF NOT EXISTS {self.sessions_table} (
src/agents/memory/sqlite_session.py:243 CREATE TABLE IF NOT EXISTS {self.messages_table} (
```
**Output:** `RunResult` (typed) plus a session-scoped message log; `usage.py` tracks tokens per run. **Factory relevance:**
the *shape* of `RunResult` + `Session` + per-run usage accounting is exactly what a chapter-generation worker needs; it is
~1k LOC to replicate with the factory's existing SQLite, versus importing the whole SDK.

### Trace 3 — instructor: validated structured output → self-healing retry
```
instructor/v2/core/retry.py:138   def retry_sync_v2(
instructor/v2/core/retry.py:377   def retry_sync(...)      :407 def retry_async(...)
instructor/v2/core/retry.py:195   stop_condition = stop_after_attempt(max(max_retries, 0) + 1)
instructor/v2/core/retry.py:215   for attempt in max_retries_instance:
instructor/v2/core/retry.py:58    from pydantic import ValidationError      # the trigger
instructor/v2/core/retry.py:325   kwargs = handlers.reask_handler(...)      # re-ask with the error text
instructor/v2/core/errors.py:44   failed_attempts: list[FailedAttempt] | None = None
instructor/v2/core/errors.py:79-90  <failed_attempts> jinja template in the raised error message
```
**Output:** a validated Pydantic instance, or `InstructorRetryException` carrying **every failed attempt** so the caller
can see what the model kept getting wrong. **Factory relevance:** the highest-value ~400 LOC in this audit — it is the
canonical implementation of "ask LLM for JSON → validate → if invalid, send the validation error back → retry with
budget", which is the backbone of outline/chapter/module schema compliance.

### Trace 4 — pydantic-ai: agent graph → durable workflow (Temporal/DBOS/Prefect)
```
pydantic_ai_slim/pydantic_ai/agent/__init__.py:475   class Agent(AbstractAgent[AgentDepsT, OutputDataT])
pydantic_ai_slim/pydantic_ai/agent/__init__.py:1400  async def run(
pydantic_ai_slim/pydantic_ai/agent/__init__.py:1668  graph = _agent_graph.build_agent_graph(self.name, self._deps_type, output_type_)
pydantic_ai_slim/pydantic_ai/agent/__init__.py:1676  state = _agent_graph.GraphAgentState(...)
pydantic_ai_slim/pydantic_ai/agent/__init__.py:1773  async def _prepare_request(
pydantic_ai_slim/pydantic_ai/_agent_graph.py:3002    def build_agent_graph(
pydantic_ai_slim/pydantic_ai/durable_exec/temporal/_agent.py:127  class TemporalAgent(WrapperAgent[...])
pydantic_ai_slim/pydantic_ai/durable_exec/dbos/_agent.py:97       class DBOSAgent(WrapperAgent[...], DBOSConfiguredInstance)
pydantic_ai_slim/pydantic_ai/durable_exec/prefect/_durability.py   (Prefect backend)
pydantic_ai_slim/pydantic_ai/messages.py            ModelMessagesTypeAdapter (round-trip of full message history)
```
**Output:** typed `output`, resumable graph state, and message history that can be dumped/restored. **Factory relevance:**
proof that "agent + durable execution wrapper" is a *wrapper* pattern, not a rewrite (`WrapperAgent`) — the factory can
adopt the same layering instead of adopting pydantic-ai's whole model abstraction.

### Trace 5 — gpt-researcher: question → cited report
```
gpt_researcher/agent.py:37    class GPTResearcher
gpt_researcher/agent.py:192   if report_type == ReportType.DeepResearch.value:   # mode switch
gpt_researcher/agent.py:343   async def conduct_research(self, on_progress=None)
gpt_researcher/agent.py:463   async def write_report(...)
gpt_researcher/agent.py:745   def get_source_urls(self) -> list:  return list(self.visited_urls)
gpt_researcher/skills/researcher.py:21        class ResearchConductor
gpt_researcher/skills/deep_research.py:250    class DeepResearchSkill
gpt_researcher/skills/context_manager.py:19   class ContextManager
gpt_researcher/skills/writer.py:20            class ReportGenerator
gpt_researcher/utils/enum.py:6                class ReportType(Enum)  # research_report, outline_report, detailed_report, deep_research...
gpt_researcher/retrievers/  (22 dirs: tavily, serper, exa, brave, arxiv, pubmed_central, semantic_scholar, mcp, searx, ...)
```
**Output:** a report body plus the **set of visited URLs** (`visited_urls`) that the writer is expected to cite.
**Factory relevance:** this is the closest OSS analogue to the factory's research → evidence → product step, and its
retriever-per-provider directory layout is the pattern to copy for pluggable `web.search` capability.

---

## (ii) Per-repo deep dives

Each subsection: URL · purpose · licence (+caveats) · last meaningful maintenance evidence · architecture & key modules ·
**specific code that helps an e-book factory (file:line)** · integration complexity · dependencies/deployment ·
resource & cost profile · limitations/defects/risks · better/worse than alternatives.

### 1. langchain-ai/langgraph — MIT — Σ29
- **URL:** https://github.com/langchain-ai/langgraph · **HEAD** `799c2f04fd3a56186e24e9fb8bbf3568e543cfbe` @ 2026-10-11 (687 files, 468 .py, ~197 k py LOC)
- **Purpose:** low-level orchestration runtime for stateful, resumable multi-step agent graphs.
- **Licence:** MIT — verified, `LICENSE` line 1 "MIT License". No field-of-use clause. Monorepo contains
  `libs/langgraph`, `libs/checkpoint`, `checkpoint-sqlite`, `checkpoint-postgres`, `prebuilt`, `sdk-py`, `sdk-js`, `cli`.
- **Maintenance:** commit same day as this audit; active release charter (`checkpoint-conformance` package signals
  deliberate cross-implementation testing).
- **Architecture:** Pregel-style superstep engine (`libs/langgraph/langgraph/pregel/`), step algorithm in
  `pregel/_algo.py` (retry policy threaded through every task-prep function, e.g. `_algo.py:364/386/407/543/817/955/1126`),
  `pregel/main.py` `Pregel.invoke`/`stream`/`update_state`, pluggable persistence via `BaseCheckpointSaver`
  (`libs/checkpoint/langgraph/checkpoint/base/__init__.py`: `get_tuple`:262, `put`:301, `put_writes`:324),
  JSON+-style serialization (`checkpoint/serde/jsonplus.py`, `_msgpack.py`, `encrypted.py`, `event_hooks.py`).
- **Factory-relevant code:** the saver contract + SQLite DDL above; `RetryPolicy` defaults
  (`libs/langgraph/langgraph/types.py:427-442`).
- **Integration complexity:** medium. `langgraph` needs `langchain-core>=1.4.7` (declared in
  `libs/langgraph/pyproject.toml`), i.e. adopting LangGraph means adopting LangChain's message/model types — a large
  platform commitment for a factory whose application code is currently stdlib-only.
- **Cost:** no service required; SQLite saver is in-repo. Checkpoint rows grow with every superstep (a 20-chapter book at
  ~10 steps/chapter → hundreds of rows per run; prune or use write-ahead trimming).
- **Risks:** fast-moving `langchain-core` floor; monorepo complexity; `AGENTS.md`-driven agent contributions (large diff
  velocity). Debugging a half-executed graph requires understanding the channel/superstep model.
- **vs alternatives:** better than CrewAI for *resumability* and explicit state; worse than DBOS/Temporal for
  crash-safety guarantees across processes (no true durable timers/queues); heavier than a hand-rolled state machine
  when your graph is already linear (the factory's `ipf/orchestration/state_machine.py` is already a linear chain).

### 2. openai/openai-agents-python — MIT — Σ30
- **URL:** https://github.com/openai/openai-agents-python · **HEAD** `125efa029b4bfd84238bd2c4fd69c3406f802663` @ 2026-10-08 (1 684 files, 1 020 .py, 382 test files, ~484 k py LOC)
- **Purpose:** production agent SDK: agents, tools, handoffs, guardrails, sessions, tracing, voice, MCP.
- **Licence:** MIT — verified, `LICENSE` line 1. Optional extras include `sqlalchemy`, `redis`, `dapr`, `mongodb`, `litellm`.
- **Maintenance:** commit 2026-10-08; 18 open issues; release-please bot + CHANGELOG (disciplined releases).
- **Architecture:** `src/agents/run.py` (`Runner`, `_run_impl`) + a newly refactored `src/agents/run_internal/` package
  (25+ modules: `run_loop.py`, `tool_calls*`, `turn_preparation.py`, `session_persistence.py`, `streaming.py`),
  typed messages in `items.py`, `RunResult` in `result.py`, pluggable memory in `memory/` (`Session` protocol,
  `SQLiteSession`, `OpenAIConversationsSession`), model providers in `models/` (`multi_provider.py`, `openai_provider.py`,
  `chatcmpl_converter.py`, `_retry_runtime.py`), guardrails in `guardrail.py`/`tool_guardrails.py`, output-shape
  enforcement in `strict_schema.py`, prompt storage helper in `prompts.py`.
- **Factory-relevant code:** `memory/sqlite_session.py:233-243` (session/message schema), `retry.py:16`
  (`ModelRetryBackoffSettings`), `strict_schema.py` (JSON-schema strictness for structured outputs),
  `run_internal/session_persistence.py` (when/how history is committed).
- **Integration complexity:** low-to-medium: `pip install openai-agents`; deps are light (`openai`, `pydantic`, `mcp`,
  `httpx`, `starlette`). But it *is* an OpenAI-first abstraction (`openai>=` core dep) even with `litellm` extras.
- **Cost:** no service; token usage accounting built in (`usage.py`); tracing off unless configured (or exported to
  OpenAI/other).
- **Risks:** API churn is real (the `run_internal/` split shows heavy refactoring inside one release cycle), so
  pinning and vendoring the *pattern* may outlive pinning the package. `AGENTS.md`/`PLANS.md` in-repo indicate LLM-agent
  contributed code — review before trusting edge paths.
- **vs alternatives:** better documented and more test-dense than CrewAI; narrower (OpenAI-shaped) than LangGraph; the
  cleanest reference for "typed run result + session memory + usage" of anything audited.

### 3. 567-labs/instructor — MIT — Σ30
- **URL:** https://github.com/567-labs/instructor · **HEAD** `035c77b7b04dbbf70ae4457eb102038907b5a2a4` @ 2026-10-10 (1 043 files, 600 .py, 164 test files, ~97.7 k py LOC)
- **Purpose:** turn any LLM call into a validated Pydantic object, with automatic re-asking on validation failure.
- **Licence:** MIT — verified, `LICENSE` line 1. Deps: `openai`, `pydantic<3`, `tenacity`, `jinja2`, `typer`, `rich`.
- **Maintenance:** commit 2026-10-10; CHANGELOG + per-provider test suites; 161 open issues (high, but proportional to use).
- **Architecture:** **restructured in v2** — `instructor/v2/core/{client,patch,retry,errors,budget,hooks}.py`,
  provider adapters under `instructor/providers/`, mode negotiation (`mode.py`, `auto_client.py`), validators
  (`validation/`), streaming/partial parsing (`dsl/partial.py`, `dsl/json_tracker.py`, `dsl/iterable.py`),
  `batch/` for offline batch APIs. Legacy v1 modules still present (`process_response` equivalent now lives in
  `processing/`, `function_calls.py`).
- **Factory-relevant code:** the whole retry/reask loop (Trace 3), plus `dsl/partial.py` (parse a partially-streamed
  chapter and validate incrementally) and `dsl/citation.py` (citation-aware response types).
- **Integration complexity:** **low.** It patches provider SDKs client-side; can also be used purely as a
  validate-retry helper around any JSON call.
- **Cost:** one or two extra model calls per failed validation — bounded by `max_retries`; no service, no per-seat cost.
- **Risks:** provider matrix breadth means some adapters are thinner than others; the v1→v2 restructure means most
  blog-post snippets online reference the older module paths (a real source of copy-paste breakage).
- **vs alternatives:** far better than raw `json.loads`/`retry` hand-rolling; more mature and better tested than
  jsonformer (dead) or guidance (different mechanism); `outlines` constrains *tokens* (can't be wrong) while
  instructor *validates and retries* (works with any hosted API, including those that don't expose logits). For
  hosted-only e-book generation, instructor is the pragmatic choice.

### 4. pydantic/pydantic-ai — MIT — Σ26
- **URL:** https://github.com/pydantic/pydantic-ai · **HEAD** `69ea1e57201ed59e7d30fbe4a7590d193acecce0` @ 2026-10-10 (4 224 files, 1 786 .py, ~862 k py LOC; `pydantic_ai_slim/`, `pydantic_graph/`, `pydantic_evals/`)
- **Purpose:** typed agent framework with first-class durable-execution integrations and evals.
- **Licence:** MIT — verified, `LICENSE` line 1 ("The MIT License (MIT)").
- **Maintenance:** commit 2026-10-10; huge velocity (1 392 open issues) — an ecosystem, not a utility.
- **Architecture:** `Agent` (`agent/__init__.py:475`) over an internal pydantic-graph state machine
  (`_agent_graph.py:3002 build_agent_graph`, `GraphAgentState`), 24+ model providers
  (`models/{openai,anthropic,google,bedrock,mistral,cohere,groq,ollama,cerebras,xai,zai,openrouter,...}.py`),
  capability/step decomposition in `_spec.py`/`_parts_manager.py`, `pydantic_evals` for evaluation,
  and **durable execution wrappers** in `durable_exec/{temporal,dbos,prefect}/`.
- **Factory-relevant code:** `durable_exec/dbos/_agent.py:97` and `durable_exec/temporal/_agent.py:127` show exactly how
  to wrap an agent so each model call becomes a durable step; `_history_mirroring.py` and
  `ModelMessagesTypeAdapter` (messages.py) for history round-trips; `_genai_prices.py` for cost accounting.
- **Integration complexity:** medium-high; large surface, fast API evolution, `pydantic_ai_slim` vs full extras split.
- **Cost:** no service unless you add the durable backend; `_genai_prices.py` gives per-model price tables (useful for
  the factory's cost guardrails).
- **Risks:** dependency weight, breaking-change cadence, and the temptation to re-platform the factory onto it.
- **vs alternatives:** it is the only audited framework that ships **both** an agent graph *and* Temporal/DBOS/Prefect
  adapters — if durable multi-chapter generation ever needs a real engine, this is the most complete reference; but for
  a stdlib-only factory, copying the wrapper pattern beats importing the framework.

### 5. microsoft/autogen → microsoft/agent-framework — MIT (code) — Σ21 / Σ24
- **URLs:** https://github.com/microsoft/autogen (`027ecf0a379bcc1d09956d46d12d44a3ad9cee14` @ 2026-04-06) ·
  https://github.com/microsoft/agent-framework (`fd52de71579162a888fb2dd7510c57c6014dcdfc` @ 2026-10-09)
- **Purpose:** multi-agent conversation frameworks. **AutoGen is in maintenance mode** — its HEAD commit is literally
  *"Update maintenance mode banner in readme (#7521)"*.
- **Licence (a real trap):** AutoGen's root `LICENSE` is **CC-BY-4.0** (18 650 bytes) while the code is under
  **`LICENSE-CODE` = MIT** (verified: first line "MIT License"). Anyone reading only the root licence file (or the
  GitHub badge, which reports `CC-BY-4.0`) will mis-state the licence. `agent-framework/LICENSE` is MIT
  ("MIT License Copyright (c) Microsoft Corporation").
- **Maintenance evidence (primary announcement, opened 2026-10-11):** AutoGen Update discussion #7066, 2025-10-02 —
  *"AutoGen and Semantic Kernel are merging into a single, unified framework under the name Microsoft Agent
  Framework… AutoGen will still be maintained… but we will not be adding significant new features to it."*
- **Architecture:** AutoGen `python/packages/*` (`autogen-agentchat`, `autogen-core`, `autogen-ext`); agent-framework
  `python/packages/*` + `dotnet/` + `go/`, with a graph-based **workflow API** as the headline addition.
- **Factory-relevant code:** conversation-pattern implementations (`GroupChat`, `Magentic` orchestration) as reference
  designs for "writer + critic + editor" role separation; the workflow graph in agent-framework for sequential/parallel
  pipelines.
- **Integration complexity:** medium (`autogen-agentchat` + `autogen-ext`); .NET/Go presence in agent-framework adds
  noise to the repo but not to the Python package.
- **Risks:** building on AutoGen today means building on a framework Microsoft has frozen feature-wise and told you to
  migrate away from. agent-framework's Python packages are newer and less battle-tested.
- **vs alternatives:** worse long-term bet than LangGraph (frozen) or openai-agents (well-tested); the **role/pattern
  vocabulary** (Magentic, group chat) is still worth reading as a design source.

### 6. deepset-ai/haystack — Apache-2.0 — Σ27
- **URL:** https://github.com/deepset-ai/haystack · **HEAD** `c5e13354117d1376d4d8caf0efe77abe80120fc0` @ 2026-10-09 (12 118 files, 568 .py, ~148 k py LOC)
- **Purpose:** production RAG/agent pipeline framework (components + graph pipelines).
- **Licence:** Apache-2.0 — verified, `LICENSE` (Apache text). Deps include `networkx` (pipeline graphs), `openai`,
  `tenacity`, `pydantic`, `jinja2`, `posthog` (**telemetry**, with a pinned problematic version documented in the dep list).
- **Maintenance:** commit 2026-10-09; 165 open issues (low for 26 k stars → active triage).
- **Architecture:** `haystack/core/pipeline/pipeline.py:118 class Pipeline`, component taxonomy by directory:
  `components/{generators,retrievers,embedders,evaluators,rankers,converters,preprocessors,joiners,routers,writers}`,
  `components/evaluators/` worth reading for faithfulness-style metrics, `document_stores/`, `marshal` (serialize
  pipelines to YAML), `hooks/`.
- **Factory-relevant code:** the **component/pipeline contract** (each component declares typed inputs/outputs and is
  independently testable) and `components/evaluators/` for retrieval-quality scoring; `marshal/` for pipeline-as-data
  (a pipeline definition you can store next to a product project).
- **Integration complexity:** medium; `pip install haystack-ai` (extra deps per component). Telemetry (posthog) is
  on by default and must be disabled via env var.
- **Cost:** in-process; embedding/reranking costs depend on chosen components; no LLM calls unless you wire generators.
- **Risks:** the framework layer can absorb a small factory whole; component churn between minor versions; document-store
  abstraction may conflict with an existing SQLite-first persistence choice.
- **vs alternatives:** stronger engineering discipline than most agent frameworks; for this factory it is most useful as
  *the pattern to copy for retrieval + evaluation components*, not as a runtime dependency.

### 7. langgenius/dify — modified Apache-2.0 — Σ23 (licence-limited)
- **URL:** https://github.com/langgenius/dify · **HEAD** `5fe7f018764e94aefa06f098a23e28a55d6b4e18` @ 2026-10-11 (14 160 files, 4 301 .py, ~1.0 M py LOC)
- **Purpose:** full LLMOps platform: visual workflow builder, RAG, agents, model management, publishing to app endpoints.
- **Licence caveats (verified, `LICENSE` is a **custom** file, not Apache):** *"Dify is licensed under a modified version
  of the Apache License 2.0, with the following additional conditions… a. Multi-tenant service: Unless explicitly
  authorized by Dify in writing, you may not use the Dify source code to operate a multi-tenant environment… b. LOGO
  and copyright information: …"*. **A multi-tenant SaaS is exactly what an e-book factory selling to multiple creators
  is** — this is a field-of-use restriction, i.e. **not open source** in the OSI sense. Also relevant: Dify now depends
  on an external engine package `graphon==0.7.0` (`api/pyproject.toml:48`; imports in
  `api/core/workflow/human_input_adapter.py:20-21`), so the workflow engine's source is *not* fully in this repo.
- **Maintenance:** commit 2026-10-11; 1 097 open issues; aggressive feature velocity.
- **Architecture:** `api/core/workflow/` (node factory/runtime, `variable_pool_initializer.py`, `template_rendering.py`,
  `human_input_*.py`), `api/models/workflow.py` with a *rich* persistence schema — `workflows`:211, `workflow_version_counters`:727,
  **`workflow_runs`:795** (`id, tenant_id, app_id, workflow_id, type, triggered_from, version, graph, inputs, status,
  outputs, error, elapsed_time, total_tokens, total_steps, created_by_role, created_by, created_at, finished_at,
  exceptions_count`), `workflow_node_executions`:966, `workflow_node_execution_offload`:1171, `workflow_app_logs`:1307,
  `workflow_archive_logs`:1381, `workflow_run_archive_bundles`:1446, `workflow_conversation_variables`:1474,
  `workflow_draft_variables`:1541, `workflow_pauses`:2091, `workflow_pause_reasons`:2148.
- **Factory-relevant code:** the **pause/resume model** (`workflow_pauses`, `workflow_pause_reasons`) and the
  node-execution/offload split — a design for "generation is waiting for human approval" and "large node outputs are
  offloaded instead of bloating the run row".
- **Integration complexity:** high if adopted (Docker-Compose stack: Postgres, Redis, workers, web). **Copy the schema,
  not the platform.**
- **Resources:** multi-container; needs Postgres + Redis + a worker fleet. Not appropriate to embed.
- **Risks:** licence (multi-tenant), external `graphon` engine, size.
- **vs alternatives:** far more complete as a *product* than langgraph; unusable as a *library* for a multi-tenant
  commercial product without permission.

### 8. assafelovic/gpt-researcher — Apache-2.0 — Σ27
- **URL:** https://github.com/assafelovic/gpt-researcher · **HEAD** `3582a6841fe7dc801e2a34e34ee408641aea9bce` @ 2026-10-10 (841 files, 366 .py, ~37 k py LOC)
- **Purpose:** autonomous deep-research agent producing reports with sources.
- **Licence:** Apache-2.0 — verified, `LICENSE`. 31 open issues; active.
- **Architecture:** `gpt_researcher/agent.py` orchestrator; skills split into
  `skills/{researcher,deep_research,curator,writer,context_manager,browser,image_generator}.py`; **22 retriever
  integrations** under `retrievers/` (tavily, serper, exa, brave, searx, arxiv, pubmed_central, semantic_scholar, mcp, …);
  `actions/` (query planning, report generation); `utils/enum.py:6 ReportType` (`research_report`, `resource_report`,
  `outline_report`, `custom_report`, `detailed_report`, `subtopic_report`, `deep_research`).
- **Factory-relevant code:** `retrievers/base.py` (uniform search/fetch contract), `skills/writer.py:20` (report
  composition), `agent.py:745 get_source_urls()` + `visited_urls` (citation inventory), `skills/curator.py`
  (source filtering/ranking).
- **Integration complexity:** low-medium as a library (`GPTResearcher(...)` then `await conduct_research()` /
  `write_report()`), but it drags in whatever retriever SDKs you enable.
- **Cost:** search API calls + LLM summarisation; "deep research" mode multiplies LLM calls by design.
- **Risks:** citation quality is prompt-driven, not enforced (no per-sentence verifier in the code path audited);
  retriever API keys required per provider; report output is Markdown/PDF-ish text, not a book structure.
- **vs alternatives:** better maintained than STORM and provider-agnostic; less academically rigorous than STORM's
  perspective-guided question asking; lacks the factory's evidence/provenance rigor — use as a *retriever pattern*
  source, then keep the factory's own `EvidenceStore`.

### 9. stanford-oval/storm — MIT — Σ25
- **URL:** https://github.com/stanford-oval/storm · **HEAD** `fb951af7744dab086e34962e9bc6fe878e145f83` @ 2025-09-30 (**74 files, 52 .py, ~14 k py LOC**)
- **Purpose:** Stanford's knowledge-curation/"Wikipedia article generation" system: perspective discovery →
  simulated expert conversations → grounded outline → article with citations.
- **Licence:** MIT — verified, `LICENSE` line 1.
- **Maintenance:** default branch untouched for ~12 months before this audit (`requirements.txt` still pins
  `dspy_ai==2.4.9`, an API generation behind current dspy) → **staleness risk is concrete, not speculative**.
- **Architecture:** `knowledge_storm/storm_wiki/engine.py` (`run(topic, ground_truth_url, do_research,
  do_generate_outline, do_generate_article, do_polish_article, ...)` at :341 — explicit stage switches worth copying),
  `storm_wiki/modules/persona_generator.py` (`StormPersonaGenerator`:114, `_get_...` persona prompts :71),
  `storm_wiki/modules/storm_dataclass.py` (`StormInformationTable`:48 with `url_to_info`, `DialogueTurn`:14,
  `dump_url_to_info`:93, `from_conversation_log_file`:100 — **citation-bearing evidence index**),
  `collaborative_storm/` (CoStormRunner:505, `expert_utterance_generator`, `grounded_question_answering`),
  `rm.py` (14 retrievers incl. Serper, Tavily, Brave, SearXNG, DuckDuckGo, Azure AI Search), `lm.py` (13 LM classes).
- **Factory-relevant code:** `storm_dataclass.py` — the `url → Information{snippets}` map is precisely the
  provenance structure an evidence-based e-book needs; `persona_generator.py` — "who would care about this topic"
  perspective expansion, directly reusable for audience/angle discovery; `engine.py:341` stage flags for
  reproducible partial re-runs.
- **Integration complexity:** medium; `requirements.txt` includes `sentence-transformers`, `qdrant-client`,
  `langchain-*`, `trafilatura` (heavy). Uses `dspy` as its LM abstraction at an **old pinned version**.
- **Cost:** many LM calls for the simulated conversations; retrieval calls per persona.
- **Risks:** stale default branch; old dspy pin; academic code style (long prompt strings, no typing discipline).
- **vs alternatives:** the best *algorithmic* source (perspective-guided question asking beats naive "search then
  summarise"); worse as a dependency than gpt-researcher. **Recommended action: port ~300 lines of ideas, import
  nothing.**

### 10. Kozea/WeasyPrint — BSD-3 — Σ30
- **URL:** https://github.com/Kozea/WeasyPrint · **HEAD** `369b15340ea97c5d649af65e8e10330b8d08f01f` @ 2026-10-01 (213 files, 154 .py, ~80 k py LOC)
- **Purpose:** HTML/CSS → PDF (and PNG) renderer implementing paged media (print CSS).
- **Licence:** BSD-3-Clause — verified, `LICENSE` line 1 "BSD 3-Clause License". Deps are clean and small:
  `pydyf`, `cffi`, `tinyhtml5`, `tinycss2`, `cssselect2`, `Pyphen`, `Pillow`, `fonttools[woff]` — **no browser, no JVM**.
- **Maintenance:** commit 2026-10-01; 154 open issues; `Development Status :: 5 - Production/Stable` classifier.
- **Factory-relevant code:** `weasyprint/__init__.py:114 class HTML` (+ `write_pdf`), `weasyprint/pdf/__init__.py:120
  def generate_pdf(document, target, zoom, **options)` — the single call the factory needs: produce a
  print-quality PDF from the same HTML/CSS used for the deliverable, with `@page` rules, running headers/footers,
  page counters, and **PDF/A-ish options** exposed as `**options` (verify against docs before promising PDF/A).
- **Integration complexity:** **low.** Pure Python + tiny C deps (`pydyf`). Renders in-process.
- **Cost:** CPU-bound; a 200-page book renders in seconds-to-tens-of-seconds; no per-render licence cost. Fonts embed
  by default (watch font licensing for commercial e-books!).
- **Risks:** CSS feature gaps vs real browsers (flex/grid support historically partial); each release changes layout
  subtly, so golden-file tests are mandatory; cannot execute JavaScript (irrelevant for e-books, relevant if you feed it
  a marketing page).
- **vs alternatives:** better than ReportLab for *design-heavy* output (you write CSS/HTML, not Python layout code);
  better than wkhtmltopdf (unmaintained) and lighter than headless Chromium (no browser process, deterministic);
  worse than ReportLab for absolute programmatic control of every line/box.

### 11. MrBitBucket/reportlab-mirror — BSD-3 — Σ27
- **URL:** https://github.com/MrBitBucket/reportlab-mirror · **HEAD** `c4c83349b7272094f7413f726dcf206dbde7836d` @ 2026-08-19
- **Purpose:** ReportLab toolkit — programmatic PDF generation (Platypus flowable layout engine).
- **Licence:** BSD-3 — verified, `LICENSE` ("Redistribution and use in source and binary forms…" 3-clause BSD text,
  © ReportLab Inc.). **Caveat:** this is a *third-party mirror* of `hg.reportlab.com`; the canonical project is
  Mercurial-hosted. Use the PyPI package `reportlab` (or `reportlab==4.x` wheels) rather than a git URL, and treat
  version/date claims from this mirror as approximate.
- **Maintenance:** mirror commit 2026-08-19 ("version --< 5.0.1"); upstream is commercially backed (ReportLab Inc.).
- **Factory-relevant code:** `src/reportlab/platypus/doctemplate.py:424 class BaseDocTemplate` and
  `:1047 def build(self, flowables, filename=None, canvasmaker=canvas.Canvas)` — the flowable→PDF pipeline; combined
  with `reportlab/pdfbase/` for low-level canvas work and `reportlab/graphics/` for vector charts. Useful when the
  factory needs *exact* layout (tables, worksheets, checklists) or PDF form fields/acroforms, which paged CSS does
  clumsily.
- **Integration complexity:** low (`pip install reportlab`, pure Python + optional C accelerator).
- **Cost:** trivial CPU; no service.
- **Risks:** Platypus layout is imperative and verbose; building a whole book design in it takes more code than CSS.
- **vs alternatives:** pairs with WeasyPrint rather than replacing it — WeasyPrint for HTML-driven book bodies,
  ReportLab for deterministic tables/worksheets (workbook-style deliverables).

### 12. microsoft/markitdown — MIT — Σ26
- **URL:** https://github.com/microsoft/markitdown · **HEAD** `4cc9fa17653d695d64fb9eee5b33d4de55ff84e8` @ 2026-10-03 (195 files, 92 .py, ~22 k py LOC)
- **Purpose:** convert many formats (PDF, DOCX, PPTX, XLSX, HTML, CSV, images, audio, EPUB, IPYNB, Outlook MSG, ZIP,
  RSS, Wikipedia, Bing SERP, YouTube) into Markdown for LLMs.
- **Licence:** MIT — verified, `LICENSE` line 1. Base deps deliberately tiny: `beautifulsoup4`, `requests`,
  `markdownify`, `magika`, `charset-normalizer`, `defusedxml`; everything else is an optional extra.
- **Maintenance:** commit 2026-10-03; multi-package repo (`packages/{markitdown,markitdown-mcp,markitdown-ocr,markitdown-sample-plugin}`).
- **Architecture:** `packages/markitdown/src/markitdown/_markitdown.py:141 class MarkItDown`,
  `:178 self._converters: List[ConverterRegistration]`, `:229-250` explicit registry (`register_converter(...)` for
  Docx/Xlsx/Pptx/Audio/Image/Ipynb/Pdf/OutlookMsg/…), converters in `converters/` with `_epub_converter.py`,
  `_pdf_converter.py`, `_markdownify.py`, `_llm_caption.py`, `_transcribe_audio.py`, `_exiftool.py`.
- **Factory-relevant code:** the **converter registry + optional-dependency-per-converter pattern** (import cost stays
  near zero until a format is used) and the EPUB→Markdown converter, which lets the factory ingest competitor e-books
  and existing client material into its corpus. Test suite has one test file per format (`tests/test_epub.py`,
  `test_pdf.py`, `test_docx.py`, …).
- **Integration complexity:** **low**; runs offline except `_llm_caption`/`_transcribe_audio`.
- **Cost:** negligible CPU for text formats; OCR/audio paths need extra deps/services.
- **Risks:** Markdown output is lossy by construction (tables/columns/heuristics), so it is *input* for further
  extraction, not a fidelity layer; `magika` adds a ~small ML model download at first use.
- **vs alternatives:** markedly lighter than `unstructured` (no spacy/numba) and than `docling` (no layout models);
  less accurate on complex PDFs. Recommended as the factory's **default ingest** path.

### 13. docling-project/docling — MIT — Σ25
- **URL:** https://github.com/docling-project/docling · **HEAD** `21588f5261d0a5a051102356d9babb35e790f33e` @ 2026-10-11 (1 869 files, 546 .py, ~177 k py LOC)
- **Purpose:** document understanding/conversion with layout + table models, outputs structured doc model (JSON/MD/HTML).
- **Licence:** MIT — verified, `LICENSE` line 1.
- **Maintenance:** commit 2026-10-11; 1 054 open issues; several parser generations maintained in parallel
  (`backend/docling_parse_backend.py`, `docling_parse_v2_backend.py`, `docling_parse_v4_backend.py`) — a clear signal of
  a fast-moving research-derived codebase.
- **Architecture:** `docling/document_converter.py:467 class DocumentConverter` with `convert`:589 and `convert_all`:654;
  ~30 backends in `docling/backend/` (`pdf_backend.py`, `docs`/msword, `mspowerpoint`, `msexcel`, `html_backend.py`,
  **`epub_backend.py`**, `latex_backend.py`, `md_backend.py`, `email_backend.py`, `webvtt_backend.py`, …),
  `docling/chunking/`, `docling/document_extractor.py`, `docling/pipeline/`.
- **Factory-relevant code:** `chunking/` (structure-aware chunking for retrieval over long books) and the
  `epub_backend` (decompose an existing EPUB into a structured tree, useful for teardown/competitive analysis).
- **Integration complexity:** medium; pulls model artifacts on first run (download + disk cache), heavier install than
  markitdown. `pydantic`-based datamodel (`docling-core`) — good fit for typed pipelines.
- **Cost:** CPU inference is slow on large PDFs (tens of seconds to minutes/page-range); GPU recommended for volume work.
- **Risks:** model download at runtime complicates offline/CI use; frequent backend churn; 1 k+ open issues.
- **vs alternatives:** much higher fidelity than markitdown on tables/columns; heavier and less predictable than
  `pypdf`/`pdfplumber`. Use for *papers/manuals being researched*, not for the factory's own high-volume, simple ingest.

### 14. Unstructured-IO/unstructured — Apache-2.0 (with strings attached) — Σ22
- **URL:** https://github.com/Unstructured-IO/unstructured · **HEAD** `e2f22b38b995858c870dade7cbf6fcf06a9c801c` @ 2026-10-09 (1 325 files, 297 .py, ~87 k py LOC)
- **Purpose:** partition/clean/chunk documents into elements for RAG.
- **Licence:** Apache-2.0 — verified, `LICENSE.md` (Apache text). **But** the package ships
  `unstructured/_usage_notice.py`, which prints: *"Try Unstructured's commercial offering for improved output quality.
  10,000 free pages to start… Hide this notice: UNSTRUCTURED_DISABLE_NOTICE=1"*, and there is a `telemetry.py` module
  plus `safe_http.py`. Licence-clean, but *opinionated* about funneling you to the paid API and it emits telemetry.
- **Maintenance:** commit 2026-10-09; CHANGELOG-driven; renovate config in-repo.
- **Architecture:** `unstructured/partition/` per format, with `partition_pdf` at `partition/pdf.py:146` and a strategy
  dispatcher `partition/strategies.py` (`determine_pdf_or_image_strategy`, `PartitionStrategy.AUTO`), decorator-based
  chunking (`add_chunking_strategy`), `partition/` (myriad formats), `chunking/`, `cleaners/`, `documents/` elements.
- **Factory-relevant code:** `partition/pdf.py:145-179` (strategy auto-selection per page: fast vs OCR vs hi-res) — worth
  copying if PDF-heavy ingest becomes core; the element model (`documents/elements.py`) is a reasonable schema for
  "extract text + tables + titles" from research PDFs.
- **Integration complexity:** **high** — base deps include `spacy`, `numba`, `numpy`, `lxml`, plus extra per format and
  system libs; container-first deployment.
- **Cost:** heavy install (~hundreds of MB), OCR needs Tesseract/poppler or a hosted service.
- **Risks:** telemetry + upsell notice in an OSS package (vetoable but must be actively disabled); spacy/numba version
  conflicts are a recurring ecosystem pain; per-format optional deps explode.
- **vs alternatives:** superseded for most factory needs by markitdown (lighter) + docling (better tables). Keep only if
  you adopt its element schema.

### 15. aerkalov/ebooklib — **AGPL-3.0** — Σ23 (licence-disqualified)
- **URL:** https://github.com/aerkalov/ebooklib · **HEAD** `37e2a19225101581e564a9608401bdf484481a1b` @ 2026-10-07 (73 files, 36 .py, ~4.9 k py LOC)
- **Purpose:** read/write EPUB 2/3 (book model, items, TOC, writer/reader).
- **Licence:** **AGPL-3.0** — verified, `LICENSE.txt` line 1 "GNU AFFERO GENERAL PUBLIC LICENSE / Version 3".
  **Disqualifying** for a proprietary product used over a network (see §(iv)). Note: it is the *only* mature,
  well-scoped EPUB writer in the Python OSS ecosystem, which is exactly why the licence matters.
- **Maintenance:** commit 2026-10-07; 110 open issues; tests present (`tests/test_epub_*.py`).
- **Architecture:** `ebooklib/epub.py` (`EpubBook`, `EpubWriter`, `write_epub`, `EpubHtml`, spine/guide/metadata),
  `book.py`, `items.py`, `toc.py`, `reader.py`, `writer.py`.
- **Factory-relevant code (read-only reference):** the EPUB packaging sequence — mimetype stored uncompressed first,
  `META-INF/container.xml`, OPF manifest/spine/guide, NCX/nav, cover as `properties="cover-image"`. That sequence is
  ~200 lines to reimplement with `zipfile` + `xml.etree`, and OCF/OPF are open specifications (IDPF/W3C), so
  **reimplementing avoids the licence entirely**.
- **Integration complexity:** trivial technically — which is the trap.
- **Risks:** AGPL contagion; also a small, single-maintainer project (bus factor).
- **vs alternatives:** AGPL alternatives exist but with their own constraints (`pandoc`/`calibre` = GPL CLIs;
  `epubcheck` = BSD but validation-only, no writing). **Recommendation: in-house minimal EPUB3 packager + `epubcheck`
  for conformance.** See §(iii).

### 16. w3c/epubcheck — BSD-3 — Σ25 (validation guardrail)
- **URL:** https://github.com/w3c/epubcheck · **HEAD** `0cc28fb` @ 2026-10-01 (4 191 files, Java)
- **Purpose:** the reference EPUB conformance validator (EPUB 2/3, plus accessibility-related checks via ACE).
- **Licence:** BSD-3-Clause — verified, `LICENSE.md` ("Redistribution and use in source and binary forms, with or
  without modification…" 3-clause text, © Adobe/IDPF/W3C).
- **Maintenance:** commit 2026-10-01; governed by W3C; releases as a jar.
- **Factory-relevant code:** the *validator* itself — invoke the jar in a validation step after export and fail the
  product build on non-zero exit. `src/main/java/com/` contains the checks (package structure per EPUB version).
  Also documents the error taxonomy the factory can surface to users ("your EPUB has 3 blocking errors").
- **Integration complexity:** needs a JVM (or the npm wrapper) — one extra runtime in the deployment.
- **Cost:** seconds per book; CPU only.
- **Risks:** Java dependency; version drift with EPUB spec updates; noisy legacy warnings on EPUB2.
- **vs alternatives:** no real alternative for *conformance*; `daisy/ace` (below) covers accessibility, not spec validity.

### 17. daisy/ace — MIT — Σ23 (accessibility QA)
- **URL:** https://github.com/daisy/ace · **HEAD** `dfa87b5` @ 2026-05-01 (593 files; Node/TS monorepo under `packages/`)
- **Purpose:** EPUB Accessibility Checker (DAISY) — runs axe-core rules over each EPUB document.
- **Licence:** MIT — verified, `LICENSE.txt` ("MIT License") and `packages/*/package.json` `"license": "MIT"`.
- **Maintenance:** commit 2026-05-01; version `1.4.7-alpha.1` (`package.json:27`) → alpha, cautious integration.
- **Architecture:** `packages/ace-{core,report,cli,http,config,meta,axe-runner-puppeteer,axe-runner-electron}` +
  `epub-utils`; jest configs for cli/common/electron/puppeteer → real test infrastructure.
- **Factory-relevant code:** the per-document axe-core invocation and the machine-readable report model
  (`ace-report*`) — copy the *report shape* (severity, file, line, rule id) into the factory's QA gates. Accessibility
  failures (missing alt text, missing `lang`, no navigation document) are exactly the defects that get a digital product
  rejected by institutional buyers.
- **Integration complexity:** Node + Chromium/Electron; heavy for a Python factory, so prefer running the CLI as a
  separate, optional process.
- **Risks:** alpha API; browser dependency in CI.
- **vs alternatives:** unique in kind (competitors: commercial tools or manual audit).

### 18. temporalio/sdk-python — MIT — Σ26 (durable execution, needs a cluster)
- **URL:** https://github.com/temporalio/sdk-python · **HEAD** `fc5299b9ddab38d6a9064a23295bf36412f4b487` @ 2026-10-09 (691 files, 497 .py, ~161 k py LOC)
- **Purpose:** official Temporal Python SDK: durable workflows, activities, timers, signals, queries, schedules.
- **Licence:** MIT — verified, `LICENSE` line 1 "The MIT License". Deps: `nexus-rpc`, `protobuf`, `python-dateutil`,
  `types-protobuf`, `typing-extensions` (light).
- **Maintenance:** commit 2026-10-09; vendor-backed; CHANGELOG + `changelog/` fragments.
- **Architecture:** `temporalio/client/` (`_client.py:117 class Client`, `start_workflow`:397-530,
  `execute_workflow`:659-792, `_schedule.py`, `_worker_versioning.py`), `temporalio/worker/` (`_worker.py:792 run()`,
  `_workflow_instance.py`, determinism tooling: `_replayer.py`, `workflow_sandbox/`, `_debugger.py`),
  `temporalio/workflow.py` (deterministic context: `workflow.sleep`, `wait_condition`, signals), `temporalio/activity.py`,
  `temporalio/testing/` (in-process test server — usable in CI without a cluster!), `converter/` (payload codecs).
- **Factory-relevant code:** the *determinism rules and sandbox* (`worker/_workflow_instance.py`, `_replayer.py`) are the
  best available documentation of what "resumable" really costs: replayable workflow code must avoid non-deterministic
  side effects, so all LLM calls become **activities** with retry policies — exactly the discipline a chapter-factory
  needs when a run must survive a restart.
- **Integration complexity:** **high** unless you only use `temporalio.testing`; production needs a Temporal server
  (self-hosted cluster or Temporal Cloud).
- **Cost:** infra (server + persistence: Cassandra/Postgres/MySQL + optional Elasticsearch) or Cloud pricing; SDK itself free.
- **Risks:** operational burden for a one-person factory; workflow-versioning discipline is non-trivial; overkill if
  the factory's jobs are minutes long and restartable by re-running.
- **vs alternatives:** stronger guarantees than LangGraph checkpoints (true durability, timers, retries across process
  restarts); heavier than DBOS (below) which needs only Postgres/SQLite.

### 19. dbos-inc/dbos-transact-py — MIT — Σ25 (durable execution, light)
- **URL:** https://github.com/dbos-inc/dbos-transact-py · **HEAD** `d3f2de758bbef8e10ca84a6b466108de07fd3be0` @ 2026-10-09 (122 files, 104 .py, ~76 k py LOC)
- **Purpose:** durable workflows/queues as Python decorators, persisted in Postgres **or SQLite**.
- **Licence:** MIT — verified, `LICENSE` line 1 and `pyproject.toml` `license = {text = "MIT"}`.
  Deps: `pyyaml`, `python-dateutil`, `psycopg`, `click`, `sqlalchemy[asyncio]` — no exotic stack.
- **Maintenance:** commit 2026-10-09; `Development Status :: 5 - Production/Stable`.
- **Architecture:** `dbos/{_dbos.py,_client.py,_context.py,_core.py}`, decorators (`decorators.py` — `@DBOS.workflow`,
  `@DBOS.step`), system database layer (`_sys_db_postgres.py`, `_sys_db_sqlite.py`, `_schemas/`, `_migration.py`),
  durability machinery (`_workflow_commands.py`, `_recovery.py`, `_queue.py`, `_scheduler.py` incl. cron),
  observability (`_tracer.py`), and a `dbos/` CLI.
- **Factory-relevant code:** `_recovery.py` + `_workflow_commands.py` show the minimal viable durable-execution core
  (operation log → replay on restart) and `_sys_db_sqlite.py` proves you can get durability with **only SQLite** —
  matching this factory's existing `ipf/state.py` choice. Also `_debouncer.py`, `_enqueue_options.py` for job queues.
- **Integration complexity:** **low-medium**: `pip install dbos`, point it at SQLite/Postgres, decorate functions.
- **Cost:** no service beyond the DB you already run; small rows per step.
- **Risks:** younger project than Temporal; single-vendor roadmap; the SQLite mode's concurrency limits need testing
  before being trusted with parallel chapter generation.
- **vs alternatives:** the best *fit-to-effort ratio* in this category for this factory: Temporal-grade concepts
  (steps, replay, queues, cron) at SQLite cost.

### 20. restatedev/restate — **BSL 1.1** — Σ22 (licence-limited)
- **URL:** https://github.com/restatedev/restate · **HEAD** `752283ec6600c45ed8a60c97d132508c5a45f3af` @ 2026-10-09 (1 501 files; Rust workspace, `crates/*`)
- **Purpose:** durable execution / workflow platform (single binary, service-oriented).
- **Licence:** **Business Source License 1.1** — verified, `LICENSE` line 1. Parameters: *"Additional Use Grant: You may
  make use of the Licensed Work, provided that you may not use the Licensed Work for a Public Restate Platform
  Service."* BSL converts to a permissive licence after the change date, but until then it is **not OSI open source**.
- **Maintenance:** commit 2026-10-09; active; 467 open issues.
- **Architecture:** Rust crates split by concern: `partition-store`, `ingestion-client`, `invoker-impl`, `ingress-http`,
  `ingress-kafka`, `metadata-store`, `timer`/`timer-queue`, `storage-query-datafusion`, `wal-protocol`, `service-client`
  — a well-factored durability engine (worth *reading* for concepts: WAL, partition store, timer queue).
- **Factory-relevant code:** conceptual only (WAL + timer queue + idempotent invocations). Its Python SDK is a separate
  repository that was **not** inspected.
- **Integration complexity:** deploy the server binary; services register with it.
- **Cost:** single binary + storage; cheaper than Temporal operationally.
- **Risks:** BSL; Rust codebase (hard to patch); Python SDK maturity unverified here.
- **vs alternatives:** nicer ops story than Temporal, but **licence makes it a non-starter for a commercial product
  unless you accept BSL terms or buy support**.

### 21. microsoft/agent-framework — MIT — Σ24 (AutoGen successor)
(Also see §5.) `fd52de71579162a888fb2dd7510c57c6014dcdfc` @ 2026-10-09; 5 518 files, 1 322 .py, ~612 k py LOC.
MIT verified from `LICENSE`. Layout: `python/packages/*`, `dotnet/`, `go/`, `declarative-agents/`, `docs/`.
Python package status is tracked in `python/PACKAGE_STATUS.md` (read it before depending on a package — several are
explicitly not stable). Headline feature is a **graph-based workflow API** with sequential/parallel/Magentic patterns
built on it. Factory relevance: pattern vocabulary + the "workflow API above agents" layering; risk: young, RC-stage,
multi-language repo. **Better** than AutoGen for a new build; **worse** than LangGraph/openai-agents in maturity.

### 22. crewAIInc/crewAI — MIT — Σ25
- **URL:** https://github.com/crewAIInc/crewAI · **HEAD** `6b93fa0e25a926215ca7bb52a95d4db7d9d7262d` @ 2026-10-09 (34 973 files, 1 412 .py, ~487 k py LOC)
- **Licence:** MIT — verified, `LICENSE` ("Permission is hereby granted, free of charge…", © crewAI, Inc.).
- **Architecture:** workspace monorepo `lib/{crewai,crewai-core,crewai-tools,crewai-files,cli,devtools}`;
  `lib/crewai/src/crewai/crew.py:164 class Crew`, `kickoff`:995, `kickoff_for_each`:1099, `kickoff_async`:1135;
  `process.py` (sequential/hierarchical), `task.py`, `agents/`, `flows/` (event-driven flows), `memory/`
  (`unified_memory.py`, `storage/{lancedb_storage,qdrant_edge_storage,backend}.py` with `save`:291/`search`:373),
  `knowledge/`, `rag/`, `llm.py`+`llms/`, `telemetry/`.
- **Factory-relevant code:** `memory/storage/backend.py:48/56` (clean save/search contract over vector stores) and
  `flows/` (event-driven orchestration for "chapter written → critique → revise"). Deps note: it **pins
  `instructor>=1.3.3,<1.16`** with an in-file comment about an API break — direct evidence that even large frameworks
  struggle with instructor's v2 transition, and a reminder to pin.
- **Integration complexity:** medium; pulls `chromadb`, `tokenizers`, opentelemetry, pdfplumber (see pyproject).
- **Risks:** 34 k files in one repo; heavy dep tree; hierarchical process mode can burn tokens unpredictably;
  telemetry present.
- **vs alternatives:** more opinionated/higher-level than LangGraph (faster to prototype a multi-role book team, harder
  to control); less durable.

### 23. dottxt-ai/outlines — Apache-2.0 — Σ25 (constrained decoding)
- **URL:** https://github.com/dottxt-ai/outlines · **HEAD** `c52af8472c6fe8a607a733ec11b709476af77a96` @ 2026-08-24 (298 files, 122 .py, ~27 k py LOC)
- **Licence:** Apache-2.0 — verified, `LICENSE`. Deps pin `outlines_core==0.2.14` (Rust core) plus optional backends
  `llguidance`, `xgrammar`, `transformers`, `vllm`, `llamacpp`, `mlxlm`, `ollama`, `tgi`, `sglang`.
- **Architecture:** `src/outlines/generator.py` (`Generator`:216 with `backend_name` selection; `__call__`:280,
  `stream`:324; async variant `AsyncGenerator`:122), `src/outlines/backends/{base,llguidance,outlines_core,xgrammar}.py`
  (`Backend.get_json_schema_logits_processor`:19, `get_regex_logits_processor`:41), `grammars.py`, `models/` per provider,
  `caching.py`.
- **Factory-relevant code:** `backends/base.py` logits-processor interface — the cleanest place to learn *how* to make a
  local model emit a valid JSON schema or regex; `models/openai.py` shows the hosted-API fallback path.
- **Reality check for a hosted-API factory:** constrained decoding only works when you control the sampler (local
  models, vLLM, TGI, llama.cpp). Against a hosted API (OpenAI/Anthropic), `outlines` degrades to schema-prompting +
  parsing, i.e. **instructor is the better tool**; outlines becomes relevant only if the factory ever runs local models
  for cost reasons.
- **Maintenance:** commit 2026-08-24 (≈7 weeks); 192 open issues.
- **Risks:** Rust core version pinning; backend matrix churn; big optional-dependency surface.

### 24. guidance-ai/guidance — MIT — Σ24
- **URL:** https://github.com/guidance-ai/guidance · **HEAD** `21b1d90dfbebff4b141df70c714c8af15aa5f4af` @ 2026-05-21 (323 files, 151 .py, ~24 k py LOC)
- **Licence:** MIT — verified, `LICENSE.md` line 1.
- **Architecture:** `guidance/{_ast.py,_grammar.py,_parser.py,_program.py,_guidance.py,_schema.py,trace,visual,registry,models}`;
  `guidance/_grammar.py:27 def gen(` is the grammar/interleaving primitive; `packages/python` holds the distribution.
- **Factory-relevant code:** `_grammar.py`/`_parser.py` — a template language that interleaves literal text, generation,
  and *choices/loops*, i.e. a way to express a chapter template with typed slots and enforced structure in one artifact.
- **Maintenance:** 2026-05-21 (≈5 months stale at audit time, still alive but slowing).
- **vs instructor/outlines:** conceptually elegant, weaker ecosystem momentum; pick instructor for hosted APIs.

### 25. explodinggradients/ragas → vibrantlabsai/ragas — Apache-2.0 — Σ23 (staleness + rename)
- **URLs:** https://github.com/vibrantlabsai/ragas (old namespace redirects) · **HEAD** `298b68274234c060deacab3cf5fb52aa3a20e885` @ 2026-02-24
- **Licence:** Apache-2.0 — verified, `LICENSE`. Also depends on `instructor`, `datasets`, `tiktoken`, `diskcache`, `typer`.
- **Maintenance risk (concrete):** the *default branch* had not moved for ~7.5 months; the repository moved from
  `explodinggradients` to a new organisation (`vibrantlabsai`) — a maintainer/ownership transition. 634 open issues.
- **Architecture:** `src/ragas/metrics/` with 30+ metric modules — notably `_faithfulness.py:134 class Faithfulness`,
  `_answer_relevance.py:149 AnswerRelevancy`, `_context_precision.py:39/311 ContextPrecision`,
  `_factual_correctness.py`, `_noise_sensitivity.py`, `_aspect_critic.py`, `_summarization.py`, `_topic_adherence.py`;
  plus `experiment.py`, `testset/` (synthetic test-set generation), `optimizers/`, `sdk.py`, `cost.py`.
- **Factory-relevant code:** the **claim-decomposition → per-claim verdict** approach in
  `_faithfulness.py` (+ `FaithfulnesswithHHEM` using a local NLI model) is the direct mechanism for "is every factual
  claim in this chapter supported by a retrieved source?" — the single most valuable eval idea for an evidence-driven
  e-book. Port the *algorithm* (split into atomic claims → retrieve → entailment verdict → ratio) rather than the
  framework.
- **vs deepeval:** both viable; ragas is metric-diverse but visibly stalling; deepeval is actively released.

### 26. confident-ai/deepeval — Apache-2.0 — Σ26
- **URL:** https://github.com/confident-ai/deepeval · **HEAD** `4598fe8eb7be713637fbc803b9f127b5b83de69a` @ 2026-10-09 (3 013 files, 1 142 .py, ~209 k py LOC)
- **Licence:** Apache-2.0 — verified, `LICENSE.md` (Apache text). Note: the vendor sells a hosted platform; the package
  contains `telemetry.py`, `sqlite_store.py`, `key_handler.py` (cloud login paths) — usable fully offline but it does
  phone home unless disabled.
- **Architecture:** `deepeval/metrics/` — a very large taxonomy (`faithfulness/`, `hallucination/`, `g_eval/`,
  `contextual_precision/`, `json_correctness/`, `answer_relevancy/`, `bias/`, `misuse/`, `agent_loop_detection/`,
  `conversational_*`, `mcp/`, `multimodal_metrics/`, `dag/`, `arena_g_eval/`, `goal_accuracy/`),
  `deepeval/evaluate/evaluate.py:84 def assert_test(`, `test_case/llm_test_case.py:352 class LLMTestCase`,
  plus `red_teaming/`, `synthesizer/` (test-case generation), `tracing/`, `optimizer/`, `integrations/`.
- **Factory-relevant code:** `assert_test` + `LLMTestCase` are directly copyable as the factory's regression-gate shape
  (assertion + threshold + dataset), and `metrics/json_correctness/` is a ready-made "did the outline come back as valid
  structured data" check. `synthesizer/` ideas map to generating evaluation sets from a corpus.
- **Risks:** large dep surface; LLM-judge metrics cost money per run (budget gates needed); vendor telemetry.
- **vs ragas:** pick deepeval for an actively maintained, pytest-shaped harness; pick ragas for its HHEM-backed
  faithfulness if you want local entailment models.

### 27. guardrails-ai/guardrails — Apache-2.0 — Σ23
- **URL:** https://github.com/guardrails-ai/guardrails · **HEAD** `06d0ff2c5f9bcb493d976b76f885e37e41ce845d` @ 2026-08-26 (563 files, 354 .py, ~45 k py LOC)
- **Licence:** Apache-2.0 — verified, `LICENSE`. Deps include `lxml`, `openai`, `pydantic`, `litellm`, `tiktoken`, plus
  `hub_telemetry.py`, `api_client.py`, `cli/` (hub auth) — the **validator library has been moved out of the repo**:
  `guardrails/validators/` now contains only `__init__.py`, and `guardrails/hub/__init__.py:1-18` explains a lazy
  dynamic-import shim that resolves validators from `.guardrails/hub_registry.json` or the installed `guardrails_ai.*`
  namespace, with a deprecation notice.
- **Factory-relevant code:** `guardrails/guard.py:86 class Guard` + `:872 def validate(llm_output, ...)` and
  `guardrails/validator_base.py:92 class Validator` / `:206 def validate(value, metadata)` — the input/output
  validation contract, plus `guardrails/actions/` (reask/fix/refrain/noop actions) and `run/` (parallel validation).
  The "validate/reask/fix" action vocabulary is a good design for enforcing banned-claims rules on generated marketing
  copy.
- **Risks:** core value is now partly *outside* the repository (hub packages), so self-hosting the validator set means
  pinning many small packages; CLI auth paths; 72 open issues.
- **vs instructor:** instructor validates *shape*; guardrails validates *content policy*. Complementary, not competing.

### 28. mem0ai/mem0 — Apache-2.0 — Σ25
- **URL:** https://github.com/mem0ai/mem0 · **HEAD** `b7ad69afda6b6ed030347c66d48a13e4de9dec08` @ 2026-10-07 (1 836 files, 420 .py, ~125 k py LOC)
- **Licence:** Apache-2.0 — verified, `LICENSE`. Master repo also contains `mem0-ts`, `server/`, `cli/`, `evaluation/`, `integrations/`.
- **Architecture:** `mem0/memory/main.py` (`Memory.add`:760, `search`:1393, `AsyncMemory.add`:2455, `search`:3074),
  pluggable `vector_stores/`, `llms/`, `embeddings/`, `reranker/`, `configs/`, `proxy/`. Memory is extracted by an LLM
  (facts → embeddings → store) rather than stored as raw text.
- **Factory-relevant code:** the **extraction prompt + dedupe/update flow** inside `main.py` — the mechanism to keep a
  durable "what this creator/audience cares about" profile across projects. The factory already has
  `ipf/authoring/authoring_memory.py` and `ipf/observer`-free preference storage; mem0's add/search API shape is a good
  reference, and its vector-store abstraction shows how to keep the backend swappable.
- **Risks:** LLM call on every `add` (cost/latency, and a per-write model dependency); vector store required;
  vendor-hosted option encourages lock-in; 819 open issues.
- **vs alternatives:** heavier than a SQLite "memory" table; the *idea* is the asset. Lettuce-free alternative: extend
  the existing JSON/SQLite memory with an embedding index only when a concrete retrieval need appears.

### 29. microsoft/markitdown / docling / unstructured — see 12–14
(Grouped note: for the factory's ingest layer, markitdown = default, docling = high-fidelity PDFs, unstructured = only if
its element schema is adopted. All three overlap; do not install all three.)

### 30. mshumer/gpt-author — MIT — Σ14 (the cautionary reference)
- **URL:** https://github.com/mshumer/gpt-author · **HEAD** `179d86f745bc706ae39e1b7a6cb1aba9ed7fd380` @ 2024-04-02
- **Licence:** MIT — verified, `LICENSE` line 1.
- **What the README promises:** *"a chain of GPT-4, Stable Diffusion, and Anthropic API calls to generate an original
  fantasy novel… outputting an EPUB file… A 15-chapter novel can cost as little as $4… written in just a few minutes."*
- **What the repo actually contains (VERIFIED by file listing):** `Claude_Author.ipynb`, `gpt_author.ipynb`,
  `gpt_author_v2.ipynb`, `README.md`, `LICENSE`, `example_novel_outputs/*.epub` — **three notebooks and example outputs,
  no importable package, no tests, no CLI, no `setup.py`/`pyproject.toml`.**
  `find . -type f` returns 14 files total; `-- *.py` count = 0.
- **Notebook pipeline (extracted from `gpt_author_v2.ipynb`, 7 code cells, ~20 k code chars):**
  `generate_plots(prompt)` → `select_most_engaging(plots)` → `improve_plot(plot)` → `get_title(plot)` →
  `generate_storyline(prompt, num_chapters)` → `write_first_chapter(...)` → `write_chapter(previous_chapters, plot,
  chapter_title)` per chapter → `generate_cover_prompt(plot)` → `create_cover_image(plot)` (Stability text-to-image HTTP) →
  `create_epub(title, author, chapters, cover_image_path)` via **`from ebooklib import epub`**.
  Models referenced: `gpt-3.5-turbo`, `gpt-3.5-turbo-16k`, `gpt-4`, `gpt-4-32k`, `claude-2`.
- **Self-declared defects (README "Contributions" section):** *"fix the issue that causes some chapters to cut off
  early"*, *"prompts with less than 7 chapters tend to cause issues"*, and the wish-list admits early chapters should be
  compressed/summarised to avoid running out of tokens.
- **Verdict:** valuable only as a **pipeline sketch** (plot-vote → storyline → per-chapter state passing → cover →
  EPUB) and as a warning: it is the canonical "impressive README, notebook-only reality" case in this space, and its
  output path depends on AGPL `ebooklib`.

### 31. 1rgs/jsonformer — MIT — Σ17 (dead)
`bfad031876ace84ec0a7853718a1c0828ea1653a` @ **2023-05-30** (15 files, 4 .py, 354 LOC). MIT verified (`license.txt`).
Architecture: `jsonformer/{main.py (245 lines: Jsonformer.__call__:240, max_array_length:26/190,
schema-driven recursion), logits_processors.py (84 lines, per-token masking), format.py}`. Genuinely elegant
(GPT-2/LLaMA-era, `transformers`-based, logits-level schema enforcement) but **3.4 years without a default-branch
commit**, no tests, no CI. Superseded by outlines/xgrammar/llguidance. **Do not adopt.**

### 32. google-deepmind/long-form-factuality — MIT (FactScore-derived) — Σ19
`9d27158d198ced0a9d8271a80147cae580614601` @ **2024-04-07**. `LICENSE` (verified) begins *"Files:
third_party/factscore/* MIT License Copyright (c) 2023 Sewon Min"* — i.e. the vendored FactScore code is MIT.
Layout: `longfact/` (LongFact dataset), `eval/`, `data_creation/`, `common/`, `third_party/factscore/`.
Factory relevance: the **sentence-level factuality grading harness** (decompose a long text into atomic facts, verify
each against search results, report a support ratio) is exactly the metric an e-book factory needs to prove a chapter is
evidence-backed. Abandoned since 2024 — port the method, not the code.
**Better/worse:** more rigorous (and citation-anchored) than the LLM-judge-only scores in deepeval/ragas for the specific
question "is this claim true?", but it is a research release with no API, no packaging and no maintenance —
so it is a *method reference*; `ragas/_faithfulness.py` is the maintained re-implementation of the same idea.

### 33. langfuse/langfuse — MIT-Expat core + commercial `ee/` — Σ23
`cb2ceba` @ 2026-10-10 (5 292 files, 6 .py — it is a TypeScript monorepo). `LICENSE` (verified) states: content under
`ee/`, `web/src/ee/`, `worker/src/ee/` is under `ee/LICENSE` (Enterprise License), everything else is **"MIT Expat"**.
Factory-relevant code (read as a schema reference, not a dependency): `packages/shared/prisma/migrations/*` — a
step-by-step history of building **prompt versioning** (`add_prompt_table`, `prompts_add_indicies`,
`add_prompt_config`, `prompt_to_json`, `prompt_table_add_tags`, `add_prompt_version_labels`) and
`packages/shared/src/features/{prompts,evals,datasets,experiments,monitors}/`. That migration trail is a free blueprint
for a prompt registry (prompt + version + labels like `production`/`staging` + config). Self-hosting needs
Postgres + ClickHouse + Redis → too heavy for this factory; **copy the prompt-versioning data model**.

### 34. promptfoo/promptfoo — MIT — Σ28 (code-inspected on the third clone attempt)
- **URL:** https://github.com/promptfoo/promptfoo · **HEAD** `579043a` @ 2026-10-11 (`fix(eval): honor cache setting from
  a config passed with -c (#11327)`); 5 939 files on disk, ~426 MB, 1 084 `*.test.ts` files, `package.json` v0.124.1.
- **Licence:** **MIT** — verified from `LICENSE` ("Copyright (c) Promptfoo 2025" + MIT grant text) and
  `package.json` `"license": "MIT"`. Binaries: `promptfoo`, `pf` → `dist/src/entrypoint.js`.
- **Purpose:** declarative eval/red-team harness: YAML test configs, assertions (including LLM-as-judge), RAG metrics,
  synthetic data generation, red-teaming, CI integration.
- **Architecture (verified paths):** CLI command registration in `src/main.ts` (`program.command('redteam')`:93,
  `program.command('generate')`:104) → `src/entrypoint.ts` → `src/evaluate.ts` → `src/evaluator.ts`
  (`export async function runEval(...)`:1758; `class Evaluator`:4080) → assertion dispatch in `src/assertions/index.ts`
  with **one module per assertion type**: `factuality.ts`, `contextFaithfulness.ts`, `contextRecall.ts`,
  `contextRelevance.ts`, `answerRelevance.ts`, `geval.ts`, `agentRubric.ts`, `guardrails.ts`, `json.ts`,
  `javascript.ts`, `classifier.ts`, `cost.ts`, `latency.ts`, `contains.ts`, `equals.ts`, modelless string metrics
  (`bleu.ts`, `gleu.ts`, `levenshtein.ts`); local persistence via `src/database/` + `drizzle-orm` + `@libsql/client`
  (SQLite-compatible); `src/redteam/` for adversarial generation; `src/integrations/` (incl. `googleSheets.ts`,
  `microsoftSharepoint.ts`); 76 runtime deps (`openai`, `@anthropic-ai/sdk`, `zod`, `js-yaml`, otel packages).
- **Factory-relevant code:** `src/assertions/factuality.ts` and `src/assertions/contextFaithfulness.ts` are ready-made,
  testable definitions of "is this claim supported by the retrieved context" — directly usable as the factory's
  pre-publish factuality gate; `src/assertions/json.ts` + `javascript.ts` cover schema/structure assertions;
  the *YAML-config-driven assertion registry* is the pattern for letting a non-engineer tune quality gates per product
  without touching Python.
- **Integration complexity:** medium (Node/Bun toolchain). Two viable modes: (a) run the CLI in CI as a separate step
  over exported chapter JSON — no Python coupling; (b) port the assertion *definitions* into Python tests.
- **Cost:** assertions that call models cost tokens per case; modelless assertions (`bleu`, `levenshtein`, `json`,
  `contains`) are free and should be the default gate, with judge-based ones run on a sample.
- **Risks:** 679 open issues and very high release velocity (config schema drifts between versions — pin the CLI and
  commit the `promptfooconfig.yaml` schema version); Node + 76 deps adds a second toolchain to the factory's CI.
- **vs alternatives:** `deepeval` is the Python-native equivalent (pytest-shaped, larger metric taxonomy, vendor
  telemetry); `ragas` is metric-diverse but stalled. promptfoo's strengths are its **declarative, language-agnostic
  config** and its red-teaming module.

### 35. metadata-only rows (n8n, AutoGPT, Flowise, pandoc, quarto, chroma, firecrawl)
- **n8n** (`207 k★`, 2026-10-11): `LICENSE.md` read via API — **Sustainable Use License**; `.ee.` files need a separate
  enterprise licence; "content of branches other than main is not licensed". Great workflow tool, **not** a library and
  licence-restricted → do not embed.
- **AutoGPT** (`187 k★`): root `LICENSE` (API-fetched) — `autogpt_platform/` is **Polyform Shield**, everything else MIT.
  The platform directory is exactly the part you'd want, and it is the restricted part.
- **Flowise** (`55 k★`): GitHub reports **`archived: true`**, last push 2026-08-13; `LICENSE.md` puts
  `packages/server/src/enterprise/**` under a commercial licence. **Do not start new work on an archived repo.**
- **pandoc** (`46 k★`): GPL-2.0 (badge-level only — **not** file-verified). Highest-fidelity document conversion
  available; invoke as an external CLI if used at all (legal review advised).
- **quarto-cli** (`6 k★`): `COPYING.md` = MIT text (fetched via API). Excellent technical publishing pipeline
  (mdBook-like), 1 895 open issues; a plausible in-house docs/book renderer alternative to Pandoc.
- **chroma** (`29 k★`, Apache-2.0): vector DB, 876 MB repo; the factory doesn't yet need a vector service (SQLite +
  FTS5 covers the current scale) — revisit only when retrieval volume justifies it.
- **firecrawl** (`190 k★`): **AGPL-3.0** (LICENSE fetched via API) — hosted-API-first scraping/crawling. AGPL blocks
  self-hosted embedding in a proprietary product.

---

## Promised-but-not-implemented / misleading-repo findings (explicit)

| Repo | What the surface implies | What the code shows (verified) | Class |
|---|---|---|---|
| `mshumer/gpt-author` | "generates an entire novel, outputting an EPUB file" | Only 3 notebooks + sample EPUBs; zero `.py` files, no package, no tests, no CLI | **README-only project** |
| `letta-ai/letta` | 25 k★ "Platform for stateful agents" (Apache-2.0 badge) | Repo contains **12 non-git files and no source code at all**; README says source moved to `letta-ai/letta-code`; the old server is only on the `archive` branch | **Gutted/relocated repo** (licence file exists but licenses nothing present) |
| `microsoft/autogen` | "A programming framework for agentic AI" | HEAD commit = *"Update maintenance mode banner in readme"*; maintainer announcement: no significant new features | **Maintenance mode** |
| `FlowiseAI/Flowise` | 55 k★ active visual AI builder | GitHub `archived: true` | **Archived** |
| `microsoft/autogen` licence | Badge/API report `CC-BY-4.0` | Root `LICENSE` = CC-BY-4.0 but code is under `LICENSE-CODE` (MIT) | **Licence-misleading metadata** |
| `restatedev/restate` | "open-source durable execution" | `LICENSE` = BSL 1.1 with a "Public Restate Platform Service" restriction | **Source-available, not OSS** |
| `langgenius/dify` | "Apache-2.0" badge | `LICENSE` = modified Apache **plus a multi-tenant prohibition and logo/attribution condition**; the workflow engine additionally depends on external `graphon==0.7.0` | **Field-of-use restricted + partial source** |
| `1rgs/jsonformer` | constrained JSON decoding | Works as described in code, but 4 files/354 LOC abandoned 2023-05-30 | **Dead project** |
| `stanford-oval/storm` | perspective-guided article generation | Code matches claims, but pinned to `dspy_ai==2.4.9` and no default-branch commit for ~12 months | **Bit-rotting** |
| `Unstructured-IO/unstructured` | Apache-2.0 document partitioning | Ships a commercial upsell banner (`_usage_notice.py`) and `telemetry.py` | **OSS-as-funnel** |
| `Meta: gpt-author + ebooklib` | pipeline you could copy | `gpt-author_v2.ipynb` does `from ebooklib import epub`, and ebooklib is **AGPL-3.0** | **Licence trap by example** |

---

## (iii) Recommended composition

### Tier A — import as a real dependency (small, permissive, high-leverage)
| Module | Why | Licence |
|---|---|---|
| **`instructor`** | The validation→reask→retry loop for every structured generation step (outline, chapter plan, module list, worksheets). Replaces hand-rolled JSON retries. | MIT |
| **`WeasyPrint`** | Client-facing PDF export from the same HTML/CSS templates the product already needs. Pure-Python install, deterministic, no browser. | BSD-3 |
| **`reportlab`** (PyPI, not the mirror) | Only for deterministic tabular artifacts (workbooks, worksheets, checklists) where CSS layout is awkward. | BSD-3 |
| **`markitdown`** | Default ingest: EPUB/PDF/DOCX/HTML → Markdown for the corpus pipeline; per-format optional deps keep the base install tiny. | MIT |
| **`dbos-transact-py`** (or Temporal if infra exists) | Durable multi-chapter jobs on the SQLite/Postgres the factory already runs: replay, queues, cron, recovery. Slide-in for `ipf/orchestration/jobs.py`. | MIT |
| **`epubcheck`** (external jar/CLI) | Post-export conformance gate; a book that fails EPUBCheck must not ship. | BSD-3 |
| **`promptfoo`** (CLI in CI) | Declarative, language-agnostic quality gates including a ready-made `factuality` assertion; runs over exported chapter/module JSON without touching application code. | MIT |
| **`pydantic`-style schemas + `outlines`** | Only if/when local models are introduced for cost reasons; then constrained decoding beats retrying. | Apache-2.0 |

### Tier B — copy the pattern/module, do not import the framework
| Source | What to port (concretely) |
|---|---|
| `langgraph` | `BaseCheckpointSaver.put/put_writes` semantics + the `checkpoints`/`writes` DDL (`checkpoint-sqlite/.../sqlite/__init__.py:147,157`) into the existing SQLite schema, so every pipeline step is resumable. **Do not** adopt `langchain-core`. |
| `openai-agents-python` | `RunResult` shape + `Session` protocol + per-run usage accounting (`run_internal/agent_runner_helpers.py:488`, `memory/session.py:53`, `usage.py`). ~1 k LOC to mirror; avoids an OpenAI-shaped dependency. |
| `instructor` | Keep as a dependency (Tier A), but note its reask-handler error-severity idea if you ever inline it. |
| `pydantic-ai` `durable_exec/` | The **wrapper** idea: `TemporalAgent`/`DBOSAgent` wrap an existing agent rather than replacing it. Apply the same wrapper approach to the factory's own pipeline so durability is additive. |
| `dify` `api/models/workflow.py` | The pause/resume + node-execution/offload schema (`workflow_pauses:2091`, `workflow_pause_reasons:2148`, `workflow_node_execution_offload:1171`) — the factory needs an explicit "WAITING_FOR_APPROVAL" row and should offload large node payloads instead of bloating run rows. |
| `storm` `storm_wiki/modules/storm_dataclass.py` + `persona_generator.py` | `url → {snippets}` evidence index (`:48-124`) is the provenance structure for evidence-backed chapters; persona generation (`:114`) is a reusable audience/angle discovery step. |
| `gpt-researcher` `retrievers/base.py` + `utils/enum.py:6` | One uniform search/fetch interface per provider (22 examples), and explicit report-type modes. Directly maps onto the factory's `UNAVAILABLE` capability state. |
| `ragas/metrics/_faithfulness.py:134` | Atomic-claim decomposition → entailment verdict → support ratio. This is the core quality metric for a book whose selling point is factual reliability. |
| `deepeval` `assert_test`/`LLMTestCase` | Pytest-shaped regression gates with thresholds; adopt the *harness shape* and one or two metrics, not the whole 1 142-file package. |
| `promptfoo` `src/assertions/{factuality,contextFaithfulness,json}.ts` + YAML config model | The definition of each assertion (what "factual" and "faithful" mean operationally) and the idea of **quality gates as data**, editable without touching application code. Run the CLI in CI rather than reimplementing. |
| `langfuse` prompt migrations | Prompt table + version + labels + config schema; copy the data model into a `prompts`/`prompt_versions` pair of SQLite tables (see the prisma migration names in `packages/shared/prisma/migrations/`). |
| `guardrails` `Guard.validate` + `actions/` | The validate → fix/reask/refrain action vocabulary for banned-claims/marketing-copy rules. |
| `mdBook` `crates/mdbook-driver/src/{load.rs,mdbook.rs}` + preprocessor API | The renderer/preprocessor separation if the factory ever offers docs-style products (HTML course sites) in addition to EPUB/PDF. |
| `daisy/ace` report model | Accessibility QA report shape (rule id, file, severity) as a shipping gate for institutional buyers. |

### Tier C — avoid importing (or avoid entirely)
| Repo | Reason |
|---|---|
| **`ebooklib`** (AGPL-3.0) | Network copyleft. Implement the EPUB3 packager in-house: OCF `mimetype` (stored, first), `META-INF/container.xml`, OPF 3 manifest/spine/`properties`, nav doc + NCX fallback, `cover-image` property — a few hundred lines using only `zipfile`+`xml.etree`, then validate with EPUBCheck. |
| **`restate`** (BSL 1.1) | Field-of-use restriction; DBOS/Temporal cover the need permissively. |
| **`n8n`, `AutoGPT platform`, `Flowise`, `dify`, `firecrawl`, `langfuse ee/`** | Source-available / restricted / archived / AGPL. |
| **`gpt-author`** | Notebook-only; AGPL dependency; abandoned; self-documented defects. |
| **`jsonformer`** | Dead since 2023; superseded. |
| **`storm` (as a dependency)** | 12-month-stale default branch, pinned old `dspy`, heavy deps — port ideas only. |
| **`unstructured`** | spacy/numba install cost + telemetry/upsell; markitdown covers the need more cheaply. |
| **`crewAI`, `langgraph`, `haystack`, `dify` as *runtimes*** | Each would re-platform a stdlib-only factory onto a large framework; their *patterns* are the asset, and the factory's `state_machine.py` + SQLite already occupy the orchestration slot. |
| **`chroma` / vector DBs now** | Premature: retrieval volume doesn't justify a service; SQLite FTS5 first. |
| **`letta`** | Nothing in the repo to reuse. |

### Deliberate non-recommendation
No single framework in this audit should become the factory's spine. The evidence: (a) the two most star-heavy agent
platforms found are *frozen* (AutoGen), *archived* (Flowise), *gutted* (Letta) or *licence-restricted* (Dify/Restate);
(b) the repos that are genuinely maintained (openai-agents, instructor, pydantic-ai) are also the ones with the highest
API churn. A factory that owns its pipeline and borrows ~2–3 small libraries plus 6–8 patterns is more durable than one
that adopts a framework whose next major version is a migration project.

---

## Unresolved questions and empty results (honest ledger)

**Could not be determined with the tools available:**
1. **`promptfoo` internals** — RESOLVED on the third clone attempt: HEAD `579043a` @ 2026-10-11 was cloned and read
   (§34). MIT verified from `LICENSE` *and* `package.json`. Remaining unknown: whether its LLM-judge assertions are
   reproducible enough for CI gating at this factory's volume (would require executing them — out of scope).
2. **`letta-ai/letta-code`** — the current Letta source was not inspected: the GitHub API rate limit was exhausted
   (unauthenticated 60 req/h) before its licence and layout could be read. Whether it is Apache-2.0, and whether it
   contains the old memory-block (MemGPT) architecture, is **UNKNOWN**.
3. **`microsoft/agent-framework` package stability** — `python/PACKAGE_STATUS.md` was seen but not read in full; which
   Python packages are GA vs preview is **UNKNOWN** (flagged in §21 as a prerequisite before depending on it).
4. **Runtimes** — nothing was executed: no test suite run, no pipeline executed, no EPUB opened with a reader, no PDF
   rendered. All "output" claims are code-path traces.
5. **Release cadence** — shallow clones have no history, so cadence claims rest on `pushed_at` from the GitHub API and
   on in-repo CHANGELOG/release-please files, not on a tag-by-tag measurement.
6. **Security posture** — no CVE/dependency-vulnerability scan was performed (no `pip-audit`/`osv-scanner` run); the
   only security-adjacent observation is that `restate/LICENSE`, `langfuse/ee/`, `dify/LICENSE`, `n8n LICENSE.md` and
   Flowise's enterprise directory each impose non-OSS terms.
7. **`pandoc` licence** — GPL-2.0 is taken from the GitHub API badge, **not** from the repository's `COPYING` file
   (repo not cloned). Flagged in §35; re-verify before relying on it.
8. **EPUB/A11y compliance of the recommended stack** — whether WeasyPrint output + an in-house EPUB packager actually
   passes EPUBCheck and ACE end-to-end was **not tested** (would require executing code, out of scope for this audit).

**Searches / retrievals that returned nothing (recorded verbatim):**
- `git clone --depth 1 https://github.com/promptfoo/promptfoo.git` → **failed twice**:
  `error: RPC failed; curl 56 Recv failure: Connection was reset`, `fatal: early EOF`,
  `fatal: fetch-pack: invalid index-pack output` (~426 MB). **Succeeded on the third, isolated attempt**
  (`repos/promptfoo_reclone`, `579043a` @ 2026-10-11) → inspected in §34.
- `git clone --depth 1 https://github.com/langfuse/langfuse.git` → failed once (same class of error), succeeded on retry
  in a later batch.
- GitHub REST: 8 of 42 `/repos/{owner}/{repo}` calls returned `curl: (6) Could not resolve host: api.github.com`
  (transient DNS), 1 returned `301` (`explodinggradients/ragas` → renamed `vibrantlabsai/ragas`), 1 returned `404`
  (`ReportLab/reportlab` does not exist; the GitHub presence is the community mirror `MrBitBucket/reportlab-mirror`,
  canonical source is `hg.reportlab.com`).
- `GET /repos/{owner}/{repo}/contents/` for `microsoft/autogen` **succeeded** (that is how the CC-BY-4.0-vs-MIT
  `LICENSE`/`LICENSE-CODE` split was proven); the same call for `letta-ai/letta` returned
  `API rate limit exceeded for 102.159.187.17`.
- Repository search `q=reportlab in:name` returned 5 unrelated repos (`driscollis/reportlabbookcode` 190★,
  `Distrotech/reportlab` 78★, `jurasec/python-reportlab-example`, `mattjmorrison/ReportLab`) — **no canonical repo**,
  confirming the mirror situation above.
- Probe of `IBM/ACE` (intended EPUB accessibility checker) returned a **0-star empty repo** — the real project is
  `daisy/ace`, cloned and inspected as §17.

---

## Appendix — exact commands used (reproducible)

```bash
# 1. metadata (42 repos) then retry failures with backoff
curl -sSL -m 30 -H "Accept: application/vnd.github+json" -H "User-Agent: audit" \
     https://api.github.com/repos/<owner>/<repo>
# 2. shallow clone everything, 4-way parallel, 3 attempts each
git clone --depth 1 --single-branch -q https://github.com/<owner>/<repo>.git repos/<owner>_<repo>
git -C repos/<owner>_<repo> log -1 --format='%h %cI %s'
# 3. licence verification (file, never badge)
head -6 repos/<owner>_<repo>/LICENSE* | grep -viE '^\s*$|^#'
# 4. structure / entry points / state model
find repos/<owner>_<repo> -name '*.py' | wc -l
grep -n "class X\|def y" <file>
# 5. inventory table (files, .py count, py LOC, licence first line per repo)
for d in repos/*/; do git -C $d rev-parse --short HEAD; done
```
Inspection working copies: `C:/Users/DELL/AppData/Local/Temp/oss/repos/` (38 directories = 36 unique repositories +
1 duplicate re-clone + 1 successful retry).
Nothing under `C:/Users/DELL/ipf-factory` was modified except
`research/raw/oss_repository_audit.md` and `research/raw/oss_repository_sources.csv`.


