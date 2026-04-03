# GitLab Flow Demo (GitHub + GitHub Pages)

GitLabフローの運用をGitHub上で体験するためのデモリポジトリです。

## 構成

```
ブランチ構成:
  feature/* → main → staging → production

デプロイ先 (GitHub Pages):
  /staging/    ← staging ブランチの自動デプロイ
  /production/ ← production ブランチの手動承認デプロイ
```

---

## セットアップ手順

### 1. リポジトリを作成してpush

```bash
git init
git add .
git commit -m "initial commit"
git branch -M main
git remote add origin https://github.com/<your-username>/<repo-name>.git
git push -u origin main
```

### 2. staging / production ブランチを作成

```bash
git checkout -b staging
git push origin staging

git checkout main
git checkout -b production
git push origin production
```

### 3. GitHub Pages を有効化

```
Settings → Pages
  Source: Deploy from a branch
  Branch: gh-pages / (root)
```

### 4. ブランチ保護ルールを設定

`Settings → Branches → Add branch ruleset` で以下を **main / staging / production** それぞれに設定。

| 設定項目 | 値 |
|---|---|
| Require a pull request before merging | ✅ |
| Required number of approvals | 1 |
| Require status checks to pass | ✅ |
| Restrict who can push | 必要に応じて |

### 5. Environment保護ルールを設定（手動承認）

`Settings → Environments → production` を開いて設定。

| 設定項目 | 値 |
|---|---|
| Required reviewers | 自分のアカウントを追加 |
| Wait timer | 任意（例: 0分） |

> ⚠️ `staging` 環境は承認なしのまま（自動デプロイ）にしておく。

---

## 日常の開発フロー

```bash
# 1. main から feature ブランチを作成
git checkout main
git checkout -b feature/my-feature

# 2. 開発・コミット
echo "変更内容" >> index.html
git add .
git commit -m "feat: my-feature を追加"

# 3. GitHub で PR を作成 (feature → main)
git push origin feature/my-feature
# → GitHub上でPRを作成 → レビュー → main へマージ
# → CI (ci-main.yml) が自動実行される

# 4. main → staging へ PR を作成してマージ
# → deploy-staging.yml が自動実行
# → https://<user>.github.io/<repo>/staging/ に反映

# 5. staging で動作確認後、staging → production へ PR を作成
# → 承認待ち画面が表示される（Environment保護ルール）
# → レビュアーが承認するとデプロイ開始
# → https://<user>.github.io/<repo>/production/ に反映
```

---

## デプロイ後のURL

| 環境 | URL |
|---|---|
| staging | `https://<your-username>.github.io/<repo-name>/staging/` |
| production | `https://<your-username>.github.io/<repo-name>/production/` |

---

## ファイル構成

```
.
├── index.html                          # デモアプリ（環境名を表示）
├── README.md
└── .github/
    └── workflows/
        ├── ci-main.yml                 # main: CIのみ（デプロイなし）
        ├── deploy-staging.yml          # staging: 自動デプロイ
        └── deploy-production.yml       # production: 手動承認デプロイ
```
