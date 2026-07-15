# Release Notes

## v1.0.16
GH#844 — migrate the gate step from node-meta (`metadata.gate: true` in the skill) to the canonical execution-entry `gate: true` on the workflow step. Single source of truth; the engine + app read the execution entry. No behaviour change — `IsGate` is identical.

## v1.0.15
GH#833 — drop the embedded `github-mcp` service node; dep-reference the canonical `_shared/github-mcp@1.0.0`. This bundle was the source of the canonical content (current API). contents.services 1→0.

## v1.0.14
GH#745 — declare per-step `output: {name, type}` on every execution step (pr_data/text, security_review/text, quality_review/text, style_review/text, gate_decision/decision, posted_review/text). Lights up the #744 rich flow-map. Content-only; no bindings or logic changes.

## v1.0.13
Fix-forward after Row 3b v1.0.12 publish failure. The v1.0.12 per-skrpt CI's "Register version with Hub API" step failed because the consumer's source `manifest.id` (9de9f1f1…) did not match the D1 catalog row's id (946666b0…) — a legacy drift from before Action 6 (`0bcc5ae0`) made publish-skrpt.mjs Step 2 INSERT use `manifest.id` for the D1 id column. v1.0.13 reconciles the source `manifest.id` to the catalog authoritative value (Row-5-equivalent for consumers) and republishes. Per Adj-1: no re-tag of v1.0.12; the orphaned GitHub release artefact stays inert (no D1 versions row, no consumer pinned it).

## v1.0.12
GH#645 Row 3b — migrate to K-037 dep-referenced schema. Strip 1 inline shared-content file and declare 1 hub-shared dep (UUID id + slug name + version + checksum from `gen-dep-checksums.mjs`). Closes pre-Step-3 inline-vendoring for this bundle.

## v1.0.11
Wave 2: re-signed with canonical engine signing pipeline.

## v1.0.10
Tags migrated inline into manifest (GH#586). tags.yaml retired.

## v1.0.9
Bundle re-signed with canonical engine signing pipeline (Wave 2 migration).

## v1.0.8
Signature fix — RELEASE_NOTES.md now included in integrity checksum.

## v1.0.7
Initial catalog release with full structural and content-quality validation. All scanner checks pass.
