# StageCheck v2.0.0 — Production Release Notes

**Enterprise AI Software Delivery Governance, Dual-Agent Verification & Multi-Repository Audit Harness**

- **Release Version**: `v2.0.0`
- **Release Date**: November 27, 2026
- **Target Commit**: `a3e97e3`
- **Release Status**: Official Production Release (`GA`)

---

## 1. Executive Summary

StageCheck v2.0.0 represents a major milestone in AI software engineering governance. As generative AI coding agents increasingly drive software development, traditional post-hoc CI/CD pipelines fall short of preventing subtle architectural drift, unauthorized scope expansion, prompt manipulation, or credential leaks.

StageCheck v2.0.0 introduces an end-to-end, deterministic, fail-closed delivery governance harness. By combining a strict **Dual-Agent Verification Model**, a **5-Gate State Machine**, an **Independent 9-Stage Clean-Room Audit Runner**, and an automated **3-Attempt Circuit Breaker**, StageCheck guarantees that AI-generated code is reproducible, secure, and compliant before reaching production.

All 7 production release gates and 528 test cases have executed and passed with zero regressions.

---

## 2. Release Highlights

- **Dual-Agent Separation of Duties**: Autonomous coding is partitioned into distinct roles: Agent 1 (Prompt Architect & Auditor) and Agent 2 (Code Implementer). Neither agent can approve its own deliverables or silently mutate task plans.
- **5-Gate Cryptographic State Machine**: Strict lifecycle progression through Gate-1 (Planning Freeze), Gate-2A (Scope & Blast Radius Review), Gate-2B (Local Commit Verification), Gate-3 (Independent Clean-Room Audit), and Gate-4 (Release Sealing).
- **Independent 9-Stage Clean-Room Audit**: Verifies deliverables in isolated, temporary Git worktrees with Gitleaks secret scanning, Bubblewrap sandbox execution, and carry-forward regression testing.
- **270-Rule Rubric Engine**: Automated compliance scoring evaluating architectural invariants, documentation schemas, and code quality.
- **Fail-Closed Circuit Breaker**: Bounded 3-attempt repair budget preventing infinite repair loops and token burn, escalating directly to human owners via cryptographically verified dispositions (`retry`, `waive`, `abort`).
- **Crash Safety & Atomic Journaling**: Write-Ahead Transaction Journal (`.stagecheck_journal/`) providing zero-loss rollback of partial mutations across multi-file operations.
- **108 Security Vulnerabilities Remediated**: Exhaustive defense-in-depth across 18 security waves resolving symlink directory escapes, zip-slip, supervisor spoofing, and gate race conditions.

---

## 3. Detailed Feature Breakdown

### 3.1 Dual-Agent Governance & Role Separation
- **Separation of Duties Protocol**: Agent 1 plans requirements, defines acceptance criteria, and verifies outcomes. Agent 2 is restricted to code and test implementation on non-protected branches.
- **Single-Agent Compatibility**: Single-agent workflows execute the identical protocol across isolated sequential phases, maintaining identical audit gates.
- **Cryptographic Signer Validation**: Validates all gate transitions and dispositions against authorized identities in `OWNER_GRANTS.md`.

### 3.2 5-Gate Deterministic Lifecycle Progression
- **Gate-1 (Planning & Spec Freeze)**: Freezes `TASKPLAN.md` under POSIX permissions (`0555` directory, `0444` files) and generates canonical `gate1_receipt.json`.
- **Gate-2A (Cooperative Scope Review)**: Inspects planned modifications against `SCOPE_BUDGET.md` whitelists and line limits, producing `cooperative_review.json`.
- **Gate-2B (Local Commit Verification)**: Enforces non-protected task branch isolation and blocks direct commits to `main` or `master`.
- **Gate-3 (Independent Clean-Room Audit)**: Executes the 9-stage audit pipeline in detached temporary Git worktrees, producing `audit_report.json` and `gate3_receipt.json`.
- **Gate-4 (Cryptographic Release Sealing)**: Validates complete chain of custody across all prior receipts, updates `evidence/EVIDENCE_MANIFEST.json`, and seals task as `SEALED_PASSED`.

### 3.3 Independent 9-Stage Clean-Room Audit Runner
1. **Stage 1 (Preflight & Environment)**: Validates runtime binaries (`python3 >= 3.10`, `git >= 2.25`, `gitleaks >= 8.18`).
2. **Stage 2 (Scope & Budget)**: Validates modified file count and line thresholds against `SCOPE_BUDGET.md`.
3. **Stage 3 (Citations & Anchors)**: Verifies task-plan markdown cross-references and citation integrity.
4. **Stage 4 (Report Linting)**: Validates structured JSON and Markdown reports against rubric targets.
5. **Stage 5 (Authority Verification)**: Confirms signatures match authorized owner entities in `OWNER_GRANTS.md`.
6. **Stage 6 (Secret & Credential Scanning)**: Executes Gitleaks across all commit deltas to detect leaked credentials and keys.
7. **Stage 7 (Test Qualification & Naming)**: Enforces test naming standards (`test_*.py`) and test suite qualification rules.
8. **Stage 8 (Clean-Room Test Execution)**: Runs test suites in isolated temporary worktrees with Bubblewrap container fallback.
9. **Stage 9 (Carry-Forward Regression)**: Verifies that baseline product capabilities remain unregressed.

### 3.4 Circuit Breaker & Human Disposition Recovery
- **Attempt Budget**: Enforces a strict 3-attempt limit per task.
- **Automatic Latching**: Transitions task state to `CIRCUIT_BREAKER_TRIPPED` upon 3 consecutive audit failures.
- **Owner Disposition**: Authorized owners can inspect failures via `stagecheck report` and issue formal dispositions:
  - `retry`: Resets attempt counter and provides 3 additional attempts.
  - `waive`: Grants formal, cryptographically hashed waivers for specific non-critical rubric rules.
  - `abort`: Cancels task and records permanent audit rationale.
- **Anti-Replay Ledger**: Prevents re-use of expired, tampered, or mismatched waivers.

### 3.5 Write-Ahead Transaction Journaling (WAL)
- File writes and gate mutations are recorded to `.stagecheck_journal/` prior to disk commitment.
- Interrupted or crashed operations are automatically detected and rolled back on subsequent command invocation.
- POSIX file locking (`fcntl`) and SQLite WAL mode eliminate concurrency collisions.

---

## 4. Security Hardening & Vulnerability Remediations

This release resolves 108 security vulnerabilities identified across 18 comprehensive audit waves:
- **Symlink Escape Defenses**: Rejection of symlinks pointing outside workspace roots across all 8 control descriptors, report directories, and evidence trees.
- **Zip-Slip Elimination**: Strict path canonicalization during source archive creation and extraction.
- **Supervisor & Agent Authority Protection**: Prevention of supervisor spoofing and unauthorized bypass via strict `OWNER_GRANTS.md` resolution.
- **Atomic Gate Sealing**: Race-condition-free gate advancement with file-locking primitives and append-only evidence manifests.
- **POSIX Invariants**: Automatic enforcement of read-only permissions (`0555`/`0444`) across active and historical phase checkpoints.

---

## 5. Verification & Quality Gate Results

The StageCheck release engineering suite executed all 7 production release gates:

| Release Gate | Verification Scope | SLA / Threshold | Actual Result | Status |
| :--- | :--- | :--- | :--- | :--- |
| **Gate 1** | Unit & Integration Test Suite | 100% Pass Rate | 528 passed, 194 subtests passed | **PASS** |
| **Gate 2** | Sentinel Runtime Benchmark | RSS <= 25MB, CPU < 1%, Latency < 50ms | 21.01 MiB RSS, 0.00% CPU, 1.14ms Latency | **PASS** |
| **Gate 3** | PEP 517 Wheel Build & Packaging | Build wheel & preserve executable bits | `stagecheck-2.0.0-py3-none-any.whl` generated | **PASS** |
| **Gate 4** | Isolated Virtual Environment | Fresh venv outside repository | Clean venv creation verified | **PASS** |
| **Gate 5** | Wheel Installation | Clean pip install in fresh venv | Installed with 0 dependency errors | **PASS** |
| **Gate 6** | CLI Verbs & Entry Points | Validate all CLI verbs and help output | All 14 CLI verbs verified | **PASS** |
| **Gate 7** | Production Lifecycle Smoke Test | Daemon startup, monitoring, clean shutdown | Zero zombie/leaked processes | **PASS** |

---

## 6. Compatibility & System Requirements

- **Operating System**: Modern Linux (kernel 5.x / 6.x / 7.x) with POSIX filesystem permission support (`0555`/`0444`) and `inotify`/`epoll`.
- **Python**: 3.10, 3.11, or 3.12 (CPython).
- **Git**: 2.25 or newer (required for detached worktree isolation).
- **Gitleaks**: 8.18.2 or newer (required for Stage 6 secret scanning).
- **Bubblewrap** *(Optional)*: 0.4.0 or newer for unprivileged container isolation in Stage 8.
- **Privilege Requirements**: 100% user-space; **no root or sudo privileges required**.

---

## 7. Migration Guide (from v1.x)

StageCheck v2.0.0 introduces structural improvements over v1.x:
1. **Module Import Paths**: Internal core modules have moved from `stagecheck.*` to `governance.*`. CLI commands (`stagecheck <verb>`) remain backwards-compatible.
2. **Control Descriptors**: Workspaces must include all 8 mandatory control descriptors (`OWNER_GRANTS.md`, `PHASE_BOUNDARIES.md`, `SECURITY_POLICY.md`, `AGENT_ROLES.md`, `EVIDENCE_SPEC.md`, `RUBRIC_CATALOG.md`, `SCOPE_BUDGET.md`, `RECOVERY_PROTOCOL.md`). Run `stagecheck init` to generate or upgrade them.
3. **Owner Resolution**: `OWNER_GRANTS.md` now strictly validates against explicit email addresses matching the host developer environment.
