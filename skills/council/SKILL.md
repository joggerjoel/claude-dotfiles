---
name: council
description: Convene an adversarial "council" to audit an engineering plan, spec, or design doc BEFORE any code is written. Runs a fixed set of lenses (architecture, red-team, security, cost-metering, reliability, code-quality, provisioning/bill-of-materials, devil's-advocate), each emitting structured YAML findings, then machine-merges them into a single ranked consensus — dedupe by id, group by root cause, consensus scoring, and an anti-herd counter-argument when every reviewer agrees. Use when the user says "council review", "counsel-council", "convene the council", "review/audit this plan or spec", "red-team this design", or otherwise wants a hardened pre-implementation review of a plan, spec, or design document.
---

# Council review

An adversarial, multi-lens audit of an engineering **document** (implementation plan / spec /
design doc) **before code is written**. The council runs 8 lenses over the document, each a
cold reader that trusts nothing the doc claims about itself, then merges every lens's structured
findings into one ranked consensus. The full reviewer prompt and orchestrator config live in
`references/reviewer-contract.md` — read it before running.

## Step 0 — SOURCE precondition (never skip)

You must have the **document text**, not just a path. If the file is readable, read it now. If
you have only a filename you cannot open and nothing was pasted, **stop** and ask for the text —
do not invent findings. This mirrors the contract's `source_status: none` branch.

Compute a `source_fingerprint` (first 6 words … last 6 words) once, up front. Every lens must
review the _same_ text — reject any merge where fingerprints disagree (`version_guard`).

## Step 1 — pick the execution mode

Three tiers, best first. Default to the best one available in the current environment.

1. **Parallel subagents (default in a Claude Code session).** Dispatch one subagent per lens
   with the Agent tool (`subagent_type: general-purpose`), all in a single message so they run
   concurrently. Each gets: the full document text, the reviewer prompt from
   `references/reviewer-contract.md` Part A, and its assigned `LENS`. Structured 8-lens coverage
   with process isolation; reviewers are all Claude, so blind spots are partially correlated —
   the anti-herd step below compensates.
2. **Fleet multi-model (highest diversity).** Use only when the user is driving this from the
   aorus CLI adapters. Route each lens to a distinct provider per `references/reviewer-contract.md`
   Part B (`routing:` block), run across the `hosts:`, collect each YAML. A single chat cannot
   orchestrate this — describe it, hand over the per-lens prompts, or drive it via Bash if the
   adapters are on PATH. Do not fake diversity by relabeling one model as five.
3. **Single-model six-hats (fallback).** No subagents available → run Part A yourself once with
   `LENS: all`, producing one YAML with findings from every lens. Cheapest; least diverse.

State which tier you used so the user knows the diversity level of the result.

## Step 2 — run the lenses

The 8 lenses (see the contract for each one's exact mandate):
`architecture`, `red_team`, `security`, `cost_metering`, `reliability`, `code_quality`,
`provisioning`, `devils_advocate`.

Each reviewer must:

- Trace every link (import→definition, constant→use, "Consumes X"→"Produces X", units end-to-end).
  A missing or mismatched link is a finding.
- Stay inside the document's text — except `provisioning`, the one lens allowed to bring
  ecosystem knowledge (implicit model backbones, dataset/license needs); it marks such findings
  `[ecosystem]` and anchors them to the snippet that implies the dependency.
- Anchor every finding to a **task/section id + a verbatim quoted snippet** (that quote is what
  makes two reviewers' findings mergeable).
- Use the exact `severity` rubric and `confidence` (0-100) anchors from the contract.
- Emit **YAML only**, exactly the contract's schema. A lens that finds nothing emits `findings: []`
  — never filler.

## Step 3 — merge into consensus

Collect every lens's YAML, then:

- **Dedupe** by finding `id` (stable kebab slug) — the same defect found by N reviewers collapses
  to one entry that records how many distinct reviewers/models raised it.
- **Group** by `root_cause_group` so N symptoms of one underlying cause read as one root cause.
- **Score** each merged finding: `severity_weight × mean(confidence) × count(distinct reviewers)`.
  Rank most-severe / highest-consensus first.
- **Anti-herd:** if _every_ reviewer agreed on a finding, you must attach at least one concrete
  counter-argument (why it might NOT be a defect) before accepting it — unanimity is a smell, not
  a proof.
- **Version guard:** drop any lens whose `source_fingerprint` differs from Step 0's — it reviewed
  a stale copy.

## Step 4 — emit the report

Output the ranked consensus: for each finding, its id, title, severity, consensus score, how many
reviewers raised it, the anchoring task+snippet, the concrete failure, and the one-line fix —
root causes grouped. Lead with `critical`/`high`. Do not soften. If the council found nothing at a
given severity, say so plainly rather than padding.

Then offer next steps: feed the criticals back into the plan, or (if the user wants) open the doc
and apply the fixes.

## Reference

- `references/reviewer-contract.md` — the verbatim reviewer prompt (Part A) + orchestrator/routing
  config (Part B). This is the source of truth for lens mandates, the severity/confidence rubrics,
  and the output schema. When in doubt, follow the contract over this summary.
