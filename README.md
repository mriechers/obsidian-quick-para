> [!WARNING]
> **Archived legacy codebase - not maintained, not installable.**
>
> `obsidian-inbox` was an experiment that did not survive. Dropped alongside email-to-para, for the same reason: too much vault noise for too little value.
>
> This branch preserves the original source and its full commit history
> for reference only. It receives no fixes, no releases, and no support,
> and shares no code with Quick PARA.
>
> For working code, see the `main` branch of this repository.

---

# Obsidian Inbox

Route emails from multiple addresses into your Obsidian vault as structured markdown notes — tasks, newsletters, and agent messages each get their own pipeline.

## How It Works

```
Gmail / Outlook / iCloud
        ↓ forwarding rules
email-to-obsidian@domain ──→ Task note       → 0 - INBOX/TASKS FROM EMAIL/
newsletters@domain ────────→ Newsletter note → 0 - INBOX/NEWSLETTERS/
claude@domain ─────────────→ Agent message   → 0 - INBOX/AGENT MESSAGES/
inbox@domain ──────────────→ Auto-detect     → (header-based routing)
        ↓
Cloudflare Email Worker (parse, convert, route)
        ↓
Cloudflare R2 Bucket
        ↓
Remotely Save plugin (existing)
        ↓
Obsidian (Desktop, iOS, Android)
```

The recipient address determines the pipeline. Each address maps to a distinct markdown template, folder, and tag set. The `inbox@` catch-all falls back to header-based detection (e.g., `List-Unsubscribe` → newsletter).

## Email Routes

| Address | Route | Tag | Folder | Description |
|---------|-------|-----|--------|-------------|
| `email-to-obsidian@*` | `task` | `email-task` | `TASKS FROM EMAIL/` | Actionable emails with a task checkbox |
| `newsletters@*` | `newsletter` | `newsletter` | `NEWSLETTERS/` | Newsletter content with layout cleanup |
| `claude@*` | `agent` | `agent-message` | `AGENT MESSAGES/` | Messages for agent processing (`status: pending`) |
| `inbox@*` | `inbox` | auto | auto | Catch-all: detects newsletters via headers, otherwise task |

All routes work across all configured domains (e.g., `newsletters@yourdomain.com`, `newsletters@otherdomain.com`).

## Note Templates

### Task Note (`email-to-obsidian@`)

```markdown
---
tags:
  - all
  - email-task
created: 2026-02-03
from: sender@example.com
subject: Follow up on Q4 budget review
email_id: abc123
source: gmail
---

## Tasks in this note
- [ ] Review and process this email

---
## Email
**From:** Manager Name <manager@example.com>
**Date:** February 3, 2026
**Subject:** Follow up on Q4 budget review

[converted email body]

---
## Notes

```

### Newsletter Note (`newsletters@`)

```markdown
---
tags:
  - newsletter
created: 2026-02-03
from: hello@designweekly.com
newsletter_name: Design Weekly
subject: "Issue #47: What's New in CSS"
email_id: abc123
source: gmail
status: unprocessed
---

## Design Weekly — Issue #47: What's New in CSS

**From:** Design Weekly <hello@designweekly.com>
**Date:** February 3, 2026

---

[cleaned newsletter body — layout tables flattened, trackers stripped]
```

### Agent Message (`claude@`)

```markdown
---
tags:
  - agent-message
created: 2026-02-03
from: sender@example.com
subject: Summarize my meeting notes from today
email_id: abc123
source: gmail
status: pending
---

## Agent Message

**From:** Your Name <you@gmail.com>
**Date:** February 3, 2026

---

[email body]
```

## Features

- **Address-based routing** — recipient address determines the pipeline, no manual tagging needed
- **Newsletter HTML cleanup** — layout tables flattened to paragraphs, tracking pixels stripped, unsubscribe footers removed, CTA buttons converted to links
- **Email source detection** — identifies Gmail, Outlook, and iCloud via headers
- **Attachment handling** — saved to `_attachments/{messageId}/` in R2 with Obsidian wikilinks in the note
- **Deduplication** — checks R2 for existing notes before writing (by filename)
- **Audit forwarding** — every email forwarded to a Gmail address for debugging/backup (before processing)
- **Daily routing report** — cron job queries Cloudflare GraphQL Analytics API for delivery stats across all zones
- **YAML-safe frontmatter** — subjects with special characters are properly escaped

## Prerequisites

- Custom domain(s) with DNS on Cloudflare
- Cloudflare account (free tier works)
- Obsidian with [Remotely Save](https://github.com/remotely-save/remotely-save) plugin
- Email clients configured with forwarding rules

## Setup

### 1. Create R2 Bucket

1. [Cloudflare Dashboard](https://dash.cloudflare.com/) → **R2 Object Storage** → **Create bucket**
2. Name: `obsidian-inbox`

### 2. Enable Email Routing

1. Dashboard → **Email** → **Email Routing** → enable for your domain
2. Create routing rules for each address you want to use:
   - `email-to-obsidian` → Send to Worker
   - `newsletters` → Send to Worker
   - `claude` → Send to Worker
   - `inbox` → Send to Worker (catch-all)

### 3. Deploy

```bash
git clone https://github.com/MarkOnFire/obsidian-inbox.git
cd obsidian-inbox
npm install

# Edit wrangler.toml — update bucket name, folders, domains
# Set the Cloudflare API token for the daily routing report:
npx wrangler secret put CF_API_TOKEN

npx wrangler deploy
```

### 4. Configure Remotely Save

1. Install [Remotely Save](https://github.com/remotely-save/remotely-save) in Obsidian
2. Service: **S3 or S3-compatible**
3. Endpoint: your R2 endpoint (from R2 dashboard → bucket settings)
4. Create an R2 API token (R2 → Manage R2 API Tokens) for Access Key / Secret
5. Bucket: `obsidian-inbox`, Region: `auto`

### 5. Set Up Forwarding Rules

#### Gmail
1. Settings → Forwarding → add `email-to-obsidian@yourdomain.com` and verify
2. Create a filter: `is:starred` → Forward to the address

#### Outlook
1. Settings → Mail → Rules → new rule
2. Condition: **Message is flagged** → Forward to the address

#### Duck.com (newsletters)
Forward newsletters to `newsletters@yourdomain.com` via Duck Address aliases.

## Configuration

`wrangler.toml`:

```toml
[vars]
INBOX_FOLDER = "0 - INBOX"
NEWSLETTER_FOLDER = "0 - INBOX/NEWSLETTERS"
AGENT_FOLDER = "0 - INBOX/AGENT MESSAGES"
FORWARD_TO = "you@gmail.com"       # Audit trail forwarding
CF_ACCOUNT_ID = "your-account-id"
CF_ZONE_IDS = "zone1,zone2"        # For daily routing report
CF_ZONE_NAMES = "domain1.com,domain2.com"

[triggers]
crons = ["0 6 * * *"]  # Daily routing report at 6 AM UTC
```

`CF_API_TOKEN` is stored as a Wrangler secret (not in toml).

## Development

```bash
npm install          # Install dependencies
npm run dev          # Local dev server
npm test             # Run tests (63 tests)
npm run typecheck    # TypeScript check
npm run test:watch   # Watch mode
npm run test:coverage # Coverage report
```

### Branch Strategy

- **`dev`** — local development branch; CI runs test + typecheck on push
- **`main`** — production; CI runs test + typecheck + deploy to Cloudflare on push
- `main` is protected: requires a PR with passing status checks, no direct pushes

### CI/CD

GitHub Actions (`.github/workflows/ci.yml`):

1. **Test & Typecheck** — runs on every push to `dev` or `main`, and on PRs to `main`
2. **Deploy to Cloudflare** — runs only on push to `main` (after tests pass)

Secrets required: `CLOUDFLARE_API_TOKEN`

## Cost

| Component | Free Tier |
|-----------|-----------|
| Cloudflare Email Routing | 100K emails/day |
| Cloudflare Workers | 100K requests/day |
| Cloudflare R2 | 10GB storage, 1M Class A ops/month |
| **Total** | **$0** |

## Limitations

- **One-way sync** — email → Obsidian only. Completing a task in Obsidian doesn't affect the email.
- **No inline images** — attachment filenames are linked but images aren't embedded in the note body.
- **Newsletter cleanup is heuristic** — layout extraction works well for most newsletter templates but edge cases exist.

## Prior Art

Evolved from an OAuth-based Obsidian plugin ([obsidian-email-to-para](https://github.com/MarkOnFire/obsidian-email-to-para) — archived). That approach required Google Cloud Console and Azure Portal setup. This Cloudflare approach is simpler, cheaper, and works on mobile.

## License

MIT
