# AI Marketing Agents

9-Agent AI Marketing Team for n8n. Modular workflows for research, content creation, and distribution.

## Overview

This system provides a team of AI agents that work together to create complete marketing campaigns. Each agent specializes in a specific task and can be used independently or as part of a full campaign workflow.

## Team Roster

| # | Agent | Role | Team |
|---|-------|------|------|
| 1 | Brand Strategist | Market research, positioning, messaging frameworks | Research |
| 2 | SEO Researcher | Keyword research and search intent analysis | Research |
| 3 | Competitive Analyst | Competitor research, gaps, opportunities | Research |
| 4 | Content Planner | Blog outlines and content strategy | Content |
| 5 | Content Writer | Writes blog posts with citations | Content |
| 6 | Content Editor | Edits and polishes content | Content |
| 7 | Social Media Manager | TikTok, LinkedIn, Instagram, Reddit posts | Distribution |
| 8 | SEO & AEO Specialist | Meta tags, schema, search + AI answer optimization | Distribution |
| 9 | Creative Director | Visual guidelines, colors, typography, logo concepts | Distribution |

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│              WORKFLOW 1: CAMPAIGN ORCHESTRATOR                   │
│                      (Chat Trigger)                              │
├─────────────────────────────────────────────────────────────────┤
│  Commands:                                                       │
│  • "New campaign: [description]" → Runs all 3 phases            │
│  • "Research: [description]" → Calls Research workflow only     │
│  • "Content for campaign #5" → Calls Content workflow           │
│  • "Distribute campaign #5" → Calls Distribution workflow       │
│  • "Status of campaign #5" → Checks Sheets                      │
└─────────────────────────────────────────────────────────────────┘
                            │
          ┌─────────────────┼─────────────────┐
          ▼                 ▼                 ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│ WF2: RESEARCH   │ │ WF3: CONTENT    │ │ WF4: DISTRIBUTE │
│ (Sub-workflow)  │ │ (Sub-workflow)  │ │ (Sub-workflow)  │
│                 │ │                 │ │                 │
│ 3 agents        │ │ 3 agents        │ │ 3 agents        │
│ parallel        │ │ sequential      │ │ parallel        │
└─────────────────┘ └─────────────────┘ └─────────────────┘
```

## Workflows

| File | Description | Trigger |
|------|-------------|--------|
| `01-orchestrator.json` | Main router and campaign coordinator | Chat |
| `02-research-team.json` | Brand Strategy + Keywords + Competitor Analysis | Chat/Webhook |
| `03-content-team.json` | Planner → Writer → Editor (sequential) | Chat/Webhook |
| `04-distribution-team.json` | Social + SEO/AEO + Creative (parallel) | Chat/Webhook |

## Quick Start

1. Import workflows into n8n (see [SETUP.md](SETUP.md))
2. Configure credentials
3. Create Google Sheets with required tabs
4. Activate workflows
5. Open Orchestrator chat and type: `New campaign: sustainable coffee brand targeting eco-conscious millennials`

## Usage Examples

**Full Campaign:**
```
New campaign: AI-powered fitness app for busy professionals
```

**Research Only:**
```
Research: competitor analysis for meal kit delivery services
```

**Content for Existing Campaign:**
```
Content for campaign #3
```

**Distribution for Any Blog:**
```
Distribute: [paste blog content or provide campaign ID]
```

## Data Storage

All outputs are saved to Google Sheets:
- **Campaign Overview** - Campaign metadata and status
- **Phase 1 - Research** - Brand strategy, keywords, competitor analysis
- **Phase 2 - Content** - Blog outline, draft, final post
- **Phase 3 - Distribution** - Social posts, SEO metadata, visual guidelines

## Requirements

- n8n instance (self-hosted or cloud)
- Google Gemini API key
- Google Search API key (for web research)
- Google Sheets OAuth2
- Slack OAuth2 (for notifications)

## Documentation

- [SETUP.md](SETUP.md) - Installation and credential configuration
- [ARCHITECTURE.md](ARCHITECTURE.md) - Detailed system design
- [prompts/agent-prompts.md](prompts/agent-prompts.md) - All agent system prompts

## License

MIT
