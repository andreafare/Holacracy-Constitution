# Olos Deployment on Railway

Guide to deploy the Olos agent (OpenClaw) on Railway.

## Prerequisites

- [Railway](https://railway.app) Account
- API key of an AI provider (e.g., Anthropic for Claude)
- This cloned repository

## 1. One-click Deploy

Use the official OpenClaw template for Railway:

**[Deploy on Railway](https://railway.com/deploy/clawdbot-railway-template)**

## 2. Environment Variables Configuration

In the Railway panel of the service, set:

### Required

| Variable | Value | Notes |
|---|---|---|
| `OPENCLAW_GATEWAY_PORT` | `8080` | Must match the HTTP Proxy port |
| `OPENCLAW_GATEWAY_TOKEN` | `<your-secret-token>` | Admin credential — keep it safe |

### Recommended

| Variable | Value | Notes |
|---|---|---|
| `OPENCLAW_STATE_DIR` | `/data/.openclaw` | Configuration persistence across redeploys |
| `OPENCLAW_WORKSPACE_DIR` | `/data/workspace` | Agent's working directory |

## 3. Persistent Storage

Add a **Volume** to the Railway service mounted on `/data`. This preserves configuration, credentials, and workspace across redeploys.

## 4. Networking

Enable **HTTP Proxy** on the Railway service, port `8080`.

## 5. Interface Access

After provisioning, retrieve the public domain from Railway (Settings > Domains). The control interface is available at:

```
https://<your-railway-domain>/openclaw
```

Use the token configured in `OPENCLAW_GATEWAY_TOKEN` to authenticate.

## 6. Olos Workspace Configuration

Once inside the Control UI or via Railway shell, copy the files of this workspace into the agent:

```bash
# From Railway shell or via openclaw CLI
mkdir -p /data/workspace

# Copy Olos workspace files
cp openclaw/AGENTS.md   /data/workspace/AGENTS.md
cp openclaw/SOUL.md     /data/workspace/SOUL.md
cp openclaw/IDENTITY.md /data/workspace/IDENTITY.md
cp openclaw/USER.md     /data/workspace/USER.md
```

Alternatively, configure `openclaw.json` to point the workspace to the `openclaw/` folder of this repo (if mounted):

```json5
{
  agents: {
    defaults: {
      workspace: "/data/workspace"
    }
  }
}
```

## 7. AI Model Configuration

From the Control UI or via `openclaw.json`:

```json5
{
  model: {
    primary: "anthropic/claude-sonnet-4-6"
  }
}
```

## 8. Channel Connection (optional)

From the Control UI or via `openclaw onboard`, connect the desired channels:

- **Telegram** — create a bot via @BotFather and enter the token
- **WhatsApp** — scan the QR code
- **Discord** — enter the bot token

## 9. Verification

```bash
openclaw --version
openclaw doctor
openclaw gateway status
```

## 10. Backup

```bash
openclaw backup create
```

Creates portable backups that can be restored on any OpenClaw instance.

## Maintenance

- Update OpenClaw: `npm install -g openclaw@latest` (or redeploy from the template)
- The files `SOUL.md`, `IDENTITY.md`, and `AGENTS.md` can be updated at any time
- The agent rereads them at each new session
