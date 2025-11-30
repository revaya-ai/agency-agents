# Architecture

## System Design

The AI Marketing Agents system uses a hybrid architecture combining:
- **Orchestrator pattern** for unified user interface
- **Sub-workflow pattern** for modularity
- **Parallel execution** for independent agents
- **Sequential execution** for dependent agents

## Workflow Relationships

```
                    ┌─────────────────────┐
                    │    ORCHESTRATOR     │
                    │   (Chat Interface)  │
                    └──────────┬──────────┘
                               │
         ┌─────────────────────┼─────────────────────┐
         │                     │                     │
         ▼                     ▼                     ▼
┌─────────────────┐   ┌─────────────────┐   ┌─────────────────┐
│  RESEARCH TEAM  │   │  CONTENT TEAM   │   │ DISTRIBUTION    │
│                 │   │                 │   │     TEAM        │
│ ┌─────────────┐ │   │ ┌─────────────┐ │   │ ┌─────────────┐ │
│ │ Strategist  │ │   │ │  Planner    │ │   │ │   Social    │ │
│ └─────────────┘ │   │ └──────┬──────┘ │   │ └─────────────┘ │
│ ┌─────────────┐ │   │        │        │   │ ┌─────────────┐ │
│ │SEO Research │ │   │        ▼        │   │ │  SEO/AEO    │ │
│ └─────────────┘ │   │ ┌─────────────┐ │   │ └─────────────┘ │
│ ┌─────────────┐ │   │ │   Writer    │ │   │ ┌─────────────┐ │
│ │ Competitor  │ │   │ └──────┬──────┘ │   │ │  Creative   │ │
│ └─────────────┘ │   │        │        │   │ └─────────────┘ │
│                 │   │        ▼        │   │                 │
│   (parallel)    │   │ ┌─────────────┐ │   │   (parallel)    │
│                 │   │ │   Editor    │ │   │                 │
│                 │   │ └─────────────┘ │   │                 │
│                 │   │  (sequential)   │   │                 │
└────────┬────────┘   └────────┬────────┘   └────────┬────────┘
         │                     │                     │
         └─────────────────────┼─────────────────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │    GOOGLE SHEETS    │
                    │  (Data Persistence) │
                    └─────────────────────┘
```

## Agent Details

### Research Team (Parallel Execution)

| Agent | Model | Tools | Temperature |
|-------|-------|-------|-------------|
| Brand Strategist | gemini-2.0-flash-exp | Google Search | 0.8 |
| SEO Researcher | gemini-2.0-flash-exp | Google Search | 0.7 |
| Competitive Analyst | gemini-2.0-flash-exp | Google Search | 0.7 |

**Execution:** All 3 agents run simultaneously, results merged after completion.

### Content Team (Sequential Execution)

| Agent | Model | Tools | Temperature |
|-------|-------|-------|-------------|
| Content Planner | gemini-2.0-flash-exp | None | 0.8 |
| Content Writer | gemini-2.0-flash-exp | Google Search | 0.8 |
| Content Editor | gemini-2.0-flash-exp | None | 0.7 |

**Execution:** Planner → Writer → Editor. Each agent receives output from previous agent.

### Distribution Team (Parallel Execution)

| Agent | Model | Tools | Temperature |
|-------|-------|-------|-------------|
| Social Media Manager | gemini-2.0-flash-exp | None | 0.9 |
| SEO & AEO Specialist | gemini-2.0-flash-exp | None | 0.7 |
| Creative Director | gemini-2.0-flash-exp | None | 0.8 |

**Execution:** All 3 agents run simultaneously, results merged after completion.

## Data Flow

### Full Campaign Flow

```
User Input
    │
    ▼
┌─────────────────────────────────────────────────────────────┐
│ PHASE 1: RESEARCH                                           │
│                                                             │
│ Input: Brand description                                    │
│ Output: brand_strategy, keywords, competitor_analysis       │
│ Storage: "Phase 1 - Research" sheet                        │
└─────────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────────┐
│ PHASE 2: CONTENT                                            │
│                                                             │
│ Input: Phase 1 outputs                                      │
│ Output: blog_outline, blog_draft, final_blog               │
│ Storage: "Phase 2 - Content" sheet                         │
└─────────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────────┐
│ PHASE 3: DISTRIBUTION                                       │
│                                                             │
│ Input: Phase 1 + Phase 2 outputs                           │
│ Output: social_posts, seo_metadata, visual_guidelines      │
│ Storage: "Phase 3 - Distribution" sheet                    │
└─────────────────────────────────────────────────────────────┘
    │
    ▼
Campaign Complete
```

### Independent Usage Flow

```
┌─────────────────────────────────────────────────────────────┐
│ RESEARCH ONLY                                               │
│                                                             │
│ Input: "Research: [brand description]"                     │
│ Output: Research results returned to chat + saved to sheet │
│ Use case: Quick brand audit, market research               │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ CONTENT ONLY                                                │
│                                                             │
│ Input: "Content for campaign #5" OR paste research         │
│ Output: Blog post returned to chat + saved to sheet        │
│ Use case: Generate content from existing research          │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ DISTRIBUTION ONLY                                           │
│                                                             │
│ Input: "Distribute campaign #5" OR paste blog content      │
│ Output: Social posts + SEO + visuals returned + saved      │
│ Use case: Repurpose any blog post for social media         │
└─────────────────────────────────────────────────────────────┘
```

## Error Handling

### Retry Logic
- All Google Sheets writes: 3 retries with 1 second delay
- Agent failures: Logged to Slack, workflow stops

### Slack Notifications

Channel: `#marketing-campaigns`

| Event | Message |
|-------|--------|
| Phase failure | 🚨 Phase X Failed - Campaign ID, Error, Timestamp |
| Campaign complete | ✅ Campaign #X Complete - Summary |

### Error Recovery

If a phase fails:
1. Check Slack for error details
2. Fix the issue (usually API quota or credential)
3. Re-run the failed phase using campaign ID
4. Orchestrator will pick up from Sheets data

## Scaling Considerations

### API Limits
- Google Gemini: Monitor quota usage
- Google Search: 100 queries/day (free tier)
- Consider caching for repeated queries

### Parallel Execution
- Phase 1 and 3 run agents in parallel
- n8n handles concurrency automatically
- Monitor for rate limiting with high volume

### Storage
- Google Sheets works for low-medium volume
- For high volume, consider:
  - Airtable
  - Supabase
  - PostgreSQL

## Extension Points

### Adding New Agents

1. Add agent node to appropriate team workflow
2. Connect to merge node (parallel) or chain (sequential)
3. Update Sheets schema if new outputs
4. Update orchestrator routing if new commands needed

### Adding New Channels

To add YouTube, Twitter, etc.:
1. Update Social Media Manager prompt
2. Add columns to Phase 3 - Distribution sheet
3. Update Structure node mappings

### Webhook Access

Each sub-workflow can accept webhook triggers:
1. Add Webhook node alongside Chat Trigger
2. Both connect to same processing chain
3. Enables API access for external integrations
