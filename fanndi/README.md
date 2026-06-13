# ECC OpenCode Setup

Portable ECC setup untuk OpenCode. Tinggal pilih profile, jalankan install script, selesai.

## Profiles

| Profile | Models | Biaya |
|---------|--------|-------|
| **gratis** | MiMo-V2.5 Free, DeepSeek V4 Flash Free | $0 (rate limited) |
| **go** | Kimi K2.7, Qwen3.7 Max, DeepSeek V4 Pro | $5/bulan pertama |

## Quick Start

### Windows (PowerShell)

```powershell
# Dari dalam ECC repo
.\fanndi\install.ps1 -Profile gratis

# Atau dari folder ecc-setup yang sudah di-clone
.\install.ps1 -Profile gratis
```

### macOS / Linux

```bash
# Dari dalam ECC repo
chmod +x ./fanndi/install.sh
./fanndi/install.sh --profile gratis

# Atau dari folder ecc-setup yang sudah di-clone
chmod +x ./install.sh
./install.sh --profile gratis
```

## Switch Profile

Mau ganti dari gratis ke go (atau sebaliknya)?

```powershell
# Windows
.\fanndi\install.ps1 -Profile go

# macOS/Linux
./fanndi/install.sh --profile go
```

Config lama otomatis di-backup.

## Update ECC

```bash
cd C:\Users\FANNNDI\Documents\ecc   # atau dimana ECC repo-mu
git fetch upstream
git merge upstream/main --no-edit
git push origin main

# Re-install rules kalau ada perubahan
.\fanndi\install.ps1 -Profile gratis
```

## Laptop Baru

```bash
# 1. Clone ECC
git clone https://github.com/fannndi/ECC.git
cd ECC

# 2. Install
.\fanndi\install.ps1 -Profile gratis   # Windows
./fanndi/install.sh --profile gratis    # macOS/Linux

# 3. Login & start
opencode /connect
opencode
```

## Token Optimization

### Caveman Mode (Built-in)

Semua profile sudah include **Caveman Mode** — terse-style system prompt yang bikin LLM reply singkat tapi akurat.

| Before | After |
|--------|-------|
| "I've analyzed the code and found that there's a missing null check..." | "Missing null check in auth.ts:42. Fix: `const token = user?.token ?? '';`" |
| ~200-500 tokens/response | ~50-150 tokens/response |
| **~60% token savings** | |

Caveman Mode sudah aktif otomatis di semua agents. Untuk non-aktifkan, hapus field `system` dari `opencode.jsonc`.

### 9Router Integration (Optional)

Untuk **RTK Token Saver** (lossless compression tool_result), pasang 9Router sebagai proxy:

```
[OpenCode] → [9Router] → [OpenCode Go API]
               ↓
         Compress: git diff, grep, ls, find
         -20-40% token usage
```

9Router jalan terpisah dari ECC. Install 9Router dulu, lalu point OpenCode ke 9Router endpoint.

**Estimated savings:**

| Layer | Savings |
|-------|---------|
| Caveman Mode (built-in) | ~60% output tokens |
| 9Router RTK (optional) | ~20-40% input tokens |
| **Combined** | **~50-70% total tokens** |

## File Structure

```
fanndi/
├── README.md              # Dokumentasi ini
├── install.ps1            # Install script Windows
├── install.sh             # Install script macOS/Linux
├── caveman-mode.md        # Caveman Mode reference
├── gratis/
│   └── opencode.jsonc     # Config: free models + Caveman Mode
└── go/
    └── opencode.jsonc     # Config: Go models + Caveman Mode
```

## What Gets Installed

| Komponen | Jumlah | Lokasi |
|----------|--------|--------|
| Agents | 24 | `~/.config/opencode/opencode.jsonc` |
| Commands | 31 | `~/.config/opencode/opencode.jsonc` |
| Skills | 14 | Loaded from ECC repo via instructions |
| Rules | 4 pack | `~/.config/opencode/rules/ecc/` |
| Hooks | 5 | Plugin hooks via ecc-universal |
| Custom Tools | 3 | run-tests, check-coverage, security-audit |

## Model Reference

### Gratis Models

| Agent | Model |
|-------|-------|
| build (primary) | `opencode/mimo-v2.5-free` |
| planner, architect, code-reviewer | `opencode/deepseek-v4-flash-free` |
| security, tdd, e2e, dll | `opencode/deepseek-v4-flash-free` |
| build-error-resolver | `opencode/mimo-v2.5-free` |

### Go Models

| Agent | Model |
|-------|-------|
| build (primary) | `opencode-go/kimi-k2.7` |
| planner, architect, tdd-guide | `opencode-go/qwen3.7-max` |
| code-reviewer, security, go-reviewer | `opencode-go/deepseek-v4-pro` |
| build-error-resolver, e2e, dll | `opencode-go/deepseek-v4-flash` |

## Troubleshooting

### Config tidak ke-load

```bash
# Cek config location
cat ~/.config/opencode/opencode.jsonc

# Cek apakah file exist
ls -la ~/.config/opencode/
```

### Plugin hooks tidak jalan

```bash
# Rebuild plugin
cd C:\Users\FANNNDI\Documents\ecc
npm run build:opencode
```

### Rate limit (gratis)

Kalau kena rate limit, switch ke go profile atau tunggu reset.
