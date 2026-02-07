# Exec ポリシーの選び方

エージェントのコマンド実行権限をどう設定するかのガイド。

## セキュリティモード

| モード | 動作 | 推奨用途 |
|---|---|---|
| `deny` | 全コマンド拒否（デフォルト） | 読み取り専用エージェント |
| `allowlist` | 許可リストのみ実行可 | 本番運用 |
| `full` | 何でも実行可 | 開発・テスト時のみ |

## Ask モード（allowlist との組み合わせ）

| モード | 動作 |
|---|---|
| `off` | 許可リストに合わなければ即拒否 |
| `on-miss` | 許可リストに合わなければ人間に承認を求める（推奨） |
| `always` | 毎回必ず人間に承認を求める |

## 推奨設定（リポジトリ質問用途）

サンドボックスを使う場合は exec ポリシーは補助的な役割:

```json5
// ~/.openclaw/exec-approvals.json
{
  "version": 1,
  "defaults": {
    "security": "allowlist",
    "ask": "on-miss",
    "askFallback": "deny"
  },
  "agents": {
    "main": {
      "allowlist": [
        { "pattern": "/usr/bin/git" },
        { "pattern": "/usr/bin/grep" },
        { "pattern": "/usr/bin/ls" },
        { "pattern": "/usr/bin/find" },
        { "pattern": "/usr/bin/cat" }
      ]
    }
  }
}
```

## Safe Bins（常に許可される読み取り系コマンド）

デフォルトで以下は許可リストなしでも通る（ファイルパス引数なしの場合のみ）:

```
jq, grep, cut, sort, uniq, head, tail, tr, wc
```

## サンドボックスとの併用

サンドボックス（Docker）を使う場合、exec ポリシーは二重防御として機能する:

1. サンドボックス → 実行環境の隔離（ネットワーク遮断、権限なし）
2. exec ポリシー → 実行できるコマンド自体を制限

両方設定するのが最も安全。
