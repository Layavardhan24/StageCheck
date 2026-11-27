# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [2.0.0] - 2026-11-27

### Added
- **Dual-Agent Verification Model**: Enforced strict separation of duties between Agent 1 (Prompt Architect & Auditor) and Agent 2 (Code Implementer), preventing prompt drift, silent spec tampering, and unreviewed code commits.
- **5-Gate Deterministic State Machine**:
  - `Gate-1` (Planning & Spec Freeze): Canonical `TASKPLAN.md` specification frozen under POSIX permissions (`0555` directory, `0444` files) with canonical `gate1_receipt.json`.
  - `Gate-2A` (Cooperative Scope & Spec Review): Blast-radius inspection against `SCOPE_BUDGET.md` with structured `cooperative_review.json` generation.
  - `Gate-2B` (Local Commit Verification): Protected branch enforcement blocking direct commits to `main`/`master` and verifying isolated task branch state.
  - `Gate-3` (Independent Clean-Room Audit): Detached temporary Git worktree verification running the 9-stage audit pipeline with Bubblewrap sandboxing fallback.
  - `Gate-4` (Release Sealing): Cryptographic task sealing producing tamper-evident `SEALED_PASSED` receipts appended to `evidence/EVIDENCE_MANIFEST.json`.
- **Independent 9-Stage Clean-Room Audit Pipeline**:
  - Stage 1: Preflight & environment check (`python3`, `git`, `gitleaks`).
  - Stage 2: Scope budget & file count/line addition thresholds.
  - Stage 3: Citations & plan anchor verification.
  - Stage 4: Report linting & schema compliance against rubric targets.
  - Stage 5: Authority verification against `OWNER_GRANTS.md`.
  - Stage 6: High-entropy secret scanning via integrated Gitleaks.
  - Stage 7: Test qualification & test naming standard (`test_*.py`).
  - Stage 8: Clean-room test suite rerun in detached worktree.
  - Stage 9: Baseline carry-forward regression audit.
- **270-Rule Rubric Catalog**: Automated compliance evaluation covering architecture invariants, security posture, and documentation standards.
- **Bounded Circuit Breaker & Human Disposition**: Automatically latches to `CIRCUIT_BREAKER_TRIPPED` after 3 failed repair attempts, escalating to authorized human owners with structured actions (`retry`, `waive`, `abort`).
- **Human Waiver Provenance & Anti-Replay Ledger**: Cryptographic SHA-256 evidence hashing, explicit expiration timestamps, and anti-replay ledger tracking.
- **Write-Ahead Transaction Journal (`.stagecheck_journal/`)**: Multi-file write-ahead rollback journal guaranteeing atomic, crash-safe state recovery across interrupted gate transitions.
- **Non-Root / Sudo-Free Runtime**: Full compatibility with unprivileged user-space execution environments.

### Security
- **Wave 1 through 18 Hardening (SEC-01 to SEC-108)**:
  - Resolved symlink directory escape vulnerabilities across all control descriptors, review artifacts, and evidence logs.
  - Hardened archive extraction against Zip-Slip vulnerabilities with canonical path sanitization.
  - Eliminated supervisor spoofing and unauthorized bypass via strict owner authority resolution.
  - Implemented atomic gate advancement and file-locking primitives preventing race conditions and gate truncation.
  - Enforced POSIX permission invariants (`0555`/`0444`) across active and historical phase checkpoints.

### Changed
- **Package Architecture**: Decoupled package directory from monolithic `stagecheck` to modular `governance` core package.
- **Single Owner Authority**: Updated `OWNER_GRANTS.md` generation to resolve a single, explicit authorized user identity.
- **Settings Standardization**: Transitioned vendor-specific `claude.settings.json` to universal `agent.settings.json`.

---

## [1.0.0] - 2026-11-05

### Added
- Initial StageCheck delivery monitoring and stage tracking harness.
- Baseline Sentinel daemon with inotify/epoll filesystem monitoring.
- Initial CLI commands for workspace initialization, review verification, and stage auditing.
- Basic test suite and packaging workflow.
