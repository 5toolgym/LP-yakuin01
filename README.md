# lp-yakuin01 — 5toolgym 薬院店 LP

Cloudflare Workers (static assets) でホストされているランディングページ群。

## デプロイ

`main` ブランチへの push で GitHub Actions が自動デプロイ。

```
現在の公開URL: https://lp-yakuin01.5toolgym.workers.dev
```

---

## 計測 ID（本番値）

| ツール | 変数名 | 本番 ID |
|--------|--------|---------|
| GA4 | `GA_ID` | `G-JB7FMH9VST` |
| Meta Pixel | `PIXEL_ID` | `970383788661577` |
| Clarity | `CLARITY_ID` | `vx3scpfrrn` |

ID を変更する場合は、各 LP の `<head>` 内 `<script>` 冒頭の変数を書き換える。

---

## 計測設計

### Meta Pixel
- `PageView`: ページロード時に自動発火
- `Lead`: かんざし予約リンクのクリック時に発火（全 CTA 共通）

### GA4 カスタムイベント
| イベント名 | パラメータ | 発火タイミング |
|-----------|-----------|-------------|
| `cta_click` | `link_position: 'sticky' / 'sticky_mobile' / 'fv' / 'trust' / 'experience' / 'pricing' / 'final'` | かんざしリンクのクリック時 |

### 動作確認手順
1. **Meta Pixel**: [Meta Event Manager](https://business.facebook.com/events_manager/) → テストイベント → LP を開いてボタンクリック
2. **GA4**: [DebugView](https://analytics.google.com/) → GA4 管理画面 → DebugView → LP を Chrome で開く（`?gtm_debug=1` or GA4 Chrome 拡張）

---

## カスタムドメイン設定（lp.5toolgym.com 等）

### 前提
- `5toolgym.com` のゾーンが同一 Cloudflare アカウントに登録されていること

### 手順

1. **Cloudflare ダッシュボードでルートを追加**
   - Workers & Pages → `lp-yakuin01` → Settings → Domains & Routes → Add
   - Route に `lp.5toolgym.com/*` を入力（または `lp.5toolgym.com` のカスタムドメイン）

2. **DNS レコードを追加**（Cloudflare DNS 管理画面）
   ```
   Type: CNAME
   Name: lp
   Target: lp-yakuin01.5toolgym.workers.dev
   Proxy: ON（オレンジ雲）
   ```

3. **canonical URL と og:url を更新**（各 LP の `<head>`）
   ```html
   <link rel="canonical" href="https://lp.5toolgym.com/5toolgym_lp_yakuin_sasaru">
   <meta property="og:url" content="https://lp.5toolgym.com/5toolgym_lp_yakuin_sasaru">
   ```

4. **noscript Pixel URL も更新**（絶対 URL のため）
   - `<noscript>` の `<img src>` 内の URL は Meta Pixel 側のトラッキング URL のため変更不要

> ドメイン切替後は workers.dev URL も引き続き動作する。広告のリンク先を新ドメインに切り替えてから古い URL をリダイレクトするか放置するかは別途判断。

---

## ファイル構成

```
/
├── 5toolgym_lp_yakuin_sasaru.html   # さとるさんLP（ダイエット特化）
├── index.html                        # 薬院メインLP
├── images/                           # WebP最適化済み画像
├── wrangler.toml
└── .github/workflows/deploy.yml
```
