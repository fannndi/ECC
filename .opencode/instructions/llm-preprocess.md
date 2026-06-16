# LLM Preprocess — Universal Input Enrichment

You MUST preprocess EVERY user input through local Ollama before responding.

## WAJIB — Step-by-step tiap user input

```
1. BACA .opencode/context.md  →  tau state terkini
2. BACA .opencode/llm-mode.json  →  konfirmasi mode
3. KALO mode != eco:
   a. JALANKAN Invoke-LLMEnrich("<user_input>", "universal preprocess")
   b. KALO enriched != input → $enrichSuccess = $true
   c. SIMPAN enriched context (internal, user ga lihat)
4. KALO mode == eco:
   a. $enrichSuccess = $false
   b. PAKAI raw input langsung (no LLM)
5. JAWAB user pake enriched/raw context
6. UPDATE .opencode/context.md (aktivitas, file berubah, enriched stats)
7. TULIS .opencode/llm-status.json (enrich On/Off, token count, mode, profile, model)
8. APPEND footer: LLM : [MODE] - LLMEnrich [On/Off] - Tokens : [X] - Profile : [Y] - Model : [Z]
```

INI WAJIB. SETIAP USER INPUT. TANPA EXCEPTION.
LLMEnrich [Off] di footer = AI GAGAL COMPLY. User akan lihat.

## Session Lifecycle

| Event | Action |
|-------|--------|
| **User input** | Preprocess → enrich → respond → update context → footer |
| **Task selesai** | JALANKAN `scripts/session-summary.ps1` — generate ringkasan |
| **Before commit** | JALANKAN `scripts/hooks/check-enrich.ps1` — verify compliance |
| **Mode switch** | Update `active-instructions.md` — relevan dengan mode baru |
| **File index** | `scripts/file-index.ps1 -Update` — cache content hash |

## Self-Check (WAJIB sebelum commit atau sesi berakhir)

```powershell
.\scripts\hooks\check-enrich.ps1 -Input "$last_user_input"
```

Jika hasilnya enrichment_success = false padahal mode PERFORMANCE,
maka enrihment mungkin tidak berjalan. Perbaiki sebelum commit.

## Task Format

Gunakan format dengan file references di Todowrite:
```
- [ ] Fix bug → scripts/sync.ps1:12
- [ ] Create test → tests/*.ps1
```

## Model Info

| Mode | Model | Enrich | VRAM |
|------|-------|--------|------|
| ECO | none | 0 tok | 0 MB |
| BALANCED | qwen2.5:1.5b-s | 100 tok | ~1075 MB |
| PERFORMANCE | qwen2.5:1.5b-s | 200 tok | ~1915 MB |
