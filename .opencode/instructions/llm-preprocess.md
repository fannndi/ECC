# LLM Preprocess — Universal Input Enrichment

You MUST preprocess EVERY user input through local Ollama before responding.

## Pipeline

```
User Input
   │
   ▼
Invoke-LLMEnrich()
   ├─ ECO:        pass-through (no LLM)
   ├─ BALANCED:   enrich ~250 tokens
   └─ PERFORMANCE: enrich ~512 tokens + intent compile + skill route
   │
   ▼
Enriched Context (internal, not shown)
   │
   ▼
AI Response + Footer
```

## Execution

Run this PowerShell on every user input:

```powershell
$mode = (Get-Content ".opencode/llm-mode.json" -Raw | ConvertFrom-Json).mode
if ($mode -ne "eco") {
  . .\scripts\llm-adapter.ps1
  $enriched = Invoke-LLMEnrich -Text "<USER_INPUT>" -Context "universal preprocess"
  # Use $enriched as context for response
}
```

## Rules

1. ALWAYS call Invoke-LLMEnrich before ANY response logic
2. ECO mode: skip entirely, use raw input
3. BALANCED: 250 token enrichment
4. PERFORMANCE: 512 token enrichment
5. Enriched context is internal — user sees normal response
6. Append LLM Status Footer after EVERY response
