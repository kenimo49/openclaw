# Docker サンドボックス設定ガイド

エージェントのツール実行を Docker コンテナ内に隔離し、ホストを保護する。

## なぜサンドボックスか

`security: "full"` でホスト上に直接実行させると以下のリスクがある:

- `apt install sshpass` 等の勝手なパッケージインストール
- `ssh` による外部接続
- `sudo` による権限昇格
- ファイルの削除・改ざん

サンドボックスならこれらを物理的に不可能にできる。

## 推奨設定（リポジトリ質問用途）

`~/.openclaw/openclaw.json` に追加:

```json5
{
  agents: {
    defaults: {
      sandbox: {
        mode: "all",              // 全セッションをサンドボックス化
        scope: "session",         // セッションごとにコンテナ分離
        workspaceAccess: "ro",    // リポジトリは読み取り専用
        docker: {
          image: "openclaw-sandbox:bookworm-slim",
          readOnlyRoot: true,
          network: "none",        // ネットワーク遮断
          capDrop: ["ALL"],       // ケーパビリティ全削除
          pidsLimit: 256,
          memory: "1g"
        }
      }
    }
  }
}
```

## デフォルトのセキュリティ境界

| 設定 | デフォルト値 | 効果 |
|---|---|---|
| `readOnlyRoot` | `true` | コンテナFS読み取り専用 |
| `network` | `"none"` | ネットワークアクセス遮断 |
| `capDrop` | `["ALL"]` | Linux ケーパビリティ全削除 |
| `no-new-privileges` | 常時有効 | 権限昇格禁止 |

## workspaceAccess の選択

| モード | 用途 |
|---|---|
| `"none"` | ワークスペースにアクセスさせない |
| `"ro"` | コード質問用。読めるが書けない |
| `"rw"` | コード修正もさせたい場合（リスクあり） |

## やってはいけない設定

- `docker.binds` で `/var/run/docker.sock` をマウント → ホスト制御可能になる
- `network: "bridge"` → 外部通信が可能になる
- `elevated.enabled: true` → ホスト上での exec を許可してしまう

## コンテナのライフサイクル

- 24 時間アイドルまたは 7 日経過で自動削除
- `docker ps -a | grep openclaw` で確認可能
