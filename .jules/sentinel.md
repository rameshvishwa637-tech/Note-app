# Sentinel Security Journal

This journal is used by Sentinel to log critical security learnings, vulnerability patterns specific to this codebase, and reusable security patterns.

---

## 2026-08-06 - Preventing Secret Leakage and Action Hijacking in AI-Generated Mobile Projects
**Vulnerability:** Potential leakage of local credentials/secrets (like local.properties or signing keystores) and vulnerability to GitHub Actions third-party supply chain attacks via mutable tag names.
**Learning:** AI-generated applications often export template repositories directly. Developers frequently run these configurations without realizing that mutable tags like `@v4` can be tampered with or that local dev credentials can accidentally get committed if a `.gitignore` is missing.
**Prevention:** Always provide a robust, pre-configured root `.gitignore` to prevent credential leakage. Additionally, secure all workflow templates by default by pinning them to immutable 40-character commit SHAs.
