# n8n Calendar Scheduler

An n8n workflow that connects Claude Code to Google Calendar for scheduling learning sessions.

## Quick Start

### 1. Start n8n

**Windows:**
```bash
start.bat
# or
npm start
```

**Mac/Linux:**
```bash
./start.sh
# or
npm start
```

### 2. Open n8n

Go to http://localhost:5678

### 3. Import the Workflow

1. Click **Add Workflow** → **Import from File**
2. Select `workflows/calendar-scheduler.json`
3. Click **Import**

### 4. Set Up Google Calendar Credentials

1. In n8n, go to **Settings** → **Credentials**
2. Click **Add Credential** → Search "Google Calendar"
3. Select **Google Calendar OAuth2 API**
4. Follow the OAuth flow:
   - Create a project in [Google Cloud Console](https://console.cloud.google.com)
   - Enable Google Calendar API
   - Create OAuth 2.0 credentials
   - Add redirect URI: `http://localhost:5678/rest/oauth2-credential/callback`
   - Copy Client ID and Client Secret to n8n

### 5. Connect Credentials to Workflow

1. Open the imported workflow
2. Click each Google Calendar node (4 total)
3. Select your Google Calendar credential
4. Save the workflow

### 6. Activate & Get Webhook URL

1. Toggle the workflow **Active** (top right)
2. Click the **Webhook** node
3. Copy the **Production URL**

## Webhook Endpoints

### Get Availability
```bash
curl -X POST https://your-url/webhook/claude-calendar \
  -H "Content-Type: application/json" \
  -d '{"action": "get_availability"}'
```

### Create Learning Session
```bash
curl -X POST https://your-url/webhook/claude-calendar \
  -H "Content-Type: application/json" \
  -d '{
    "action": "create_session",
    "data": {
      "title": "n8n Learning: Phase 1 - Foundations",
      "description": "Topics: nodes, connections, triggers",
      "start_time": "2024-01-20T18:00:00",
      "duration_minutes": 60,
      "phase": "1",
      "topics": ["nodes", "connections", "triggers"]
    }
  }'
```

### Get Schedule
```bash
curl -X POST https://your-url/webhook/claude-calendar \
  -H "Content-Type: application/json" \
  -d '{"action": "get_schedule"}'
```

### Update Progress
```bash
curl -X POST https://your-url/webhook/claude-calendar \
  -H "Content-Type: application/json" \
  -d '{
    "action": "update_progress",
    "data": {
      "event_id": "abc123",
      "notes": "Completed basic workflow tutorial"
    }
  }'
```

## Exposing for Claude Code

For Claude to call your webhook, expose n8n to the internet:

### Option 1: ngrok (easiest)
```bash
# In a new terminal
ngrok http 5678

# Copy the https URL (e.g., https://abc123.ngrok-free.app)
# Your webhook is at: https://abc123.ngrok-free.app/webhook/claude-calendar
```

### Option 2: localtunnel
```bash
npx localtunnel --port 5678
```

## Project Structure

```
n8n-calendar-scheduler/
├── README.md
├── package.json
├── .env.example
├── .gitignore
├── start.bat          # Windows start script
├── start.sh           # Mac/Linux start script
└── workflows/
    └── calendar-scheduler.json
```

## Sharing with Claude Code

Once your webhook is accessible, tell Claude:

> "My n8n webhook URL is https://your-url/webhook/claude-calendar"

Claude can then:
- Check your calendar availability
- Schedule learning sessions
- View upcoming sessions
- Mark sessions as completed

## Troubleshooting

### Webhook not working
- Ensure workflow is **Active** (toggle is on)
- Use **Production URL**, not Test URL
- Check n8n is running and accessible

### Google Calendar auth fails
- Verify redirect URI matches exactly
- Check OAuth consent screen is configured
- Ensure Calendar API is enabled in Google Cloud

### Can't connect from Claude
- Make sure ngrok/tunnel is running
- Verify the full webhook URL is correct
- Test with curl first

## Next Steps

1. [ ] Start n8n
2. [ ] Import workflow
3. [ ] Connect Google Calendar
4. [ ] Test with curl
5. [ ] Set up ngrok
6. [ ] Share webhook URL with Claude
