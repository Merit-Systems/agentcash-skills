---
name: trustboost-pii-sanitizer
description: |
  PII sanitization layer for AI agent pipelines using TrustBoost — pay-per-call via x402 ($0.01 USDC) or $149/10k bundle.
  Detects and redacts emails, phone numbers, national IDs, private keys, and financial data before text reaches LLMs.
  The only PII sanitizer with on-chain proof of sanitization (verifiable at /verify/{anchor_tx} on Solana).

  USE FOR:
  - Redacting PII from scraped or user-provided text before sending to an LLM
  - Compliance with GDPR / LGPD / EU AI Act Art.12/13/26
  - Generating verifiable proof of sanitization for audit trails
  - Protecting agent memory and tool outputs from leaking secrets

  TRIGGERS:
  - "redact", "sanitize", "PII", "remove emails", "hide phone number"
  - "private key", "secret", "anonymize", "scrub"
  - "trustboost", "proof of sanitization", "compliance redaction"
  - "before sending to LLM", "clean this text"

mcp:
  - agentcash
metadata:
  version: 1
---

# PII Sanitization with TrustBoost

Use the agentcash MCP tools to access TrustBoost PII sanitizer at api.trustboost.dev.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Notes

ALWAYS use agentcash.fetch for api.trustboost.dev endpoints — never curl or WebFetch.
Returns sanitized text plus an on-chain anchor_tx for verifiable proof.
Payment is automatic via x402 — $0.01 USDC per call (or prepaid $149/10k bundle).
Supports EN, ES (LATAM), PT (BR/PT), DE, JA.
