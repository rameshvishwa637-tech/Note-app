# Sentinel Security Journal

This journal is used by Sentinel to log critical security learnings, vulnerability patterns specific to this codebase, and reusable security patterns.

---

## 2026-08-11 - Supply Chain Security in Workflows and Secret Leakage Prevention
**Vulnerability:** Workflow templates in tutorials and guides that reference mutable GitHub Actions version tags (e.g. `@v4`) expose downstream projects to supply chain vulnerabilities. Additionally, the lack of a pre-configured `.gitignore` increases the risk of users accidentally committing highly sensitive Gemini API keys and signing keys (`*.keystore`, `*.jks`) during development.
**Learning:** Guides for AI-generated applications often focus on ease-of-use and speed, occasionally omitting robust `.gitignore` protections and using simplified `@v4` workflow steps. If a user clones or duplicates the codebase, they inherit these omissions, leading to potential credentials exposure and insecure CI/CD templates.
**Prevention:** Always pin GitHub Action workflow templates to immutable 40-character commit SHAs in documentation, and supply a robust, secure-by-default `.gitignore` tailored specifically for both Native Android and Web App dependencies, build outputs, and local properties to prevent credential leaks.
