---
type: incident
status: resolved
owner: Chief of Staff
date: 2026-07-01
reviewed: 2026-07-03
---

# Incident: Atlas cron fails with "Encrypted content is not supported"

> **Promoted 2026-07-03** from `Agents/patches/avoid-encrypted-include/` (finding M2 from the 2026-07-03 audit), the postmortem was already complete and dated, it just lived outside the canonical incident record (`Operations/Monitoring/Incidents/`, empty until now). See also [[project_atlas_encrypted_content_bug]] in session memory.

## What failed

Some Atlas cron jobs (e.g. `daily-vault-capsules`) failed with `HTTP 400: "Encrypted content is not supported with this model."`. The error was published in public channels, causing loss of trust in automation.

**Evidence:** `/srv/finco/atlas-homes/default/sessions/request_dump_cron_374cef6cc334_20260701_131256_20260701_171257_236842.json` (VPS).

## Root cause

API compatibility bug, not a content/security block. The Atlas-Agent framework (NousResearch, v0.14.0) has a branch in `agent/transports/codex.py::ResponsesApiTransport.build_kwargs()` that, for the generic OpenAI Responses backend, always sends `reasoning` + `include: ["reasoning.encrypted_content"]` when `reasoning_enabled` is `True`, without checking whether the target model supports reasoning. An analogous check existed only for xAI, not for OpenAI.

The 2026-07-01 incident occurred because a cron job fell back to `gpt-4o-mini` (hardcoded in `agent/auxiliary_client.py` when the main model cannot be read) while `reasoning_effort: medium` remained active globally. `gpt-4o-mini` does not support reasoning → OpenAI responded 400 → the job aborted without retry (the error classifier only retries code `invalid_encrypted_content`, which does not match this message).

## Corrective action

Added `model_supports_encrypted_reasoning(model, provider="")` in `agent/model_metadata.py` (same style as `grok_supports_reasoning_effort`). Resolves capability via `models_dev_cache.json` and falls back to a static allowlist (`o1`, `o3`, `o4`, `gpt-5*`) if no data, conservative: without positive evidence, does not send `reasoning`/`include`.

Applied at two points: `agent/transports/codex.py` (`build_kwargs()`, main agent path) and `agent/auxiliary_client.py` (`_CodexCompletionsAdapter.create()`, auxiliary calls like context compression).

**Verification:** `py_compile` OK on all 3 files; isolated test confirmed `gpt-4o-mini` → no `reasoning`/`include` (previously: crash) and `gpt-5-mini` (real model) → unchanged behavior, no regression. Image `finco/atlas-agent:local` rebuilt and containers recreated without state loss (`cron/jobs.json` intact).

**Rollback available:** backup of the 3 files at `/srv/finco/backups/atlas-agent-source-encrypted-fix-20260701/`; previous image preserved as `finco/atlas-agent:pre-encrypted-fix-20260701` (digest `bcbc03663854`).

## Prevention / pending

- Why `_read_main_model()` returned empty in the cron that triggered the fallback was not investigated, the guard prevents the crash but does not fix that underlying cause.
- The fix lives only in the local VPS checkout (`/srv/finco/runtime/atlas-agent-source`) and in the `finco/atlas-agent:local` image; it was not submitted as a PR to the upstream repo `NousResearch/Atlas-Agent`. If the base image is updated from upstream, the patch is lost and must be reapplied (diffs in the backup).

---

*FinCo-OS · Operations/Monitoring/Incidents/2026-07-01-atlas-cron-encrypted-content.md · 2026-07-03*
