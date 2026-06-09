# Changelog

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
