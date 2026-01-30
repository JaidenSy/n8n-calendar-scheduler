# n8n Calendar Scheduler - Progress Notes

**Last Updated:** January 29, 2026

## Current Status: Branch 1 Complete

### Completed
- [x] Webhook node configured (POST, path: `claude-calendar`)
- [x] Switch node with 4 routing rules
  - **Important:** Uses `{{ $json.body.action }}` (not `$json.action`)
- [x] Branch 1 (get_availability):
  - [x] Google Calendar node (Get Many, After/Before filters)
  - [x] Code node (processes busy slots)
  - [x] Respond to Webhook node
- [x] Tested successfully - returns 200

### Remaining Branches to Build

**Branch 2: create_session**
1. Google Calendar node (Create operation)
   - Start: `{{ $json.body.data.start_time }}`
   - End: `{{ DateTime.fromISO($json.body.data.start_time).plus({ minutes: $json.body.data.duration_minutes || 60 }).toISO() }}`
   - Title: `{{ $json.body.data.title }}`
2. Respond to Webhook node

**Branch 3: get_schedule**
1. Google Calendar node (Get Many)
   - Query: `n8n Learning`
   - After/Before: next 14 days
2. Respond to Webhook node

**Branch 4: update_progress**
1. Google Calendar node (Update operation)
   - Event ID: `{{ $json.body.data.event_id }}`
   - Description: `COMPLETED`
2. Respond to Webhook node

### After Workflow Complete
- [ ] Activate workflow (toggle on)
- [ ] Set up ngrok for public webhook URL
- [ ] Share webhook URL with Claude Code
- [ ] Test all 4 actions

### Key Learning
- Webhook data is nested under `body` - always use `$json.body.xxx`
- Google Calendar "Time Min/Max" = "After/Before" in newer n8n versions

## How to Resume
1. Start n8n: `npm start` in this directory
2. Open http://localhost:5678
3. Open "Claude Calendar Scheduler" workflow
4. Continue building branches 2, 3, 4 from the Switch node
