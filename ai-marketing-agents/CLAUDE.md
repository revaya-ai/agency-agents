# CLAUDE.md

This file provides context for Claude Code when working on this project.

## Project Overview

9-Agent AI Marketing Team for n8n. Modular workflow system with 4 workflows that can run independently or as a coordinated campaign.

## Architecture

```
Orchestrator (Chat Interface)
       |
       +-- Research Team (3 agents, parallel)
       +-- Content Team (3 agents, sequential)
       +-- Distribution Team (3 agents, parallel)
       |
       v
  Google Sheets (data persistence)
```

## Workflow Files

| File | Purpose | Trigger |
|------|---------|--------|
| `workflows/01-orchestrator.json` | Main router, coordinates teams | Chat |
| `workflows/02-research-team.json` | Brand Strategy + Keywords + Competitor | Chat/Webhook |
| `workflows/03-content-team.json` | Planner → Writer → Editor | Chat/Webhook |
| `workflows/04-distribution-team.json` | Social + SEO/AEO + Creative | Chat/Webhook |

## Key Configuration

### Google Sheets ID
```
1kv0ZJZOY-n8CX6Az3N8fkPXmpSZbl3i84f7MFMz3X7E
```

### Sheet Tabs
- Campaign Overview
- Phase 1 - Research
- Phase 2 - Content
- Phase 3 - Distribution

### Slack Channel
```
#marketing-campaigns
```

### AI Model
```
gemini-2.0-flash-exp
```

## Credential Placeholders

All workflows use placeholder credential IDs that must be replaced after import:

- `Google Gemini API` - Gemini API key
- `Google Search API` - Custom Search API
- `Google Sheets OAuth2` - Sheets access
- `Slack OAuth2` - Notifications

Search for `CREDENTIAL_ID` in workflow files to find all placeholders.

## Workflow ID Placeholders

The orchestrator references sub-workflows by ID. After importing, update:

- `RESEARCH_WORKFLOW_ID`
- `CONTENT_WORKFLOW_ID`
- `DISTRIBUTION_WORKFLOW_ID`

## Agent Configuration

| Agent | Temperature | Tools |
|-------|-------------|-------|
| Brand Strategist | 0.8 | Google Search |
| SEO Researcher | 0.7 | Google Search |
| Competitive Analyst | 0.7 | Google Search |
| Content Planner | 0.8 | None |
| Content Writer | 0.8 | Google Search |
| Content Editor | 0.7 | None |
| Social Media Manager | 0.9 | None |
| SEO & AEO Specialist | 0.7 | None |
| Creative Director | 0.8 | None |

## Common Tasks

### Modify Agent Prompts
Prompts are in the `systemMessage` field of each agent node. Reference: `prompts/agent-prompts.md`

### Add New Output Fields
1. Add column to appropriate Google Sheet tab
2. Update the `Structure Output` Set node
3. Update the `Write to Sheets` node column mapping

### Change AI Model
Update the `model` parameter in Gemini nodes. Current: `gemini-2.0-flash-exp`

### Add Error Handling
All workflows have:
- 3 retries on Sheets writes
- Slack notification on failure
- Error check after write operations

## JSON Structure Notes

n8n workflow JSON requires:
- Unique node `id` fields
- Proper `connections` object mapping node names
- `typeVersion` matching n8n version
- Credentials referenced by placeholder ID

## Testing

1. Import sub-workflows first (02, 03, 04)
2. Note their workflow IDs
3. Update orchestrator with correct IDs
4. Import orchestrator (01)
5. Configure all credentials
6. Activate sub-workflows
7. Activate orchestrator
8. Test with: `Help` command in chat

## File Naming Convention

- Workflows: `NN-name.json` (NN = order)
- Documentation: `UPPERCASE.md`
- Prompts: `prompts/*.md`
