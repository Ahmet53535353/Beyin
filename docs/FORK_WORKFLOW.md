# Beyin Fork Workflow (Profesyonel Dal Stratejisi)

Repo: `Ahmet53535353/Beyin` (fork)
Upstream: `avenoxai/avenoxbeyin`

## 1. Dalları profesyonel kullan

| Dal | Amaç | Örnek |
|-----|------|-------|
| `main` | Yayınlanabilir, testten geçmiş kod | — |
| `feat/<ad>` | Yeni özellik | `feat/telegram-bildirim` |
| `fix/<ad>` | Hata düzeltme | `fix/update-cache` |
| `sync/upstream-2026-11-08` | Upstream senkronu (geçici) | — |

**Kural:** `main`'e doğrudan commit atma. Önce dal aç.

## 2. Yeni özellik ekleme adımları

```powershell
git checkout main
git pull --ff-only origin main

# 1) Yeni dal aç
git checkout -b feat/ozellik-adi

# 2) Kodu yaz, test et
python -m unittest tests.v3_releases_test
python -m unittest tests.v3_update_test

# 3) Commit + push
git add .
git commit -m "feat: ozellik-adi..."
git push -u origin feat/ozellik-adi

# 4) GitHub'da PR aç → incele → "Squash and merge" veya "Merge"
# 5) PR merge olunca main güncellenir:
git checkout main
git pull --ff-only origin main
git branch -d feat/ozellik-adi
```

## 3. Upstream'den senkron (yine de alabilirsin)

Upstream'de yeni bir şey varsa, önce **geçici dalda** birleştir:

```powershell
git fetch upstream --prune
git checkout main
git pull --ff-only origin main

git checkout -b sync/upstream-$(Get-Date -Format yyyy-MM-dd)
git rebase upstream/main
# çakışma varsa çöz: git add <dosya>; git rebase --continue
# iptal: git rebase --abort

# test et
python -m unittest tests.v3_releases_test

# main'e al
git checkout main
git merge --no-ff sync/upstream-2026-11-08 -m "merge(sync): upstream senkron"
git branch -d sync/upstream-2026-11-08
git push origin main
```

## 4. Release çıkarma

```powershell
# 1) VERSION bump (X.Y.Z zorunlu)
#    VERSION: 3.12.1 -> 3.13.0
# 2) docs/v3/releases/3.13.0.md oluştur
# 3) commit + push
git add VERSION docs/v3/releases/3.13.0.md
git commit -m "release(v3.13.0): ..."
git push origin main
git tag v3.13.0
git push origin v3.13.0

# 4) GitHub Actions: Verified V3 release package → publish=true
gh workflow run "Verified V3 release package" --repo Ahmet53535353/Beyin --ref main -f publish=true
```

## 5. Son kullanıcı

Değişiklik yok: `beyin.py update` zaten fork'undan çekiyor.
