# Configuration Directory

YAML configuration files for the Prompt Optimizer.

## Overview

All configuration uses YAML format for readability and easy modification.

## Configuration Files

| File | Purpose |
|------|---------|
| `settings.yaml` | Core settings: API, models, voting, export |
| `voter_presets.yaml` | System prompts for voter agents |
| `pedagogy_templates.yaml` | Teaching framework templates |
| `validation_settings.yaml` | Phase 15 validation rules |
| `near_peer_matrix.yaml` | Confusion matrices for distractors |
| `misconceptions_cyber.yaml` | Domain-specific misconceptions |
| `output_schema.yaml` | Export schema definitions |

## settings.yaml

Core configuration for the entire system:

```yaml
api:
  openrouter:
    base_url: "https://openrouter.ai/api/v1"
    timeout: 120
  exa:
    base_url: "https://api.exa.ai"
    max_results: 5

models:
  voting:
    model: "anthropic/claude-sonnet-4"
    max_tokens: 2000
  synthesis:
    model: "x-ai/grok-4-1"
    max_tokens: 4000
  flashcards:
    model: "google/gemini-2.5-flash-lite"
    max_tokens: 30000

voting:
  consensus_threshold: 0.57  # 5 of 8 voters
  min_confidence: 0.6
```

## validation_settings.yaml

Phase 15 validation rules and weights:

```yaml
validation:
  pass_threshold: 0.80

  weights:
    prohibited_phrases: 1.0
    distractor_diversity: 1.0
    parenthetical_giveaways: 1.0  # AUTOMATIC FAIL
    self_contained: 0.5

  blocked_patterns:
    - "none of the above"
    - "all of the above"

domains:
  cybersecurity:
    prohibited_patterns:
      - "according to (?:NIST|OWASP|ISO)"
```

## near_peer_matrix.yaml

Confusion matrices for near-peer distractors:

```yaml
confusion_matrices:
  cryptography:
    - [Encryption, Hashing, Encoding, Obfuscation]
    - [AES, DES, 3DES, Blowfish]

  authentication:
    - [Authentication, Authorization, Accounting]
    - [OAuth, OIDC, SAML, Kerberos]
```

## voter_presets.yaml

System prompts for each voter:

```yaml
voters:
  pedagogy:
    system_prompt: |
      You are an expert in educational pedagogy...
    evaluation_criteria:
      - cognitive_level_alignment
      - learning_objective_clarity
```

## Environment Variables

Required variables (set in `.env` or environment):

| Variable | Required | Description |
|----------|----------|-------------|
| `OPENROUTER_API_KEY` | Yes | OpenRouter API key |
| `EXA_API_KEY` | No | Exa API key |

## Modifying Configuration

1. Edit YAML files directly
2. Restart the application
3. Changes take effect immediately

## See Also

- [Configuration Reference](../docs/configuration.md)
- [CLI Reference](../docs/cli-reference.md)
