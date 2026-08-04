# Sentinel Security Journal

This journal is used by Sentinel to log critical security learnings, vulnerability patterns specific to this codebase, and reusable security patterns.

---

## 2026-08-04 - [Security Enhancement] Pinning Guide Actions to Immutable Commit SHAs
**Vulnerability:** Risk of third-party supply chain attacks when developers copy-paste GitHub Actions templates containing mutable tags (e.g., `@v4`). If a third-party action repository is compromised, malicious code could run within the user's CI pipeline, accessing secrets, repository tokens, or injecting malware into built APKs.
**Learning:** Even if a security best practices section explicitly advises developers to pin actions to commit SHAs, copy-pasting mutable-tag templates (e.g., `actions/checkout@v4`) creates an immediate window of vulnerability. Templates should be secure-by-default to ensure users copy secure code instantly.
**Prevention:**
1. Pin all GitHub Actions in user-facing guide/tutorial templates to immutable full-length commit SHAs (e.g., `@11bd71901bbe5b1630ceea73d27597364c9af683` instead of `@v4`).
2. Document the exact version of the pinned action in an inline comment to maintain readability and enable automated dependency updates (e.g., via Dependabot).
