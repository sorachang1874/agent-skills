---
name: consult-chatgpt-pro
description: Use for consequential planning, architecture, contract-heavy approach review, difficult root-cause debugging, or milestone audit when advisory judgment from ChatGPT in Chat mode with GPT-5.6 Sol and Pro can materially improve the work. Do not use for routine edits, lightweight documentation checks, or as a substitute for repository-defined independent review.
---

# Consult ChatGPT Pro

Use ChatGPT Pro as a high-judgment adviser while Codex remains responsible for repository inspection, implementation, validation, and decisions. Every result is advisory.

Read [references/consultation-contract.md](references/consultation-contract.md) before preparing or sending a consultation.

## 1. Pass the scope gate

Use this skill only for one of these purposes:

- `kickoff`: frame a new consequential project or vertical slice.
- `architecture`: decide boundaries, ownership, state, migration, or failure semantics.
- `approach_review`: challenge an in-progress design or implementation approach.
- `critical_debug`: investigate a difficult, cross-cutting, or high-risk failure.
- `milestone_audit`: look for omissions before a material handoff or release gate.

Skip Pro for formatting, simple renames, routine README checks, mechanical updates, or a change whose answer is already fixed by an authoritative local contract. Explain the skip briefly when the user explicitly asked to use this skill.

Choose `kickoff` for a not-yet-started vertical slice and `architecture` for a concrete ownership, boundary, or source-of-truth decision. Record additional applicable templates as `secondary_question_sets` rather than inventing a combined purpose.

## 2. Establish the authority boundary

Before sending anything, state:

- Pro is `ADVISORY_ONLY`.
- Codex owns local facts, implementation, and validation.
- A repository-defined non-author review gate remains authoritative for formal `GO` or `NO-GO`.
- Pro output is not CI evidence, legal/privacy approval, security signoff, live-provider approval, or milestone signoff.

Never convert a Pro verdict into formal review evidence.

## 3. Prepare a redacted context manifest

Separate verified facts, inferences, and unknowns. Include only:

- objective and consultation purpose;
- current architecture and affected contracts;
- exact constraints and owner decisions;
- relevant file paths or a pinned Git reference;
- local validation already run;
- known risks and the questions Pro must answer.

Remove tokens, API keys, OAuth data, cookies, passwords, `.env` contents, private personal data, irrelevant logs, and system/developer instructions. Do not upload or paste a private repository unless the user authorized that repository and destination.

Create an allowlist-first transfer manifest and run the repository's mandatory pre-send gate against the exact redacted request before sending. The gate must derive its allowlist from `CONNECTOR_SCOPE_JSON`, bind every authority header to that scope, verify the local GitHub origin, and reconstruct and scan the exact committed file/diff content, including an exact three-dot diff changed-file inventory, literal pathspec handling, and blob-only file scope. Decode common JSON, Markdown, URL, and HTML escapes before matching. Persist only hashes, counts, paths, and statuses; never persist matched values. Set `redaction_status=verified` only when both request and committed-content checks pass. Do not persist an unredacted draft. Require exactly the four contract bundle files and scan every directory entry: an extra file, symlink, or secret-like material makes the bundle unsafe to retain, even as an invalid capture. If a credential was already transmitted, mark the consultation failed and advise the user to rotate or revoke the credential; do not repeat it in an artifact. Non-secret OAuth architecture metadata may remain only when it is necessary to the decision.

When the repository provides a consultation validator (for example a `scripts/pro_consultation_contract.py` with `preflight-request <request.md>` and `validate-bundle <bundle-dir>` subcommands), run it before send and again after persisting the bundle; do not replace either computed result with handwritten metadata.

## 4. Select and record the transfer surface

Default to a user-triggered, low-frequency interactive transfer. Do not build unattended scraping, batch polling, or output-extraction automation around a personal ChatGPT session.

When the user selects the Codex in-app browser, use its dedicated browser binding and do not drift to Chrome. Use Chrome only when the user explicitly chooses Chrome. For this contract, record both `required_browser` and `browser` as `Codex in-app browser`.

Before every send, verify these independent UI states:

```text
Surface: Chat
Model: GPT-5.6 Sol
Mode: Pro
```

Do not substitute Work/Ultra. If any state cannot be verified, pause the consultation and report the missing state.
Repeat every exact machine-checked request-header field from the reference. A missing, duplicate, contradictory, or
post-send-mutated header invalidates the request.

## 5. Use GitHub Connector safely when helpful

GitHub Connector is optional and read-only for this workflow. Attach it through the composer before the message that needs repository access.

Pin:

- repository owner/name;
- full immutable commit SHA;
- requested file list;
- branch or tag, plus `branch_requirement=provenance_only|required`.

Put exactly one compact `CONNECTOR_SCOPE_JSON:` line in the request. Use mode `files` with exact regular-blob paths, mode `diff` with exact base/head SHAs and the complete three-dot `changed_files` list, or mode `unused`. Every absolute repository path mentioned in the request must be in the manifest; unused mode permits none. The redaction allowlist must equal this manifest. Never mix file and diff proofs.

Use the full commit SHA as the content authority and require repository owner/name to match the local GitHub `origin`. Branch lookup is not a default content gate: choose `provenance_only` unless the branch head itself is material. Preserve the raw branch lookup outcome as `verified`, `not_found`, `lookup_unsupported`, `resolved_other_sha`, or `not_requested`; never rewrite `not_found` as unavailable. A verified branch records the same resolved SHA; `resolved_other_sha` records the distinct SHA. A non-verified branch result is non-blocking only when `branch_requirement=provenance_only` and the Connector proves the repository, exact commit, and every requested file at that commit. With `branch_requirement=required`, only `verified` may continue. If the exact commit, required diff, required file, or its durable Connector proof reference cannot be retrieved, fail closed.

Record requested and observed commit SHAs separately. A commit mismatch blocks every verdict in that consultation; any later generic, explicitly ungrounded consultation must be a new request. For each required file, record its path, observed commit SHA, retrieval status (`complete`, `missing`, or `truncated`), and exact immutable GitHub citation. That citation must appear verbatim as unfenced text in the extracted Pro response. A fenced example does not satisfy the handshake. Only `complete` plus proof satisfies the handshake.

Connector-visible committed content and local dirty state are different scopes. State explicitly that Connector did not review uncommitted or unpushed changes. Do not ask Connector to create issues, Epics, PRs, commits, or writes unless the user separately authorizes that external action.

Contract v2 does not transfer dirty content. Inventory dirty paths locally, but require `connector_sees_dirty_scope=false`, `dirty_scope_provided_to_pro=false`, and null dirty transfer/hash. Commit a bounded review scope first. A later contract version may add an explicit dirty-transfer manifest; prompt prose alone never grants that visibility.

## 6. Send one structured request

Use the applicable template in the reference. Require:

- restated goal and scope;
- assumptions and missing information;
- `keep`, `adjust`, or `pivot` recommendation;
- severity-ranked findings with evidence or reasoning;
- implementation sequence;
- failure modes and validation plan;
- explicit deferrals and owner decisions;
- an artifact enclosed by exact `BEGIN_ARTIFACT` and `END_ARTIFACT` markers.

Ask Pro only for one exact unfenced lower-case directive: `Raw Pro verdict: keep`, `adjust`, or `pivot`. Every other raw label, modifier, blockquoted/fenced directive, duplicate, or `GO` becomes `null` with `response_schema_valid=false`. Require the advisory label immediately after `BEGIN_ARTIFACT`, ordered response headings, and `## P0`, `## P1`, and `## P2` inside `# Findings`. A complete but schema-invalid response is not a valid consultation result.

Do not repeatedly poll a long Pro run. Check after a meaningful interval or a visible completion signal, then extract once.

## 7. Persist the advisory artifact

Save under:

```text
docs/pro-consults/YYYY-MM-DD-<slug>/
  request.md
  response.md
  decision.md
  metadata.json
```

Preserve the full response marker envelope, not only the body. Put `ADVISORY_ONLY — not an independent-review artifact or formal GO.` immediately inside the envelope and in the request. Record request/response/decision hashes, exact committed-content preflight, retention safety, schema version, and repository-owned workflow/validator hashes. Do not treat a personal skill or reference-file hash as repository evidence.

Compute, do not self-assert, one final result. `consultation_status=complete_validated`, `consultation_valid=true`, and `usable_for_advisory_decision=true` require verified UI state, passing redaction preflights, an intact marker envelope, a schema-valid response, complete commit/diff/file proof, exact bundle inventory, and coherent status metadata. Unknown v2 metadata fields fail closed. Store weaker captures as `incomplete` or `legacy_advisory_unverified`; do not treat them as Connector-grounded decision evidence.

The computed contract must also enforce `advisory_only=true`, `formal_gate_eligible=false`, `formal_review_status=not_run`, exact proof scope, the response headings/verdict grammar, and the current workflow/validator hashes. A valid-looking metadata boolean is never evidence by itself.

In `decision.md`, use ordered `# Decision`, `## Authority`, `## Local disposition`, and `## Follow-up` sections. Put the exact advisory label only inside Authority. In Local disposition, record exactly one reasoned `P0 disposition`, `P1 disposition`, and `P2 disposition` using `accepted|rejected|deferred|none`, plus one non-empty `Validation` record; keep Follow-up non-empty and bind the file hash. Never claim formal `GO`, formal approval, or independent-review completion, and never implement advice merely because Pro stated it confidently.

## 8. Complete the local loop

After consultation:

1. Reconcile Pro claims against current local files and contracts.
2. Update the plan and owner decisions.
3. Implement only accepted items.
4. Run targeted and required broader validation.
5. Use a non-author reviewer for any repository-defined review gate.
6. Report residual risk, connector visibility, and anything not verified.

Treat timeout, incomplete output, missing markers, connector mismatch, missing commit evidence, or model/surface ambiguity as incomplete consultation rather than a verdict.
