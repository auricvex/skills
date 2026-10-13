---
name: "redteam-impl"
description: "Red-team audit a software implementation against its spec: conformance checking plus security vulnerabilities, bugs, inefficiency, and undefined behavior, via multi-agent fan-out with double-verified findings. Use when asked to audit code against a spec, or to security/correctness review an implementation."
---

# Red-Team Implementation Audit

## Purpose
Two jobs: (1) conformance — verify the implementation does exactly what the spec says; every deviation is a finding. (2) adversarial — attack it directly for security vulnerabilities, bugs, inefficiency, and undefined behavior, even where the spec is silent. Never rewrite the implementation; report, the human decides.

Inputs: implementation directory, docs/spec directory, and any additional focus request (which narrows focus but never expands the boundary below). Take them from the triggering request when present; ask for what's missing (see Operating Rules).

## Workflow

### 1. Fan out (multi-agent mandatory when available)
- Spawn one red-team agent per subsystem or module (or per attack angle: conformance, security, correctness, performance, undefined behavior).
- Each agent works independently — no sharing notes until consolidation.
- Each agent must: read its code and the governing spec sections; map every normative spec claim ("must", "shall", "is", "cannot") to its implementing code and verify behavior including error paths and edge cases; think like an attacker — trace untrusted inputs end to end (injection, auth bypass, privilege escalation, data leaks, denial of service, weak crypto, unsafe defaults); hunt bugs (logic errors, off-by-ones, races, error-handling gaps, resource leaks) with breaking inputs; flag inefficiency only with a complexity argument or measurement — "slow" without either is not a finding; reason like a sanitizer for UB (memory unsafety, data races, integer-overflow-as-logic, platform-undefined dependencies).
- Build the implementation and run its existing test suite first. Where the toolchain allows, build with sanitizers (address, undefined behavior, thread) and throw adversarial inputs at the result.
- A coordinator agent consolidates: dedupes, clusters, ranks by severity.
- If multi-agent is unavailable: run at least two independent passes with different angles; pass-1 findings must survive pass 2.

### 2. Classify conformance deviations
- Impl violates spec: code does what the spec forbids or omits what it requires — always a finding; the spec is the contract.
- Spec silent, impl guesses: report only if dangerous, wrong, or load-bearing for interoperability; otherwise one parked line.
- Spec unimplementable as written: do NOT "fix" the spec — separate "spec-side findings" list, one line each. (If the spec itself needs a full audit, suggest the redteam-spec skill.)

### 3. Verify every finding twice
Real only if: exact file/line (or function) cited, plus the violated spec section or the exact attack; reproduced by a second independent check (different agent, or a concrete PoC input, test case, or trace) — a security finding without a working PoC is not a finding; and it survives the "so what?" test (changes behavior, leaks data, wastes real resources, breaks the contract). Discard anything unreproducible silently. Severity (strict): Critical = exploitable vulnerability, spec violation causing wrong behavior, or UB reachable in practice; Major = real bug, meaningful deviation, or inefficiency with concrete impact; Minor = small bugs, weak error messages, missing edge-case handling.

### 4. Report
Per finding: ID, severity, one-line title, exact location, spec section with quoted words (conformance findings), why it is real (concrete), minimal reproduction (input, test case, or PoC), fix direction (not rewritten code). Then: coverage list, verification notes, discarded count, spec-side findings (one line each).

## Output Contract
1. Consolidated report: findings ranked critical → major → minor, with verification notes, coverage list, parking lot, out-of-scope list, discarded count, spec-side findings.
2. One-paragraph verdict: STABLE or NEEDS FIXES (with counts by severity).
3. If not the first audit round: explicit convergence statement ("Round N found 0 new critical/major → implementation declared STABLE" / "Round N found K new major → one more round authorized").

## Operating Rules
1. Input discipline: the required inputs are the implementation directory and the docs directory. Take them from the triggering request when present; for anything missing, ask the user with the available question/options tools — one question at a time. You may ask one optional question about additional focus areas; if the user declines or says none, proceed without it. Never guess paths, invent defaults, or start with missing inputs.
2. Scope box: only the implementation and docs directories. Out-of-scope → parked list (max 5 items, one line each).
3. No redesign: never propose architecture changes, new features, or refactors beyond what the findings require. Bigger ideas → parking lot, unelaborated.
4. Convergence: first round reports everything verified; later rounds report only new critical/major findings. Zero new critical/major = STABLE — declare it and stop scheduling audits. Never run another round merely because "there might be more".
5. Fix budget: report, don't fix in the same session unless explicitly authorized. If authorized: critical first, then major, then stop. Minors batch for a later polish pass.
6. Decision freeze: the human's prior decisions (in the spec or otherwise) are frozen — never re-litigate. Conflicts are reported as "conflicts with a frozen prior decision" and left for the human.
7. Time-box: agree a clock limit and/or max verification passes before starting; when hit, report coverage honestly instead of expanding.
