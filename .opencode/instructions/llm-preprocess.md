# Input Processing — PERFECT COMPLIANCE

WAJIB: SETIAP user input → enrich → respond → footer.

## Pipeline

```
1. Read .opencode/llm-mode.json
2. IF mode != eco → Invoke-LLMEnrich() → GPU spike >0%
3. IF mode == eco → skip enrichment
4. EXECUTE (User: presisi/max2 tanya, Admin: goal-oriented)
5. Update .opencode/context.md
6. Write .opencode/llm-status.json
7. Append footer: Mode : [ User/Admin ] | LLMEnrich : [ On/Off ] - EnrichTime : [ X ] - Cloud : [ Y ]
```

## Rules

1. Invoke-LLMEnrich WAJIB sebelum jawab — tidak ada alasan skip
2. Footer WAJIB di setiap respons — compliance hook, bukan dekorasi
3. LLMEnrich [Off] = AI gagal comply — user langsung lihat
4. Session lifecycle: enrich → respond → update context → footer → summary (akhir task)

## Model Info

| Mode | Model | Enrich | VRAM | Timeout |
|------|-------|--------|------|---------|
| ECO | none | 0 tok | 0 MB | 0 |
| BALANCED | qwen2.5:1.5b-s | 100 tok | ~1 GB | 5 min auto-unload |
| PERFORMANCE | qwen2.5:1.5b-s | 200 tok | ~1 GB | 5 min auto-unload |

## Task Format

```
- [ ] Fix bug → scripts/sync.ps1:12
- [ ] Create test → tests/*.ps1
```
