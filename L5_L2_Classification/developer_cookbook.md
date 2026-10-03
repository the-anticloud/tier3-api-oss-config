# Developer Cookbook — api-oss-config
**Stack:** Python 3.11, TOML, AES-256-GCM, SQLite, AIOSS_FORMAT
**Domain:** Sovereign configuration management: versioned, encrypted, AIOSS-audited config store
**License:** Apache-2.0 | **IP:** USPTO pending 2026, Anticloud FZ LLE

## Core Usage

```python
from api_oss_config import ConfigStore

config = ConfigStore("./anticloud_config.db", encryption_key=vault_key,
                     aioss_chain="./config.aioss")

# Read config
max_tokens = config.get("pax.max_tokens", default=512)
sample_rate = config.get("tier7.biosignals.sample_rate", default=256)

# Write config (AIOSS-audited)
config.set("pax.max_tokens", 1024, author="admin", reason="Clinical context requires longer responses")

# View change history
for change in config.history("pax.max_tokens"):
    print(f"{change.timestamp}: {change.old_value} -> {change.new_value} by {change.author}")

# Validate config via PAX
validation = config.validate_with_pax(pax_model="./pax-27b-q4.gguf")
print(f"Config valid: {validation.ok}, warnings: {validation.warnings}")
```

## AIOSS Chain Append

```python
import hashlib, time

def aioss_append(chain_path, payload: bytes, module_id: str):
    entry_hash = hashlib.sha3_256(payload).digest()
    ts = int(time.time_ns()).to_bytes(8, 'big')
    with open(chain_path, 'rb') as f:
        f.seek(-32, 2); prev_hash = f.read(32)
    new_hash = hashlib.sha3_256(prev_hash + entry_hash + ts).digest()
    with open(chain_path, 'ab') as f:
        f.write(ts + entry_hash + new_hash)
    return new_hash.hex()

# After every api-oss-config output:
chain_hash = aioss_append("./api_oss_config.aioss",
                           result_bytes, "api-oss-config")
```

## Performance & Integration

SQLite WAL for concurrent reads. Encrypt sensitive values (API keys, passwords) with per-key AES-256-GCM. Config read path is hot: cache in memory, invalidate on write. Integration: consumed by every TIER_3 api-oss-* project and all TIER_2 PAX_* modules. Writes are audited by AIOSS_FORMAT.
