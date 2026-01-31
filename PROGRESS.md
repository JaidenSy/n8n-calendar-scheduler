# n8n Calendar Scheduler - Progress Notes

**Last Updated:** January 30, 2026

## Current Status: COMPLETE & LIVE

### All Components Working
- [x] Webhook node configured (POST, path: `claude-calendar`)
- [x] Switch node with 4 routing rules (uses `$json.body.action`)
- [x] Branch 1 (get_availability) - Google Calendar → Code → Respond
- [x] Branch 2 (create_session) - Google Calendar → Code → Respond
- [x] Branch 3 (get_schedule) - Google Calendar → Code → Respond
- [x] Branch 4 (update_progress) - Google Calendar → Code → Respond
- [x] All branches tested successfully
- [x] Workflow activated
- [x] ngrok installed and running

### Setup Complete
- [x] n8n running at http://localhost:5678
- [x] Google Calendar credentials connected
- [x] ngrok tunnel active
- [x] Webhook URL ready for Claude

---

## Quick Start (Daily Use)

### 1. Start n8n
```bash
cd C:/Users/Jypsi/Projects/n8n-calendar-scheduler
npm start
```

### 2. Start ngrok (in separate terminal)
```bash
ngrok http 5678
```

### 3. Copy the https URL and tell Claude
> "My n8n calendar webhook is https://xxxxx.ngrok-free.app/webhook/claude-calendar"

**Note:** The ngrok URL changes each time you restart it (unless you have a paid plan).

---

## Share With Claude

When starting a new Claude session, paste this message:

```
My n8n calendar webhook is [YOUR_NGROK_URL]/webhook/claude-calendar

You can use these actions:
- get_availability: Check my calendar for free time
- create_session: Schedule n8n learning sessions
- get_schedule: See upcoming learning sessions
- update_progress: Mark sessions as completed OR reschedule (pass start_time and duration_minutes)

Example: POST to the webhook with {"action": "get_availability"}
```

**Current URL (changes on restart):**
```
https://jere-unproffered-reta.ngrok-free.dev/webhook/claude-calendar
```

---

## API Reference

### get_availability
Returns busy slots and preferred learning hours for next 7 days.
```json
{"action": "get_availability"}
```

**Response:**
```json
{
  "success": true,
  "busy_slots": [...],
  "message": "Found X events in the next 7 days"
}
```

### create_session
Creates a new learning session on your calendar.
```json
{
  "action": "create_session",
  "data": {
    "title": "n8n Learning: Phase 1 - Foundations",
    "description": "Core concepts",
    "start_time": "2026-02-01T18:00:00",
    "duration_minutes": 60,
    "phase": "1",
    "topics": ["nodes", "connections"]
  }
}
```

**Response:**
```json
{
  "success": true,
  "event_id": "xxx",
  "title": "...",
  "start": "...",
  "end": "...",
  "link": "https://google.com/calendar/event?...",
  "message": "Learning session scheduled successfully"
}
```

### get_schedule
Returns upcoming n8n learning sessions (next 14 days).
```json
{"action": "get_schedule"}
```

**Response:**
```json
{
  "success": true,
  "upcoming_sessions": [...],
  "count": 2,
  "message": "Found 2 upcoming n8n learning sessions"
}
```

### update_progress
Marks a session as completed with optional notes, OR reschedules the event to a new time.

**Mark as Complete:**
```json
{
  "action": "update_progress",
  "data": {
    "event_id": "your-event-id",
    "notes": "Completed the tutorial"
  }
}
```

**Reschedule Event:**
```json
{
  "action": "update_progress",
  "data": {
    "event_id": "your-event-id",
    "start_time": "2026-02-02T18:00:00-08:00",
    "duration_minutes": 60
  }
}
```

**Response:**
```json
{
  "success": true,
  "event_id": "xxx",
  "title": "...",
  "message": "Progress updated - session marked as completed"
}
```

---

## Workflow Architecture

```
Webhook (POST /claude-calendar)
    │
    ▼
Switch (Route by $json.body.action)
    │
    ├─► get_availability
    │   └─► Get Calendar Events → Process Availability → Respond Availability
    │
    ├─► create_session
    │   └─► Create Calendar Event → Format Create Response → Respond Create
    │
    ├─► get_schedule
    │   └─► Get Learning Sessions → Format Schedule Response → Respond Schedule
    │
    └─► update_progress
        └─► Update Event Progress → Format Update Response → Respond Update
```

---

## Key Learnings

- Webhook data is nested under `body` - always use `$json.body.xxx`
- Google Calendar "Time Min/Max" = "After/Before" in newer n8n versions
- Make sure expression fields are in **Expression mode** (fx toggle)
- Each branch needs: Google Calendar node → Code node → Respond to Webhook node
- ngrok requires free account signup and authtoken configuration
- Use Test URL for development, Production URL when activated

---

## Update Node Expressions (for reschedule)

The **Update Event Progress** node needs these Update Fields for reschedule support:

**Description:**
```
{{ $json.body.data.notes ? 'COMPLETED\n\nNotes: ' + $json.body.data.notes : '' }}
```

**Start:**
```
{{ $json.body.data.start_time ?? '' }}
```

**End:**
```
{{ $json.body.data.start_time && $json.body.data.duration_minutes ? DateTime.fromISO($json.body.data.start_time).plus({ minutes: Number($json.body.data.duration_minutes) }).toISO() : '' }}
```

> **Important:** All fields must be in Expression mode (fx toggle). Empty strings allow the field to be skipped when not provided.

---

## Troubleshooting

### Webhook returns 404
- Make sure workflow is **Active** (toggled on)
- Use Production URL, not Test URL
- Verify n8n is running

### ngrok closes immediately
- Sign up at https://ngrok.com
- Run: `ngrok config add-authtoken YOUR_TOKEN`

### Expression not evaluating
- Click the field and enable **Expression mode** (fx toggle)
- Expression should have highlighted/colored background

### Google Calendar auth fails
- Check credentials in n8n Settings → Credentials
- Re-authenticate if token expired

---

## Files

- `workflows/calendar-scheduler.json` - Workflow export
- `PROGRESS.md` - This file
- `README.md` - Setup instructions
