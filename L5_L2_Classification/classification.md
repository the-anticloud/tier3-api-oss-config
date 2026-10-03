# L5 Narrow / L2 General Classification — api-oss-config
**Platform:** Anticloud | **PAX:** 27B | **IP:** USPTO pending 2026, Anticloud FZ LLE
**Domain:** Sovereign configuration management: versioned, encrypted, AIOSS-audited config store

## L5 Narrow
api-oss-config manages all Anticloud deployment configuration: PAX model parameters, AIOSS chain paths, API rate limits, module enable/disable flags. Every config change is versioned and AIOSS-chained — no undocumented configuration drift.

## L2 General
L2 General: one configuration store for all 9 tiers. A config key like `pax.max_tokens` applies globally; tier-specific overrides use namespacing: `tier7.biosignals.sample_rate`.

## PAX Integration
PAX 27B is invoked for configuration validation: given a proposed config change, PAX checks it against known-good configurations and flags potentially dangerous changes (e.g., disabling AIOSS chain audit).

## AIOSS Audit Relevance
Every config change event (key + old_value_hash + new_value_hash + author + validation result) is AIOSS-chained: H_n = SHA3-256(H_{n-1} || entry_hash_n || timestamp_n).
Tamper-evident, offline-verifiable, zero cloud dependency.

## Regulatory / Compliance
NIST SP 800-53 CM-3 (configuration change control), ISO 27001 A.12.1.2
