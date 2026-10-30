# Design and scoring

## 1. AI vs. rules

The reviewer selects a category and uploads PDFs. Code validates file type/size, scans files, assigns IDs and hashes, and extracts text with page references (OCR if needed). An admin supplies versioned criteria, required fields, mandatory conditions, weights and scoring parameters; none are embedded in application logic.

AI converts text into structured facts with exact evidence and locations, identifying missing/conflicting information and suspicious instructions. It cannot call tools or decide eligibility, scores or awards. Code validates its output; a reviewer confirms relevant evidence before deterministic eligibility checks and scoring. AI helps interpret varied wording; arithmetic and compliance checks belong in code.

## 2. Consistency and explainability

A run freezes the category version, evaluation date, file hashes, extraction result, reviewer corrections, prompt and model version. Reuse approved facts for repeat scoring. Fixed model settings reduce variation but do not guarantee identical fresh extraction. Identical approved inputs and rule versions produce identical scores. Store full precision, display two decimals, and break exact ties by proposal ID while showing the tie.

### Explicit sample assumptions

For this worked example only, assume an evaluation date of 1 January 2026
and a five-year window from 1 January 2021 up to that date. Assume the
summaries describe projects completed before this evaluation date.
“Since 2021” counts fall within the window; C’s “since 2020” aggregate
does not establish its qualifying count. This is an illustrative date
assumption, not a date supplied by MATACS. Production requires actual
project dates and credential verification before award.

Budget above CAD 260,000 or duration above 16 weeks is a hard failure. After confirming the document was extracted completely, an absent required
mandatory declaration fails the stated evidence requirement. Unreadable,
ambiguous or contradictory mandatory evidence is flagged for human review
rather than treated as a confirmed omission. Unclear budget or schedule feasibility is flagged, not assumed to pass. Failure takes precedence over flags. Missing weighted evidence earns zero for that component and is visibly marked for clarification; it does not alone block a provisional ranking. A security flag requires human clearance before the ranking is released.

### Proposed rubric for admin approval

These anchors are design assumptions, not additional employer requirements. Each component is 0–100; clamp formulas to that range.

| Criterion | Weight | Raw score |
|---|---:|---|
| Experience | 30% | 100 × qualifying project count / 15, capped at 100 |
| Price | 25% | 100 × (260000 − price) / 60000 |
| Timeline | 15% | 100 × (16 − weeks) / 8 |
| Warranty | 15% | 50 × min(panel years / 25, 1) + 50 × min(workmanship years / 12, 1) |
| Team/approach | 15% | 25 each for stated named lead, defined crew, safety plan, monitoring plan |

Price reaches full credit at CAD 200,000 and timeline at eight weeks; these fixed anchors avoid changing existing scores when another bidder enters.

Total = sum((weight / 100) × raw score). Reviewers see evidence, raw scores, weighted contributions, missing fields and reasons.

### Expected sample results

The table shows weighted contributions. Totals use unrounded values.

| Proposal | Experience | Price | Timeline | Warranty | Team | Total | Outcome |
|---|---:|---:|---:|---:|---:|---:|---|
| A | 28.00 | 9.17 | 7.50 | 13.75 | 15.00 | 73.42 | Rank 1 |
| E | 16.00 | 7.29 | 5.63 | 15.00 | 15.00 | 58.92 | Rank 2 |
| C | 0.00 | 6.67 | 3.75 | 13.75 | 7.50 | 31.67 | Flagged: held for security review (would rank 3rd) |
| B | — | — | — | — | — | — | Ineligible |
| D | — | — | — | — | — | — | Flagged |

B offers a subcontractor licence "to be confirmed after award" instead of a bidder-held licence, so it fails the stated requirement (this does not prove the bidder is unlicensed; a reviewer can confirm). D lacks a completion commitment, so feasibility cannot be confirmed. C contains an embedded instruction to rank it first; it is ignored and logged, and C is not released into the ranking until a reviewer clears it. The 31.67 is the provisional score if cleared. Its nine-project aggregate starts in 2020, before the window, so the qualifying count is unknown, not zero projects. Confirming all nine would raise C to 49.67, still third. A's experience, price and schedule outweigh E's stronger workmanship warranty under this rubric.

## 3. Human in the loop

Humans approve criteria before use, check extracted evidence, resolve flagged cases and confirm the final award. A reviewer can correct facts or record an override with identity, timestamp and reason. Preserve the original system score; display an override separately. Log file hashes, evidence locations, versions, original/corrected facts, flags, calculations, clarification history, overrides and final decisions.

## 4. Changing criteria

An edit creates a new immutable version. Existing runs remain reproducible and visibly show their original version. A reviewer explicitly requests a new run for all proposals using the updated criteria. Reuse approved facts where sufficient; extract/review newly required fields. Do not compare scores across versions. Show before/after results without overwriting history.

## 5. Risks and safeguards

- Extraction mistakes/hallucinations: require source evidence, schema/type checks, null for unknown facts, and human confirmation; reject unsupported values.
- Prompt injection: isolate proposal text as untrusted data, disable tools, ignore embedded commands, flag manipulation, and enforce eligibility/scoring outside AI.
- Bias: use a published rubric; exclude company identity and irrelevant attributes from scoring. Audit overrides and review whether chosen anchors unfairly disadvantage bidders.
- Cost/failure: cap file size/pages/tokens, cache by content and version, bound retries, and route failed OCR/model calls to manual review.
- Privacy/security: restrict access, encrypt stored files, sandbox parsing, define retention, keep secrets out of repositories.
