# Setup Guide

## Prerequisites

- n8n instance (v1.0+)
- Google Cloud Project with APIs enabled
- Slack workspace (optional, for notifications)

## Step 1: Import Workflows

1. Download all JSON files from `workflows/` folder
2. In n8n, go to **Workflows** → **Import from File**
3. Import in this order:
   - `02-research-team.json`
   - `03-content-team.json`
   - `04-distribution-team.json`
   - `01-orchestrator.json` (import last)

## Step 2: Configure Google Sheets

### Create Spreadsheet

Use this template spreadsheet or create your own:

**Spreadsheet ID:** `1kv0ZJZOY-n8CX6Az3N8fkPXmpSZbl3i84f7MFMz3X7E`

### Required Sheets (Tabs)

Create 4 tabs with these exact names:

**Tab 1: Campaign Overview**
| Column | Type |
|--------|------|
| campaign_id | Number |
| timestamp | DateTime |
| user_input | Text |
| status | Text |
| blog_url | URL |
| last_updated | DateTime |
| notes | Text |

**Tab 2: Phase 1 - Research**
| Column | Type |
|--------|------|
| campaign_id | Number |
| timestamp | DateTime |
| brand_strategy | Text |
| keywords | Text |
| competitor_analysis | Text |
| research_notes | Text |

**Tab 3: Phase 2 - Content**
| Column | Type |
|--------|------|
| campaign_id | Number |
| timestamp | DateTime |
| blog_outline | Text |
| blog_draft | Text |
| final_blog | Text |
| word_count | Number |
| content_notes | Text |

**Tab 4: Phase 3 - Distribution**
| Column | Type |
|--------|------|
| campaign_id | Number |
| timestamp | DateTime |
| tiktok_script | Text |
| linkedin_post | Text |
| instagram_caption | Text |
| reddit_post | Text |
| meta_title | Text |
| meta_description | Text |
| image_alt_texts | Text |
| internal_links | Text |
| schema_markup | Text |
| featured_image_concept | Text |
| color_palette | Text |
| typography | Text |
| visual_style | Text |
| logo_concept | Text |
| distribution_notes | Text |

## Step 3: Configure Credentials

### Google Gemini API

1. Go to [Google AI Studio](https://makersuite.google.com/app/apikey)
2. Create API key
3. In n8n: **Credentials** → **New** → **Google Gemini API**
4. Paste API key
5. Name it: `Google Gemini API`

### Google Search API

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Enable **Custom Search API**
3. Create credentials (API Key)
4. Set up [Programmable Search Engine](https://programmablesearchengine.google.com/)
5. In n8n: **Credentials** → **New** → **Google Search API**
6. Name it: `Google Search API`

### Google Sheets OAuth2

1. In Google Cloud Console, enable **Google Sheets API**
2. Create OAuth 2.0 credentials
3. In n8n: **Credentials** → **New** → **Google Sheets OAuth2**
4. Follow OAuth flow
5. Name it: `Google Sheets OAuth2`

### Slack OAuth2 (Optional)

1. Go to [Slack API](https://api.slack.com/apps)
2. Create new app
3. Add OAuth scopes: `chat:write`, `channels:read`
4. Install to workspace
5. In n8n: **Credentials** → **New** → **Slack OAuth2**
6. Name it: `Slack OAuth2`

## Step 4: Update Workflow References

After importing, update these in each workflow:

### Google Sheets Document ID

Replace placeholder ID with your spreadsheet ID in all Google Sheets nodes.

Find/replace: `1kv0ZJZOY-n8CX6Az3N8fkPXmpSZbl3i84f7MFMz3X7E`

### Slack Channel

Update channel name in Slack nodes if different from `#marketing-campaigns`

### Sub-workflow IDs (Orchestrator only)

After importing sub-workflows, note their IDs and update the Execute Workflow nodes in `01-orchestrator.json`.

## Step 5: Activate Workflows

1. Activate sub-workflows first:
   - `02-research-team`
   - `03-content-team`
   - `04-distribution-team`
2. Activate orchestrator last:
   - `01-orchestrator`

## Step 6: Test

1. Open Orchestrator chat interface
2. Type: `Research: test brand for coffee shop`
3. Verify output appears in chat and Google Sheets

## Troubleshooting

**"Credential not found" error:**
- Ensure credential names match exactly
- Re-authenticate OAuth credentials

**Google Sheets write fails:**
- Check sheet tab names match exactly (case-sensitive)
- Verify OAuth has edit permissions

**Agents not responding:**
- Check Gemini API quota
- Verify API key is valid

**Sub-workflow not found:**
- Ensure sub-workflows are activated before orchestrator
- Update workflow IDs in orchestrator Execute Workflow nodes
