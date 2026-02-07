# 本番向けセキュリティ設定

OpenClaw を外部チャネル（Slack 等）から利用する際のセキュリティ強化ガイド。

## チェックリスト

- [ ] DM ポリシーが `pairing`（デフォルト）であること
- [ ] サンドボックスが有効（`mode: "all"` or `"non-main"`）
- [ ] `workspaceAccess` が `"ro"` または `"none"`
- [ ] `network` が `"none"`
- [ ] `elevated.enabled` が `false`（デフォルト）
- [ ] Gateway の bind が `loopback`
- [ ] Gateway に認証トークンが設定されている
- [ ] `openclaw security audit` で警告がないこと
- [ ] Gateway を動かすユーザーが sudoers に入っていないこと

## 専用ユーザーの作成（推奨）

```bash
sudo useradd -m -s /bin/bash openclaw-user
sudo -u openclaw-user -i

# openclaw-user として以下を実行
npm install -g openclaw@latest
openclaw onboard --install-daemon
```

sudo 権限を持たない専用ユーザーで動かすことで、仮にサンドボックスを突破されてもホストへの影響を最小化できる。

## セキュリティ監査

```bash
# 基本監査
openclaw security audit

# 詳細（Gateway への実際のプローブを含む）
openclaw security audit --deep

# 自動修正
openclaw security audit --fix
```

## ファイルパーミッション

`openclaw doctor` が自動チェックするが、手動確認する場合:

```bash
ls -la ~/.openclaw/
# drwx------ (0o700) であること

ls -la ~/.openclaw/openclaw.json
# -rw------- (0o600) であること
```

## 定期メンテナンス

- `openclaw doctor` を定期的に実行
- Gateway のログを確認: `tail -f /tmp/openclaw-gateway.log`
- Docker コンテナの状態確認: `docker ps -a | grep openclaw`
