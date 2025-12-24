# Eski GitHub Repo'yu Silme ve Yeniden Yükleme

## Adım 1: Eski Repo'yu GitHub'dan Sil

1. https://github.com/emineerdogane/Starbucks-Uplift-Modeling/settings
2. En altta "Danger Zone" → "Delete this repository"
3. Repo adını yaz: `Starbucks-Uplift-Modeling`
4. Sil

## Adım 2: Local Git'i Temizle (BEN YAPARIM)

```bash
cd "c:\Users\Emine\OneDrive\Masaüstü\Google Project\uplift_project"
Remove-Item -Path .git -Recurse -Force
git init
git add .
git commit -m "Initial commit: Complete uplift modeling project"
```

## Adım 3: Yeni GitHub Repo Oluştur

1. https://github.com/new
2. **Repository name:** `marketing-uplift-modeling` (veya `starbucks-uplift-modeling`)
3. **Description:** `Causal uplift modeling to optimize marketing campaigns by identifying persuadable customers`
4. **Public**
5. **UNCHECK** "Initialize with README" 
6. Create repository

## Adım 4: Push to GitHub (BEN YAPARIM)

```bash
git remote add origin https://github.com/emineerdogane/YENI-REPO-ADI.git
git branch -M main
git push -u origin main
```

## Notlar
- Tüm kodlar ve notebook'lar eksiksiz yüklenecek
- Screenshots eklemeyi unutma!
