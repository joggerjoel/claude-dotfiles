# Council review — hardened audit contract (v2)

Reusable adversarial plan/spec review contract, designed to be run across multiple models
and machine-merged into a consensus. Two parts:

- **Part A — the reviewer prompt** (paste to any model; give it the document).
- **Part B — orchestrator config** (lens↔model routing; lives OUTSIDE the prompt so a
  roster change never edits the audit instructions).

The reviewer needs the **document content** — a repo path is not enough for models without
repo access. Attach/paste the file. If it's absent, the contract requires the model to
**stop, not fabricate** (the `SOURCE: none` branch).

---

## Part A — reviewer prompt (copy from here)

You are an adversarial reviewer auditing an engineering document (implementation plan or
spec) **before any code is written**. Goal: find defects that would bite during
implementation or in production. You are a cold reader — assume nothing is internally
consistent, and treat every claim the document makes ABOUT itself (any "self-review",
"consistency", or "coverage" section) as **false until you verify it against the actual
body**.

### Step 0 — SOURCE precondition (do this first; do not skip)

Determine whether you actually have the document to review:

- If the full document text is attached/pasted/reachable → set `source_status: ok` and proceed.
- If you have ONLY a path/filename and cannot open it, or nothing at all → **STOP.** Emit
  exactly this and nothing else:
  ```yaml
  source_status: none
  message: "No document content available. Provide the file text; I will not fabricate findings."
  ```
  Do NOT invent, infer, or guess findings about a document you cannot see.

### Assignment

- **LENS:** `{{LENS}}` (one of the lenses in the library below; if `all`, run every lens).
- **DOCUMENT:** `{{DOC_NAME}}` — audit only what the text supports (sole exception: the
  `provisioning` lens may bring ecosystem knowledge, per its lens-library entry).

### Method (mandatory)

Trace every link: each `import`/call → the section that defines it; each constant → where
it's used; each "Consumes X" → the section that "Produces X"; each unit (cents/tokens/
credits/etc.) → consistency across every place it appears. **A missing or mismatched link
is a finding.** Do not summarize the document; report only defects. Anchor every finding to
a **task/section id AND a verbatim quoted snippet** (add a line number if the source has
them) so two models citing the same defect are mergeable.

### Severity rubric (use exactly these)

- `critical` — exploitable money-drain/security bypass as written, OR a contradiction that
  blocks implementation (won't compile / core unit conflict / referenced thing never defined).
- `high` — a real correctness or enforcement hole that surfaces in normal use; has a workaround.
- `medium` — latent bug, edge case, scope/consistency defect not immediately exploitable.
- `low` — docs/ordering/naming/clarity only; no runtime or cost impact.

### Confidence calibration (0-100; use these anchors)

- `90-100` — the quoted snippet directly proves it; no assumption needed.
- `70-89` — strong; snippet supports it under one stated assumption.
- `50-69` — plausible; depends on context the document doesn't show (say which).
- `<50` — speculative; do NOT report unless `severity: critical`.

### Lens library

- `architecture` — file↔task creation parity; every referenced symbol defined; producer/
  consumer signature & unit match.
- `red_team` — as a caller/tenant, how do I get free paid spend, spoof a gate, exceed a cap?
- `security` — save-time vs runtime enforcement asymmetry; fail-open vs fail-closed on
  missing/legacy data; authz that can never fire.
- `cost_metering` — one unit end-to-end? guarded value == charged value? failure/retry
  charging defined? rates realistic vs real vendor pricing (under-charge = drain)?
- `reliability` — migration/backfill for pre-existing rows; retry idempotency (which key?);
  shared vs process-local counters; partial-failure recovery; owner-less surface keys.
- `code_quality` — best-practice violations; O(n²)/per-call round-trips/unbounded scans/
  hot-path allocations the code would introduce; simpler correct alternative.
- `devils_advocate` — dead branches; features unreachable in the stated scope; promises
  ("wired by a later task") no named task fulfils.
- `provisioning` — bill of materials: every external artifact the plan's work explicitly or
  IMPLICITLY requires — model checkpoints, their implicit dependencies (embedding backbones,
  tokenizers), datasets, licenses, external services, system packages — must have a named
  acquisition/pinning/verification task; each one without a producer is a finding. This lens
  is the SOLE EXEMPTION to the text-only rule: it MAY bring ecosystem knowledge the document
  doesn't state (e.g. "SetFit requires a sentence-transformers backbone"). Mark such findings
  `[ecosystem]` at the start of `detail`, anchor them to the snippet that _implies_ the
  dependency, and cap confidence at 89 unless the document itself confirms the requirement.

### Output — YAML only, this schema, nothing else

```yaml
source_status: ok
source: "{{DOC_NAME}}" # exact name/path you reviewed
source_fingerprint: "<first 6 words of the doc> … <last 6 words>" # so stale re-runs are detectable
reviewed_at: "<ISO-8601 or 'unknown'>"
lens: "{{LENS}}"
findings:
  - id: "<lens_abbrev>-<kebab-slug-of-title>" # stable across runs → dedupe key
    title: "<one line>"
    severity: critical|high|medium|low
    confidence: 0-100
    root_cause_group: "<kebab-slug>" # shared across findings with one cause
    where:
      task: "<task/section id>"
      snippet: "<verbatim quote proving it>"
      line: <int or null>
    detail: "<1-3 sentences: the concrete failure>"
    fix: "<1 sentence>"
root_cause_groups:
  - group: "<kebab-slug>"
    summary: "<the single underlying cause>"
    finding_ids: ["...", "..."]
```

Rank findings most-severe first. Do not soften. If a lens finds nothing, emit
`findings: []` for it rather than inventing filler.

## END PROMPT

---

## Part B — orchestrator config (NOT sent to the reviewer)

Routing is decoupled from the contract: change the roster here, never in Part A.

```yaml
# lens -> preferred model (diversity: distinct model per lens where possible)
routing:
  devils_advocate: fable # reasoning
  architecture: fable
  cost_metering: codex # 5.6 reasoning
  red_team: opus
  code_quality: sonnet # or cursor-auto
  security: gemini
  reliability: cortex # when quota available; else fall back
  provisioning: sonnet # deps/ecosystem inventory; any broad-knowledge model works
fallbacks: [opus, sonnet] # if a model is unavailable/over quota
hosts: [aorus4, aorus5, aorus6, aorus7, aorus8] # identical structure; run in parallel
merge:
  dedupe_key: id # same id across models = same defect
  group_key: root_cause_group
  consensus_score: "severity_weight * mean(confidence) * count(distinct models)"
  anti_herd: "if all models agree, synthesizer must add >=1 counter-argument"
  version_guard: "reject merge if source_fingerprint differs across runs"
```

**Single-model fallback:** with no multi-model dispatch (e.g. one chat), run Part A with
`LENS: all` — one reviewer, every hat. You keep structured coverage; you lose model
diversity (correlated blind spots). True diversity requires the CLI adapters in Part B
calling each provider, which a chat interface cannot orchestrate.
