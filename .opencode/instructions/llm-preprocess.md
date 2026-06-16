# LLM Preprocess — Universal Input Enrichment

You MUST preprocess EVERY user input through local Ollama before responding.

## WAJIB — Step-by-step tiap user input

```
1. BACA .opencode/llm-mode.json  →  tau mode (eco/balanced/performance)
2. KALO mode != eco:
   a. JALANKAN Invoke-LLMEnrich("<user_input>", "universal preprocess")
   b. KALO enriched != input → $enrichSuccess = $true
   c. SIMPAN enriched context (internal, user ga lihat)
3. KALO mode == eco:
   a. $enrichSuccess = $false
   b. PAKAI raw input langsung (no LLM)
4. JAWAB user pake enriched/raw context
5. TULIS .opencode/llm-status.json  (enrich On/Off, token count, mode, profile, model)
6. APPEND footer: LLM : [ MODE ] - LLMEnrich [ On/Off ] - Tokens : [ X ] - Profile : [ Y ] - Model : [ Z ]
```

INI WAJIB. SETIAP USER INPUT. TANPA EXCEPTION.
LLMEnrich [Off] di footer = AI GAGAL COMPLY. User akan lihat.

## Architecture

Local Ollama = preprocessor (enrichment only).
Cloud AI (opencode model) = actual responder.

User never sees local LLM output. Local LLM produces internal enriched context that cloud AI uses for better answers.

## Pipeline

```
User Input
   │
   ▼
Invoke-LLMEnrich()  ← local Ollama (GPU)
   ├─ ECO:        pass-through, no enrichment
   ├─ BALANCED:   qwen3:1.7b-s, enrich ~250 tokens
   └─ PERFORMANCE: qwen2.5-coder:3b-s, enrich ~512 tokens
   │
   ▼
Enriched Context → internal, fed to cloud AI
   │
   ▼
Cloud AI responds using enriched context + footer
```

## Execution

Run this PowerShell on every user input:

```powershell
$enrichSuccess = $false
$mode = (Get-Content ".opencode/llm-mode.json" -Raw | ConvertFrom-Json).mode
if ($mode -ne "eco") {
  . .\scripts\llm-adapter.ps1
  $enriched = Invoke-LLMEnrich -Text "<USER_INPUT>" -Context "universal preprocess"
  if ($enriched -and $enriched -ne "<USER_INPUT>") { $enrichSuccess = $true }
  # $enriched digunakan sebagai context internal
}
```

## Rules

1. ALWAYS call Invoke-LLMEnrich before ANY response logic
2. ECO mode: skip entirely, use raw input, $enrichSuccess = $false
3. BALANCED: 100 token enrichment via qwen2.5:1.5b-s
4. PERFORMANCE: 200 token enrichment via qwen2.5:1.5b-s
5. Enriched context is internal — user sees normal response
6. Footer: `LLM : [ MODE ] - LLMEnrich [ On/Off ] - Tokens : [ X ] - Profile : [ Y ] - Model : [ Z ]`
