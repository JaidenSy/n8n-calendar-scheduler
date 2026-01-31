# Claude Reference - n8n Learning Schedule Manager

Quick reference for Claude to help manage your n8n learning schedule.

---

## Quick Start Commands

### Start Everything (Two Terminals Needed)

**Terminal 1 - Start n8n:**
```bash
cd C:/Users/Jypsi/Projects/n8n-calendar-scheduler
npm start
```

**Terminal 2 - Start ngrok:**
```bash
ngrok http 5678
```

After ngrok starts, copy the `https://xxxxx.ngrok-free.app` URL.

**Webhook URL:** `[NGROK_URL]/webhook/claude-calendar`

---

## Learning Path Progress Tracker

### Phase 1: Foundations (Week 1-2)

**Core Concepts:**
- [ ] Understand nodes, connections, and data flow
- [ ] Learn trigger types (webhook, schedule, manual, app triggers)
- [ ] Master data manipulation (Set, Edit Fields, Merge, Split)
- [ ] Practice error handling and workflow settings

**First Projects:**
- [ ] Hello World - Webhook → Respond with JSON
- [ ] Daily Digest - Schedule → Fetch RSS → Email summary
- [ ] Form Handler - Webhook → Validate → Store in Google Sheets

**Resources:**
- [n8n Beginner Course](https://docs.n8n.io/courses/level-one/)
- [Node Overview](https://docs.n8n.io/integrations/builtin/core-nodes/)

---

### Phase 2: AI Integration (Week 3-4)

**AI Nodes to Master:**
- [ ] OpenAI node (Chat, Embeddings, Image generation)
- [ ] AI Agent node (tool-calling workflows)
- [ ] Vector Store nodes (Pinecone, Supabase, Qdrant)
- [ ] Text chunking and document loaders

**AI Projects:**
- [ ] AI Classifier - Webhook → OpenAI classify → Route to different outputs
- [ ] RAG Chatbot - Load docs → Embed → Store → Query with context
- [ ] Content Generator - Trigger → AI write → Human approval → Publish

**Resources:**
- [n8n AI Documentation](https://docs.n8n.io/integrations/builtin/cluster-nodes/root-nodes/n8n-nodes-langchain.agent/)
- [Building AI Agents](https://docs.n8n.io/courses/level-two/)

---

### Phase 3: Advanced Patterns (Week 5-6)

**Techniques:**
- [ ] Sub-workflows and workflow composition
- [ ] Custom Function nodes (JavaScript)
- [ ] HTTP Request node for any API
- [ ] Looping and batch processing
- [ ] Credentials management and security

**Advanced Projects:**
- [ ] Multi-step Agent - Agent with tools (search, calculate, API calls)
- [ ] Approval Workflow - Request → AI process → Human approve → Execute
- [ ] Data Pipeline - Extract → Transform → Load with error recovery

---

### Phase 4: Production & Business (Week 7-8)

**Production Skills:**
- [ ] Self-hosting n8n (Docker, cloud deployment)
- [ ] Monitoring and logging
- [ ] Backup and version control workflows
- [ ] Performance optimization

**Business Skills:**
- [ ] Package workflows for clients
- [ ] Documentation and handoff
- [ ] Pricing strategies
- [ ] Support and maintenance plans

---

## Calendar API Reference

**Webhook URL:** `POST [NGROK_URL]/webhook/claude-calendar`

### Actions

| Action | Description |
|--------|-------------|
| `get_availability` | Check calendar for free time (next 7 days) |
| `create_session` | Schedule a new learning session |
| `get_schedule` | View upcoming n8n learning sessions (next 14 days) |
| `update_progress` | Mark session complete OR reschedule |

### Example Payloads

**Check Availability:**
```json
{"action": "get_availability"}
```

**Create Session:**
```json
{
  "action": "create_session",
  "data": {
    "title": "n8n Learning: Phase 1 - Foundations",
    "description": "Topics: nodes, connections, triggers",
    "start_time": "2026-02-01T18:00:00",
    "duration_minutes": 60,
    "phase": "1",
    "topics": ["nodes", "connections", "triggers"]
  }
}
```

**Get Schedule:**
```json
{"action": "get_schedule"}
```

**Mark Complete:**
```json
{
  "action": "update_progress",
  "data": {
    "event_id": "EVENT_ID",
    "notes": "Completed core concepts tutorial"
  }
}
```

**Reschedule:**
```json
{
  "action": "update_progress",
  "data": {
    "event_id": "EVENT_ID",
    "start_time": "2026-02-02T18:00:00-08:00",
    "duration_minutes": 60
  }
}
```

---

## Claude Workflow for Schedule Management

When user asks to manage their n8n learning schedule:

1. **Check if n8n/ngrok are running** - Ask user to start them if needed
2. **Get webhook URL** - User provides current ngrok URL
3. **Check current progress** - Call `get_schedule` to see upcoming sessions
4. **Check learning path** - Reference this file to see what topics are next
5. **Suggest sessions** - Based on progress and availability, suggest scheduling
6. **Create/update calendar** - Use the webhook API to manage events
7. **Update progress tracker** - Mark completed items in this file

### Scheduling Guidelines

- Sessions should be 30-60 minutes
- One topic per session works best
- Leave buffer time between sessions
- Weekend mornings often work well for learning
- Check availability before suggesting times

---

## File Locations

| File | Purpose |
|------|---------|
| `docs/CLAUDE-REFERENCE.md` | This file - main reference |
| `docs/n8n-learning-path.md` | Full learning curriculum |
| `docs/n8n-local-setup.md` | n8n installation & config |
| `PROGRESS.md` | Project status & API docs |
| `README.md` | Quick start guide |
| `workflows/calendar-scheduler.json` | n8n workflow export |

---

## Session Naming Convention

When creating calendar events, use this format:

```
n8n Learning: Phase [X] - [Topic]
```

Examples:
- `n8n Learning: Phase 1 - Core Concepts`
- `n8n Learning: Phase 2 - AI Agent Node`
- `n8n Learning: Phase 3 - Custom Functions`

---

## Phase Completion Tracking

```
[ ] Phase 1 Complete - Date: ___
[ ] Phase 2 Complete - Date: ___
[ ] Phase 3 Complete - Date: ___
[ ] Phase 4 Complete - Date: ___
```
