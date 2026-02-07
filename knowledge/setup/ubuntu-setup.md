# Ubuntu PC セットアップ手順

専用 Ubuntu PC に OpenClaw をインストールし、Slack 等から問い合わせ可能な状態にするまでの手順。

## 前提条件

- Ubuntu 22.04 または 24.04 LTS
- インターネット接続

## 1. Node.js 22+ のインストール

```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs
node -v  # v22.x.x を確認
```

## 2. Docker のインストール（サンドボックス用）

```bash
sudo apt install -y docker.io
sudo usermod -aG docker $USER
# ログアウト→ログインで反映
docker run hello-world  # 動作確認
```

## 3. OpenClaw のインストール

```bash
npm install -g openclaw@latest
openclaw --version
```

## 4. セットアップウィザード

```bash
openclaw onboard --install-daemon
```

ウィザードが Gateway、ワークスペース、チャネル、スキルの設定を対話的に案内する。

## 5. リポジトリの配置

```bash
cd ~/.openclaw/workspace
git clone https://github.com/<org>/<repo>.git
```

または設定でワークスペースパスを直接指定:

```json5
{
  agents: {
    defaults: {
      workspace: "/home/<user>/code/<repo>"
    }
  }
}
```

## 6. Gateway の起動確認

```bash
openclaw channels status --probe
```

## 次のステップ

- [Slack 接続](./slack-integration.md)
- [Docker サンドボックス設定](./docker-sandbox.md)
