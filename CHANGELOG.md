# Changelog

## 2026.09.1 — 2026-09-11

Adds `tool_call` and `parallel_call` idiom rules so L3 (`hexr analyze
--suggest`) can actually propose them.

Only four idiom buckets seed L3's corpus — `llm_call`, `tool_call`,
`agent_spawn`, `parallel_call` — and before this release `tool_call` had **1**
rule across all 15 packs and `parallel_call` had **0**. The suggest call uses
`top_k 5` with `min_agree 3`, so three neighbours must agree on a label:
CallClass::Tool and CallClass::Parallel could never be proposed, silently
(hexr spec §22.13).

  tool_call      1 -> 12
  parallel_call  0 ->  9

Added, all verified by importing the real package before writing the rule:

  langchain    tool_call     BaseTool.invoke / ainvoke / run / arun
               parallel_call Runnable.batch / abatch, RunnableParallel
  crewai       tool_call     tools.BaseTool.run
               parallel_call Crew.kickoff_for_each / kickoff_for_each_async
  llamaindex   tool_call     FunctionTool.call / acall
  langgraph    tool_call     prebuilt.ToolNode.invoke / ainvoke
               parallel_call types.Send
  smolagents   tool_call     Tool.forward
  mcp          tool_call     ClientSession.call_tool
  bespoke      parallel_call asyncio.gather,
                             ThreadPoolExecutor.map / submit

Deliberately NOT added: `pydantic_ai.tools.Tool.run` (no such attribute) and
autogen tool invocation (module did not import for verification). Guessing API
surface would pollute both detection and the seed corpus.

`tool_call` holds tool INVOCATIONS and is distinct from the `tool` bucket,
which holds tool DEFINITIONS (decorators, BaseTool subclasses). Mapping the
latter into the corpus would seed call-site retrieval with definition sites.

## 2026.09.0 — 2026-09-11

No pack changes. Identical content to 2026.06.0 (15 frameworks, 7 patterns).

Re-cut solely to publish a valid SLSA build-provenance attestation. The
`.intoto.jsonl` shipped with 2026.06.0 is 268 bytes of `gh attestation
download` status output rather than a DSSE bundle — the release workflow
redirected stdout, while that command writes the bundle to a file named
`sha256:<digest>.jsonl`. The feed therefore advertised provenance it could not
actually provide, and nothing caught it because no client fetched the file.

hexr-sdk 0.5.15 verifies provenance (predicate type + subject digest against
the downloaded tarball), which is what surfaced this. The workflow now takes
the file `gh` writes and validates the statement before publishing, failing the
release rather than shipping an unverifiable attestation.

All notable releases of `hexr-signatures` packs.

Format: CalVer (`YYYY.MM.PATCH`). Drives `latest.json.frameworks_added` /
`frameworks_updated` / `frameworks_removed`.

## Unreleased

## 2026.06.0 — first signed release

Initial cut-over from in-wheel-only distribution (SDK v0.5.5 and earlier) to
the signed-feed distribution channel (SDK v0.5.6+).

**Frameworks (15):** adk, agno, autogen, bespoke, claude-agent, crewai, dspy,
langchain, langgraph, llamaindex, mcp, openai-agents, pydantic-ai, smolagents,
strands.

**Patterns (7):** evaluator-optimizer, orchestrator-workers, parallelization,
prompt-chain, react, reflection, routing.

Pack contents are byte-identical to what shipped inside `hexr-sdk==0.5.5`'s
`_native/packs/` tree, so customers upgrading from 0.5.5 → 0.5.6 + `hexr update
signatures` get the same baseline plus the ability to receive out-of-band
updates without an SDK upgrade.
