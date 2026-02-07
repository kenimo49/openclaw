# Slack 接続手順

OpenClaw を Slack に接続し、DM やチャネルからエージェントに問い合わせ可能にする。

## 1. Slack App の作成

1. https://api.slack.com/apps にアクセス
2. 「Create New App」→「From scratch」
3. App 名と Workspace を選択

## 2. 必要な権限（Bot Token Scopes）

- `chat:write` - メッセージ送信
- `app_mentions:read` - メンション検知
- `im:history` - DM 履歴
- `im:read` - DM 読み取り
- `im:write` - DM 送信

## 3. Socket Mode の有効化

1. 「Socket Mode」を ON にする
2. App-Level Token (`xapp-...`) を生成

## 4. Bot Token の取得

「OAuth & Permissions」から Bot User OAuth Token (`xoxb-...`) を取得。

## 5. OpenClaw 設定

`~/.openclaw/openclaw.json`:

```json5
{
  channels: {
    slack: {
      enabled: true,
      appToken: "xapp-...",
      botToken: "xoxb-..."
    }
  }
}
```

## 6. 動作確認

```bash
openclaw channels status --probe
```

Slack で Bot に DM を送り、ペアリングコードが返ってくることを確認。

```bash
openclaw pairing approve slack <code>
```

承認後、Bot が応答するようになる。

## グループチャネルでの利用

グループでは `@bot名` でメンションするとエージェントが応答する（デフォルト: mention gating）。
