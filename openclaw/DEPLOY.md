# Deploy di Olos su Railway

Guida per deployare l'agente Olos (OpenClaw) su Railway.

## Prerequisiti

- Account [Railway](https://railway.app)
- API key di un provider AI (es. Anthropic per Claude)
- Questa repository clonata

## 1. Deploy con un click

Usa il template ufficiale OpenClaw per Railway:

**[Deploy su Railway](https://railway.com/deploy/clawdbot-railway-template)**

## 2. Configurazione variabili d'ambiente

Nel pannello Railway del servizio, imposta:

### Obbligatorie

| Variabile | Valore | Note |
|---|---|---|
| `OPENCLAW_GATEWAY_PORT` | `8080` | Deve coincidere con la porta HTTP Proxy |
| `OPENCLAW_GATEWAY_TOKEN` | `<il-tuo-token-segreto>` | Credenziale admin — tienila al sicuro |

### Raccomandate

| Variabile | Valore | Note |
|---|---|---|
| `OPENCLAW_STATE_DIR` | `/data/.openclaw` | Persistenza configurazione tra redeploy |
| `OPENCLAW_WORKSPACE_DIR` | `/data/workspace` | Directory di lavoro dell'agente |

## 3. Storage persistente

Aggiungi un **Volume** al servizio Railway montato su `/data`. Questo preserva configurazione, credenziali e workspace tra i redeploy.

## 4. Networking

Attiva **HTTP Proxy** sul servizio Railway, porta `8080`.

## 5. Accesso all'interfaccia

Dopo il provisioning, recupera il dominio pubblico da Railway (Settings > Domains). L'interfaccia di controllo e disponibile su:

```
https://<tuo-dominio-railway>/openclaw
```

Usa il token configurato in `OPENCLAW_GATEWAY_TOKEN` per autenticarti.

## 6. Configurazione del workspace di Olos

Una volta dentro la Control UI o via shell Railway, copia i file di questo workspace nell'agente:

```bash
# Dalla shell Railway o via openclaw CLI
mkdir -p /data/workspace

# Copia i file del workspace Olos
cp openclaw/AGENTS.md   /data/workspace/AGENTS.md
cp openclaw/SOUL.md     /data/workspace/SOUL.md
cp openclaw/IDENTITY.md /data/workspace/IDENTITY.md
cp openclaw/USER.md     /data/workspace/USER.md
```

In alternativa, configura `openclaw.json` per puntare il workspace alla cartella `openclaw/` di questa repo (se montata):

```json5
{
  agents: {
    defaults: {
      workspace: "/data/workspace"
    }
  }
}
```

## 7. Configurazione modello AI

Dalla Control UI o via `openclaw.json`:

```json5
{
  model: {
    primary: "anthropic/claude-sonnet-4-6"
  }
}
```

## 8. Connessione canali (opzionale)

Dalla Control UI o via `openclaw onboard`, collega i canali desiderati:

- **Telegram** — crea un bot via @BotFather e inserisci il token
- **WhatsApp** — scansiona il QR code
- **Discord** — inserisci il bot token

## 9. Verifica

```bash
openclaw --version
openclaw doctor
openclaw gateway status
```

## 10. Backup

```bash
openclaw backup create
```

Crea backup portabili ripristinabili su qualsiasi istanza OpenClaw.

## Manutenzione

- Aggiorna OpenClaw: `npm install -g openclaw@latest` (o redeploy dal template)
- I file `SOUL.md`, `IDENTITY.md` e `AGENTS.md` possono essere aggiornati in qualsiasi momento
- L'agente li rilegge ad ogni nuova sessione
