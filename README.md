# Claude HUD

Claude Code のステータスラインプラグイン。コンテキスト使用量、アクティブツール、エージェント、Todo進捗をリアルタイム表示。

> [jarrodwatts/claude-hud](https://github.com/jarrodwatts/claude-hud) のフォーク。マルチセッション環境でのキャッシュTTL延長と429バックオフ強化により安定性を改善。

[![License](https://img.shields.io/github/license/paseriINU/claude-hud)](LICENSE)

![Claude HUD in action](claude-hud-preview-5-2.png)

## インストール

Claude Code内で以下のコマンドを実行:

**Step 1: マーケットプレイスを追加**
```
/plugin marketplace add paseriINU/claude-hud
```

**Step 2: プラグインをインストール**

<details>
<summary><strong>Linux環境の場合（クリックして展開）</strong></summary>

Linuxでは `/tmp` が別のファイルシステム(tmpfs)であることが多く、インストールに失敗する場合がある:
```
EXDEV: cross-device link not permitted
```

**対処法**: インストール前にTMPDIRを設定:
```bash
mkdir -p ~/.cache/tmp && TMPDIR=~/.cache/tmp claude
```

そのセッションで以下のインストールコマンドを実行する。

</details>

```
/plugin install claude-hud
```

**Step 3: ステータスラインを設定**
```
/claude-hud:setup
```

設定完了。再起動不要ですぐに表示される。

---

## フォークの変更点

元リポジトリからの変更:

| 変更 | 元の値 | フォーク |
|------|--------|---------|
| 成功キャッシュTTL | 60秒 | 300秒 (5分) |
| 失敗キャッシュTTL | 15秒 | 60秒 |
| ロックstale判定 | 30秒 | 45秒 |
| 429バックオフ初期値 | 60秒 | 120秒 |
| 429バックオフ最大値 | 5分 | 15分 |

tmuxで複数Claude Codeセッションを並走させた際の429エラーを大幅に削減する。

---

## 機能

| 表示項目 | 説明 |
|---------|------|
| **プロジェクトパス** | 作業中のプロジェクト名 (1-3階層で設定可) |
| **コンテキスト使用量** | コンテキストウィンドウの使用率をバーで表示 |
| **ツールアクティビティ** | ファイル読み書き・検索の実行状況 |
| **エージェント追跡** | サブエージェントの実行状況 |
| **Todo進捗** | タスク完了状況のリアルタイム追跡 |

### 表示例

**デフォルト (2行)**
```
[Opus | Max] │ my-project git:(main*)
Context █████░░░░░ 45% │ Usage ██░░░░░░░░ 25% (1h 30m / 5h)
```

**オプション行 (`/claude-hud:configure` で有効化)**
```
◐ Edit: auth.ts | ✓ Read ×3 | ✓ Grep ×2        ← ツール
◐ explore [haiku]: Finding auth code (2m 15s)    ← エージェント
▸ Fix authentication bug (2/5)                   ← Todo
```

---

## 設定

```
/claude-hud:configure
```

ガイド付きフローでレイアウトと表示項目を設定できる。

### プリセット

| プリセット | 内容 |
|-----------|------|
| **Full** | 全項目表示 |
| **Essential** | アクティビティ + Git状態 |
| **Minimal** | モデル名とコンテキストバーのみ |

### 手動設定

`~/.claude/plugins/claude-hud/config.json` を直接編集:

```json
{
  "lineLayout": "expanded",
  "pathLevels": 2,
  "gitStatus": {
    "enabled": true,
    "showDirty": true,
    "showAheadBehind": true
  },
  "display": {
    "showTools": true,
    "showAgents": true,
    "showTodos": true,
    "showDuration": true
  },
  "usage": {
    "cacheTtlSeconds": 300,
    "failureCacheTtlSeconds": 60
  }
}
```

### 設定項目

| オプション | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `lineLayout` | string | `expanded` | `expanded` (複数行) / `compact` (1行) |
| `pathLevels` | 1-3 | 1 | プロジェクトパスの表示階層数 |
| `gitStatus.enabled` | boolean | true | Gitブランチ表示 |
| `gitStatus.showDirty` | boolean | true | 未コミット変更の `*` 表示 |
| `gitStatus.showAheadBehind` | boolean | false | `↑N ↓N` 表示 |
| `gitStatus.showFileStats` | boolean | false | ファイル変更数 `!M +A ✘D ?U` |
| `display.showModel` | boolean | true | モデル名表示 |
| `display.showContextBar` | boolean | true | コンテキストバー表示 |
| `display.contextValue` | string | `percent` | `percent` / `tokens` / `remaining` |
| `display.showUsage` | boolean | true | 使用量制限表示 (Pro/Max/Teamのみ) |
| `display.showTools` | boolean | false | ツールアクティビティ行 |
| `display.showAgents` | boolean | false | エージェント行 |
| `display.showTodos` | boolean | false | Todo進捗行 |
| `display.showDuration` | boolean | false | セッション経過時間 |
| `display.showSpeed` | boolean | false | 出力トークン速度 |
| `usage.cacheTtlSeconds` | number | 300 | 成功時キャッシュTTL (秒) |
| `usage.failureCacheTtlSeconds` | number | 60 | 失敗時キャッシュTTL (秒) |

---

## 上流の更新を取り込む

```bash
git fetch upstream
git merge upstream/main
```

---

## 動作要件

- Claude Code v1.0.80+
- Node.js 18+

---

## 開発

```bash
git clone https://github.com/paseriINU/claude-hud
cd claude-hud
npm ci && npm run build
npm test
```

元リポジトリの貢献ガイドライン: [CONTRIBUTING.md](CONTRIBUTING.md)

---

## ライセンス

MIT — [LICENSE](LICENSE)
