---
name: "redteam-spec"
description: "Red-team audit a technical specification (protocol, architecture, design spec): find contradictions, gaps, and ambiguities via multi-agent fan-out, double-verified findings, and hard stopping rules against scope creep. Use when asked to audit, review, or red-team a spec."
---

# Red-Team Spec Audit

## Purpose
Attack a technical specification to find every real issue — contradictions, gaps, ambiguities, unenforceable claims, missing definitions — and prove each one is real. A false positive is a failure, not a finding. Never edit the spec; report, the human decides.

Inputs: the spec documents or directories under audit — take from the triggering request when present; ask for what's missing (see Operating Rules).

## Workflow

### 1. Fan out (multi-agent mandatory when available)
- Spawn one red-team agent per spec section or subsystem (or per attack angle: consistency, completeness, adversarial, security, implementability).
- Each agent works independently — no sharing notes until consolidation.
- Each agent must: read every line of its section; cross-check every normative claim ("must", "shall", "is", "cannot") against every other section; construct adversarial cases (inputs, sequences, configurations that break the spec's claims); apply the implementer-guess test — could two competent implementers build materially different things from this text? If yes, cite the exact words permitting both readings.
- A coordinator agent consolidates: dedupes, clusters, ranks by severity.
- If multi-agent is unavailable: run at least two independent passes with different attack angles; a finding from pass 1 must survive pass 2.

### 2. Verify every finding twice
A finding is real only if: it quotes the exact contradictory or deficient spec words; it is reproduced by a second independent check (another agent, or tracing a concrete example through the spec text); and it survives the "so what?" test (changes what an implementer builds, or what the spec permits/forbids). Discard anything unreproducible silently — never as "possible". Severity (strict): Critical = spec permits something unsafe/wrong or contradicts itself on normative behavior; Major = gap blocking implementation or ambiguity with materially different readings; Minor = imprecision, weak wording, missing example.

### 3. Report
Per finding: ID, severity, one-line title, spec sections with exact quoted words, why it is real (concrete), a minimal worked example that breaks it, and a fix direction (not a rewritten section). Then: coverage list (what was checked and found clean), verification notes (how each finding was verified twice), discarded count, and an out-of-scope parked list (max 5 items, one line each).

## Output Contract
1. Consolidated report: findings ranked critical → major → minor, with verification notes, coverage list, parking lot, out-of-scope list, discarded count.
2. One-paragraph verdict: STABLE or NEEDS FIXES (with counts by severity).
3. If not the first audit round: explicit convergence statement ("Round N found 0 new critical/major → spec declared STABLE" / "Round N found K new major → one more round authorized").

## Operating Rules
1. Input discipline: the required input is the spec documents under audit. Take it from the triggering request when present; if missing, ask the user with the available question/options tools — one question at a time. Never guess paths, invent defaults, or start the audit with missing inputs.
2. Scope box: only the documents under audit. Out-of-scope → parked list, no elaboration.
3. No spec growth: never propose new features, capabilities, or sections. New ideas → parking lot, unelaborated. The spec must not grow because it was audited.
4. Convergence: first round reports everything verified; later rounds report only new critical/major findings. A round with zero new critical/major = STABLE — declare it and stop scheduling audits. Never run another round merely because "there might be more".
5. Fix budget: report, don't fix in the same session unless explicitly authorized. If authorized: critical first, then major, then stop. Minors batch for a later polish pass.
6. Decision freeze: the human's prior decisions are frozen — never re-litigate. A finding conflicting with one is reported as "conflicts with a frozen prior decision" and left for the human.
7. Time-box: agree a clock limit and/or max verification passes before starting; when hit, report coverage honestly instead of expanding.
