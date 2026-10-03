# Deploy Guide — api-oss-config
**Platform:** Anticloud sovereign infrastructure | Air-gap capable
**Stack:** Python 3.11, TOML, AES-256-GCM, SQLite, AIOSS_FORMAT

## Prerequisites
Python 3.11+, tomli 2.0+, cryptography 42.0+, SQLite (stdlib)

## AIOSS Integration
```bash
aioss init --module api-oss-config --output ./api_oss_config.aioss
aioss append --chain ./api_oss_config.aioss --payload ./output.bin --module api-oss-config
aioss verify --chain ./api_oss_config.aioss
```

## Air-Gap Deployment
```bash
# On networked machine:
pip download -r requirements.txt -d ./wheels/
# Transfer wheels/ to air-gap host, then:
pip install --no-index --find-links ./wheels/ -r requirements.txt
```

## PAX 27B Harness Wiring
```python
from anticloud_pax import PAXHarness
harness = PAXHarness(
    model_path="./pax-27b-q4.gguf",
    module="api-oss-config",
    aioss_chain="./api_oss_config.aioss",
    classification="L5_NARROW_L2_GENERAL"
)
result = harness.process(input_data)
```

## Verification
```bash
aioss verify --chain ./api_oss_config.aioss --verbose
python -m api_oss_config.tests.smoke
```
