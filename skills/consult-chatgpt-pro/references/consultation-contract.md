# ChatGPT Pro consultation contract

## Request header

Use this header for every consultation:

```markdown
Purpose: <kickoff|architecture|approach_review|critical_debug|milestone_audit>
Secondary question sets: <comma-separated other purpose names or none>
Authority: ADVISORY_ONLY
Surface required: Chat
Model required: GPT-5.6 Sol
Mode required: Pro
Browser required: Codex in-app browser
Local state: <clean|dirty>
Connector sees dirty scope: false
Dirty scope provided to Pro: false
Branch: <branch label or none>
Branch requirement: <provenance_only|required|not_applicable>
Repository: <owner/name or none>
Commit authority: <full SHA or none>
Consultation status: planned
Connector status: <attached_pending|unused>
Redaction status: verified
```

Immediately after the header, include exactly one compact source-of-truth line:

```text
CONNECTOR_SCOPE_JSON: {"commit_sha":"<40-hex>","mode":"files","repository":"owner/repo","required_files":["/exact/path"]}
CONNECTOR_SCOPE_JSON: {"base_sha":"<40-hex>","changed_files":["/exact/path"],"head_sha":"<40-hex>","mode":"diff","repository":"owner/repo"}
CONNECTOR_SCOPE_JSON: {"mode":"unused"}
```

## Common context shape

```markdown
## Objective
<one outcome>

## Verified facts
- <fact with local or connector-backed source>

## Inferences
- <clearly labelled inference>

## Unknowns
- <unknown that may change the recommendation>

## Constraints and non-goals
- <owner decision or safety boundary>

## Validation already run
- `<exact command>` -> <result>

## Questions
1. <decision question>
```

## Purpose-specific questions

### kickoff

- What is the smallest end-to-end slice that tests the riskiest assumptions?
- Which contracts, owner decisions, and release gates must exist before implementation?
- What should explicitly remain out of scope?

### architecture

- Where should ownership and source of truth live?
- Which state machines, idempotency keys, migrations, and recovery paths are required?
- What bounded step retires an old heuristic or ambiguous fallback?

### approach_review

- Which parts should we keep, adjust, or pivot?
- Which downstream readers/writers or failure modes are missing?
- Which tests would fail before the proposed correction?

### critical_debug

- What causal mechanism best explains the evidence?
- Which discriminating checks separate the top hypotheses?
- What root-cause correction prevents recurrence without masking the symptom?

### milestone_audit

- What could still block production, migration, recovery, or operator use?
- Are public contracts and UI states coherent during partial and terminal states?
- Which claims lack independent evidence?

## Required response shape

```markdown
BEGIN_ARTIFACT path=<requested relative path>
ADVISORY_ONLY — not an independent-review artifact or formal GO.

# Verdict
Raw Pro verdict: <keep|adjust|pivot>

# Scope understood
...

# Assumptions and missing information
...

# Findings
## P0
## P1
## P2

# Recommended sequence
...

# Validation and failure modes
...

# Deferred decisions
...

# Owner decisions required
...
END_ARTIFACT
```

If Connector is used, add repository, requested and observed full commit SHAs, `commit_verification`, per-file retrieval status and proof references, `branch_requirement`, `branch_lookup_outcome`, and `branch_verification`. Allowed commit values are `verified`, `unavailable`, and `mismatch`; unavailable or mismatch blocks every grounded verdict. Raw branch lookup outcomes are `verified`, `not_found`, `lookup_unsupported`, `resolved_other_sha`, and `not_requested`. `branch_verification` preserves the corresponding normalized fact (`verified`, `not_found`, `lookup_unsupported`, `mismatch`, or `not_applicable`). Only `verified` satisfies `branch_requirement=required`; other outcomes may continue only for `provenance_only`, without claiming branch verification.

Each required file proof is `{path, observed_commit_sha, retrieval_status, citation}`. The repository must match local
GitHub `origin`; the citation must be the exact immutable GitHub URL
`https://github.com/<owner>/<repo>/blob/<full-sha>/<path>` and must appear verbatim as unfenced text in the Pro response. Diff proof uses
`https://github.com/<owner>/<repo>/compare/<base>...<head>`. Diff mode requires `changed_files` to equal the complete
local `git diff --name-only <base>...<head>` inventory. Only regular Git blobs are valid file scope; directories,
submodules, binary diff paths, special pathspec interpretation, missing/truncated content, generic conversation links,
and uncited claims fail closed. The mandatory `preflight-request` command reconstructs and scans the exact committed
content before send; `validate-bundle` repeats the check after capture.

## Artifact metadata

`metadata.json` should contain at least:

```json
{
  "purpose": "architecture",
  "secondary_question_sets": ["kickoff"],
  "advisory_only": true,
  "formal_gate_eligible": false,
  "formal_review_status": "not_run",
  "contract_schema_version": "chatgpt-pro-consultation.v2",
  "consultation_status": "complete_validated",
  "request_status": "planned",
  "request_connector_status": "attached_pending",
  "consultation_valid": true,
  "usable_for_advisory_decision": true,
  "failure_reason": null,
  "requested_at": "<ISO-8601>",
  "completed_at": "<ISO-8601 or null>",
  "required_surface": "Chat",
  "required_model": "GPT-5.6 Sol",
  "required_mode": "Pro",
  "required_browser": "Codex in-app browser",
  "observed_surface": "Chat|Work|null",
  "observed_model": "<UI label or null>",
  "observed_mode": "<UI label or null>",
  "ui_state_verification": "verified|blocked",
  "browser": "Codex in-app browser",
  "repository": "<owner/name or null>",
  "requested_commit_sha": "<full SHA or null>",
  "observed_commit_sha": "<full SHA or null>",
  "commit_verification": "verified|unavailable|mismatch|not_applicable",
  "branch": "<label or null>",
  "branch_requirement": "provenance_only|required|not_applicable",
  "branch_lookup_outcome": "verified|not_found|lookup_unsupported|resolved_other_sha|not_requested",
  "branch_verification": "verified|not_found|lookup_unsupported|mismatch|not_applicable",
  "branch_resolved_sha": "<full SHA or null>",
  "connector_status": "unused|attached_pending|commit_pinned|blocked|legacy_unverified",
  "outcome_code": "connector_ok_commit_pinned|connector_blocked|connector_evidence_incomplete|none",
  "connector_evidence_valid": true,
  "connector_scope_manifest": {
    "mode": "files|diff|unused"
  },
  "connector_scope_manifest_sha256": "<hex>",
  "local_state": "clean|dirty|unknown",
  "connector_sees_dirty_scope": false,
  "dirty_scope_provided_to_pro": false,
  "dirty_scope_transfer": null,
  "dirty_diff_sha256": null,
  "local_dirty_inventory_status": "clean|recorded_not_transferred",
  "local_dirty_paths": [],
  "local_dirty_path_count": 0,
  "local_dirty_inventory_sha256": "<hex>",
  "redaction_status": "verified|blocked|legacy_unverified",
  "redaction_preflight": {
    "rules_version": "<version>",
    "request_sha256": "<hex>",
    "included_paths": ["/exact/path"],
    "excluded_categories": ["credentials", "oauth_session"],
    "manifest_sha256": "<hex>",
    "allowlist_status": "passed|blocked",
    "secret_match_count": 0
  },
  "transfer_content_preflight": {
    "mode": "files|diff|unused",
    "scope_manifest_sha256": "<hex>",
    "requested_paths": ["/exact/path"],
    "actual_changed_files": [],
    "changed_file_inventory_matches": true,
    "binary_path_count": 0,
    "non_blob_path_count": 0,
    "content_sha256": "<hex>",
    "secret_match_count": 0,
    "status": "passed|unused"
  },
  "retention_safe": true,
  "response_complete": true,
  "response_marker_envelope_preserved": true,
  "response_schema_valid": true,
  "raw_verdict": "keep|adjust|pivot|null",
  "normalized_recommendation": "keep|adjust|pivot|null",
  "request_sha256": "<hex or null>",
  "response_sha256": "<hex or null>",
  "decision_sha256": "<hex or null>",
  "validator_sha256": "<hex>",
  "workflow_sha256": "<hex>"
}
```

## Failure handling

- Missing, unavailable, or mismatched exact commit, or any required file that is missing/truncated: set `consultation_status=blocked_connector`, `connector_status=blocked`, `outcome_code=connector_blocked`, the appropriate `commit_verification`, and a precise `failure_reason`; emit no verdict. Any generic advice requires a separate explicitly ungrounded request.
- Branch outcome is `not_found`, `lookup_unsupported`, or `resolved_other_sha`: preserve that exact fact. Continue only when the branch requirement is `provenance_only` and immutable commit/file proof is otherwise complete; never claim the branch was verified. A `required` branch blocks.
- Connector content and local dirty diff differ: contract v2 records dirty paths locally but never transfers dirty
  content. Commit a bounded scope first; do not imply Connector visibility from prompt prose.
- Redaction cannot be verified: set `blocked_pre_send`; do not save or send the draft.
- A credential was already transmitted: mark the consultation failed, avoid repeating it, and advise rotation/revocation.
- Missing response markers or truncated response: preserve partial output with `response_complete=false`; do not infer the rest.
- Pro says `GO`, adds a modifier, duplicates the directive, or fences it: set the raw/normalized verdict to null, keep
  `formal_gate_eligible=false`, and run the formal non-author gate separately.

Codex performs response validation after extraction. Exactly one unfenced lower-case `Raw Pro verdict: keep|adjust|pivot`
inside the Verdict section maps to the matching normalized value. Any other label maps to
`normalized_recommendation=null` and `response_schema_valid=false`. The response headings are ordered and P0/P1/P2
must be inside Findings. `decision.md` is hash-bound and uses `# Decision`, `## Authority`, `## Local disposition`, and
`## Follow-up`; the exact advisory label is inside Authority. Local disposition contains exactly one reasoned line for
each of `P0 disposition`, `P1 disposition`, and `P2 disposition`, plus one non-empty `Validation` line; Follow-up is
non-empty. No formal GO, formal approval, or independent-review-completion claim is allowed. Final
`consultation_valid`, `usable_for_advisory_decision`, and `storage_valid` are computed from all required evidence; they
are never operator assertions. The request header and Connector scope are machine checked; absolute repository paths
outside the manifest fail. The bundle directory contains exactly four regular files—extra files and symlinks make even
storage-only validation fail—and unknown v2 metadata keys fail
closed. Common encoded JSON/Markdown/URL/HTML secret representations are decoded before matching; a secret-like value
in any persisted bundle entry makes storage invalid.
