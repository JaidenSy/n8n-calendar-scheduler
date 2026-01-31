# n8n Local Setup Guide

Run n8n locally for free to learn and build workflows.

---

## Option 1: npx (Recommended)

The easiest way to run n8n locally. Just one command.

### Requirements

- Node.js 18+ installed
- Check with: `node --version`

### Run

```bash
npx n8n
```

First run downloads n8n, then starts it. Access at `http://localhost:5678`.

### Run in Background

```bash
# Start
npx n8n start

# Or with PM2 (process manager)
npm install -g pm2
pm2 start npx -- n8n
pm2 save
```

---

## Option 2: Docker

If you prefer containers.

### Quick Start

```bash
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v n8n_data:/home/node/.n8n \
  n8nio/n8n
```

### Persistent with Docker Compose

Create `docker-compose.yml`:

```yaml
version: '3'
services:
  n8n:
    image: n8nio/n8n
    restart: unless-stopped
    ports:
      - "5678:5678"
    volumes:
      - n8n_data:/home/node/.n8n
    environment:
      - N8N_SECURE_COOKIE=false
      - GENERIC_TIMEZONE=America/Los_Angeles
volumes:
  n8n_data:
```

Run:
```bash
docker-compose up -d
```

---

## Exposing for Webhooks

For Claude Code to call your n8n webhooks, you need a public URL.

### ngrok (Free, Temporary)

1. Install ngrok: https://ngrok.com/download
2. Sign up for free account and authenticate:
   ```bash
   ngrok config add-authtoken <your-token>
   ```
3. Run n8n, then in another terminal:
   ```bash
   ngrok http 5678
   ```
4. Use the `https://xxxx.ngrok.io` URL for webhooks

**Note:** Free ngrok URLs change each restart. Paid plan ($8/mo) gets stable URLs.

### Cloudflare Tunnel (Free, More Stable)

1. Install cloudflared: https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/
2. Run:
   ```bash
   cloudflared tunnel --url http://localhost:5678
   ```
3. Get a temporary public URL (changes on restart)

For permanent URL, set up a tunnel with your domain (free with Cloudflare account).

---

## Environment Variables

Configure n8n behavior with environment variables.

### Common Settings

```bash
# Set timezone
export GENERIC_TIMEZONE="America/Los_Angeles"

# Change port
export N8N_PORT=5678

# Set encryption key (important for credentials)
export N8N_ENCRYPTION_KEY="your-random-string-here"

# Webhook URL (for production)
export WEBHOOK_URL="https://your-domain.com"
```

### Windows (PowerShell)

```powershell
$env:GENERIC_TIMEZONE="America/Los_Angeles"
npx n8n
```

### Persistent (Windows)

Add to System Environment Variables or create a batch file:

```batch
@echo off
set GENERIC_TIMEZONE=America/Los_Angeles
npx n8n
```

---

## Backup Your Workflows

### Export Individual Workflow

1. Open workflow in n8n
2. Click menu (three dots) → Download

### Export All Workflows

```bash
# Using n8n CLI
npx n8n export:workflow --all --output=./backups/

# Specific workflow
npx n8n export:workflow --id=1 --output=./workflow.json
```

### Backup Credentials

```bash
npx n8n export:credentials --all --output=./backups/
```

### Backup Location

Copy the entire `.n8n` folder for full backup:
- Workflows: `~/.n8n/database.sqlite`
- Credentials: Encrypted in same database

---

## Workflow Storage in This Repo

Save your workflow JSON files to this repo for version control:

```
docs/
└── workflows/
    ├── calendar-learning-scheduler.md    # Documentation
    ├── calendar-learning-scheduler.json  # Exported workflow
    └── ...
```

### Export and Save

1. Build workflow in n8n
2. Export as JSON
3. Save to `docs/workflows/`
4. Commit and push

This lets you:
- Version control your workflows
- Sync between devices
- Share with others

---

## Troubleshooting

### Port Already in Use

```bash
# Find what's using port 5678
# Windows
netstat -ano | findstr :5678

# Mac/Linux
lsof -i :5678

# Use different port
N8N_PORT=5679 npx n8n
```

### Credentials Not Working

- Check OAuth callback URLs match your n8n URL
- For Google: Add `http://localhost:5678/rest/oauth2-credential/callback` to authorized redirects

### Webhook Not Receiving Data

- Ensure n8n is running
- Check webhook is activated (toggle on)
- Use test URL for development, production URL when exposed
- Verify firewall isn't blocking

### Node.js Version Issues

```bash
# Check version (need 18+)
node --version

# Use nvm to manage versions
nvm install 18
nvm use 18
```

---

## Quick Reference

| Task | Command |
|------|---------|
| Start n8n | `npx n8n` |
| Start on custom port | `N8N_PORT=5679 npx n8n` |
| Export all workflows | `npx n8n export:workflow --all` |
| Import workflow | `npx n8n import:workflow --input=file.json` |
| Expose locally | `ngrok http 5678` |

---

## Next Steps

1. [ ] Install n8n (desktop app or npx)
2. [ ] Run and access at localhost:5678
3. [ ] Complete Phase 1 of learning path
4. [ ] Build the calendar scheduler workflow
5. [ ] Set up ngrok when ready for webhooks
6. [ ] Share webhook URL with Claude Code
