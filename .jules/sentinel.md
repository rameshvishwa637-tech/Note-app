# Sentinel Security Journal

This journal is used by Sentinel to log critical security learnings, vulnerability patterns specific to this codebase, and reusable security patterns.

---

## 2026-08-03 - [Security Enhancement] Adding Gitignore & Security Best Practices to Prevent Key Exposure
**Vulnerability:** Risk of accidental API key leaks (such as Gemini/AI Studio keys), keystore theft, and GitHub Action third-party supply chain attacks when users use this repository template/guide to build APKs.
**Learning:** AI-generated applications often hardcode API keys or store them in `.env` files. Without a predefined `.gitignore` template at the root, users cloning or pushing their generated projects are highly likely to accidentally commit secrets publicly. Additionally, mobile builds are susceptible to insecure Gradle setups and dependencies.
**Prevention:**
1. Maintain a robust `.gitignore` file that proactively filters out common Node, Web, Android, Gradle, and secret/keystore patterns.
2. Embed clear, action-oriented "Security, Secrets Management & Best Practices" documentation in the README.md to educate builders on securing keys, pinning actions to immutable commit SHAs, and handling keystores.
