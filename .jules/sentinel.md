# Sentinel Security Journal

This journal is used by Sentinel to log critical security learnings, vulnerability patterns specific to this codebase, and reusable security patterns.

---

## 2026-08-02 - Mutable GitHub Action Tags in Configuration Guides
**Vulnerability:** Utilizing mutable tags (like `@v4`) in CI/CD pipeline examples or templates makes users vulnerable to supply chain attacks if malicious actors compromise or overwrite the action tags.
**Learning:** Users often copy-paste CI/CD templates directly without considering pipeline security. If guide repositories suggest insecure patterns, they inadvertently propagate those vulnerabilities to end-user applications.
**Prevention:** Always pin GitHub Actions to full-length immutable commit SHAs in guide templates and tutorials, and explicitly document this practice under a dedicated security section.
