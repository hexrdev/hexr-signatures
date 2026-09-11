# Changelog

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
