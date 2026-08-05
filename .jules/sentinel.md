# Sentinel Security Journal

This journal is used by Sentinel to log critical security learnings, vulnerability patterns specific to this codebase, and reusable security patterns.

---

## 2025-08-05 - Securing Guide Templates & Local Repositories
**Vulnerability:** Guide templates in README.md recommended floating tags for GitHub Actions (such as `actions/checkout@v4`). This pattern exposes downstream copied workflows to supply chain attacks if the action repository/tag is compromised or maliciously repointed. In addition, the lack of a default `.gitignore` in a template-like/showcase repo increases the risk of users accidentally committing sensitive credentials (e.g., API keys, Keystore passwords, or debug keystores).
**Learning:** In public guide repositories, users tend to copy-paste configurations verbatim. Providing secure-by-default templates (including pinned SHAs) and a default robust `.gitignore` acts as a crucial defense-in-depth mechanism.
**Prevention:** Always pin third-party GitHub Actions to 40-character immutable commit SHAs in guide templates, and provide a comprehensive `.gitignore` template to safeguard secrets from accidental commits.
