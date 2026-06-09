# hexr-signatures

Declarative YAML signature packs for the Hexr SDK.

Each pack identifies a Python agent framework (e.g. langchain, crewai) or
an agent pattern (e.g. reflection, evaluator-optimizer) so the Hexr SDK's
Rust analyzer can classify and instrument agents without any framework-specific
code in the SDK itself.

**Architecture rationale:** `HEXR_SDK_OVERHAUL_TECH_SPEC.md` §17 — *"Adding a
new framework or pattern is a YAML PR, not a Rust release."*

**Wire format + verification:** `HEXR_SDK_OVERHAUL_TECH_SPEC.md` §21.4 —
CalVer releases, cosign keyless signing, atomic `latest.json` pointer.

## Add a new framework or pattern

1. Open a PR with a new YAML file under `packs/frameworks/` or `packs/patterns/`.
2. The `validate` workflow checks:
   - yamllint
   - JSON Schema (`schema/pack.schema.json`)
   - smoke-build against the current published `hexr-sdk` wheel
3. Maintainer reviews + merges.
4. Maintainer pushes a tag (CalVer: `YYYY.MM.PATCH`) to release. The `release`
   workflow signs the tarball with Sigstore cosign keyless and uploads to GCS
   behind `https://signatures.hexr.cloud/`.

## Consume

Customers run:
```
pip install 'hexr-sdk[ops]'   # adds sigstore for verification
hexr update signatures        # default source: https://signatures.hexr.cloud/latest.json
```

Verification happens client-side: the SDK pins the Fulcio cert subject regex
and OIDC issuer at build time — no public keys to rotate, no PKI to operate.

## Trust root

Signatures are signed by this repo's `release.yml` workflow's OIDC token.
The SDK's [`update.py`](https://github.com/hexrdev/hexr/blob/main/sdk/python/src/hexr/cli/update.py)
pins:
- Fulcio cert subject regex: `^https://github\.com/hexrdev/hexr-signatures/\.github/workflows/release\.yml@refs/tags/.+$`
- OIDC issuer: `https://token.actions.githubusercontent.com`

Modifying `release.yml` does NOT change the trust root unless a corresponding
SDK release also moves the pinned regex. This is intentional.

## License

Apache-2.0. Packs are public; verification is the security boundary, not
secrecy.
