> [!WARNING]
> **Archived legacy codebase - not maintained, not installable.**
>
> `obsidian-email-to-para` was an experiment that did not survive. Dropped. It introduced too much noise into the vault and was overbuilt for the value it delivered.
>
> This branch preserves the original source and its full commit history
> for reference only. It receives no fixes, no releases, and no support,
> and shares no code with Quick PARA.
>
> For working code, see the `main` branch of this repository.

---

# Email to PARA Sync Plugin

> ⚠️ **DEPRECATED:** This OAuth-based Obsidian plugin approach has been superseded by a simpler Cloudflare Email Worker architecture. See [cloudflare-email-to-obsidian](https://github.com/mriechers/cloudflare-email-to-obsidian) for the new approach, which eliminates OAuth complexity by using email forwarding rules instead.
>
> This repository is archived for reference only.

---

Automatically sync starred Gmail and flagged Outlook emails as notes in your Obsidian PARA inbox.

## Overview

Stop losing important emails in your inbox. When you star an email in Gmail or flag an email in Outlook, this plugin automatically creates a fully-formatted markdown note in your Obsidian vault with the email content, metadata, and a link back to the original message.

**Perfect for:**
- Converting email tasks into your PARA workflow
- Capturing important information from emails
- Processing email action items in Obsidian
- Integrating email with your personal knowledge management system

## Features

- **Automatic Sync:** Checks for new starred/flagged emails every 30 minutes
- **Manual Sync:** Click ribbon icon for immediate sync
- **Full Email Content:** HTML emails converted to clean markdown
- **Metadata Preservation:** Subject, sender, date, and link to original
- **PARA Integration:** Notes created in inbox folder, auto-tagged
- **Deduplication:** Never creates duplicate notes from the same email
- **Secure Authentication:** OAuth 2.0 with read-only access
- **Bi-Provider Support:** Works with both Gmail and Outlook

## Requirements

- Obsidian v0.15.0 or higher
- Gmail account (optional, if you want Gmail sync)
- Outlook/Microsoft 365 account (optional, if you want Outlook sync)
- Internet connection for OAuth setup and syncing

## Installation

### 1. Install Plugin Files

Copy the plugin to your vault:

```bash
cp -r custom-extensions/plugins/email-to-para-sync \
  "/path/to/your/vault/.obsidian/plugins/"
```

Or manually copy the folder to:
```
YourVault/.obsidian/plugins/email-to-para-sync/
```

### 2. Enable Plugin in Obsidian

1. Open Obsidian Settings
2. Go to **Community Plugins**
3. Click **Reload** to detect the new plugin
4. Find **Email to PARA Sync** in the list
5. Toggle it **ON**

### 3. Connect Email Accounts

See **Setup Guide** section below for detailed OAuth configuration.

## Setup Guide

### Gmail Setup

#### Step 1: Create OAuth Application

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project (or select existing):
   - Click **Select a project** → **New Project**
   - Name: "Obsidian Email Sync"
   - Click **Create**

3. Enable Gmail API:
   - Go to **APIs & Services** → **Library**
   - Search for "Gmail API"
   - Click **Enable**

4. Create OAuth credentials:
   - Go to **APIs & Services** → **Credentials**
   - Click **Create Credentials** → **OAuth client ID**
   - If prompted, configure consent screen:
     - User type: **External**
     - App name: "Obsidian Email Sync"
     - User support email: Your email
     - Developer contact: Your email
     - Click **Save and Continue**
     - Scopes: Skip for now (we'll add via code)
     - Test users: Add your Gmail address
     - Click **Save and Continue**

5. Create OAuth client:
   - Application type: **Desktop app**
   - Name: "Obsidian Desktop"
   - Click **Create**
   - **Download JSON** (save for later)
   - Note your **Client ID** and **Client Secret**

#### Step 2: Connect Gmail in Plugin

1. Open Obsidian Settings → **Email to PARA Sync**
2. Under **Gmail** section:
   - Toggle **Enable Gmail sync**
   - Click **Connect Gmail**
3. Browser will open for OAuth authorization
4. Sign in to your Google account
5. Grant permissions (read-only access to Gmail)
6. You'll be redirected back to plugin
7. Verify connection: Status should show "✓ Connected as your@gmail.com"

### Outlook Setup

#### Step 1: Register Azure AD Application

1. Go to [Azure Portal](https://portal.azure.com/)
2. Navigate to **Azure Active Directory** → **App registrations**
3. Click **New registration**:
   - Name: "Obsidian Email Sync"
   - Supported account types: **Accounts in any organizational directory and personal Microsoft accounts**
   - Redirect URI:
     - Type: **Public client/native (mobile & desktop)**
     - URI: `http://localhost:42813/callback` (or custom port)
   - Click **Register**

4. Note your **Application (client) ID** from Overview page

5. Configure API permissions:
   - Go to **API permissions**
   - Click **Add a permission**
   - Select **Microsoft Graph**
   - Choose **Delegated permissions**
   - Search and select: `Mail.Read`
   - Click **Add permissions**
   - **No admin consent needed** for Mail.Read

6. Configure authentication:
   - Go to **Authentication**
   - Under **Advanced settings**, enable:
     - ✓ Allow public client flows
   - Click **Save**

#### Step 2: Connect Outlook in Plugin

1. Open Obsidian Settings → **Email to PARA Sync**
2. Under **Outlook** section:
   - Toggle **Enable Outlook sync**
   - Click **Connect Outlook**
3. Browser will open for Microsoft OAuth
4. Sign in to your Microsoft account
5. Grant permissions (read emails)
6. You'll be redirected back to plugin
7. Verify connection: Status should show "✓ Connected as your@outlook.com"

## Usage

### Basic Workflow

1. **Receive important email** in Gmail or Outlook
2. **Star the email** (Gmail) or **Flag the email** (Outlook)
3. **Wait up to 30 minutes** for automatic sync (or click ribbon icon for immediate sync)
4. **Find new note** in your `0 - INBOX/` folder
5. **Process the note** like any inbox item:
   - Add tasks using Obsidian Tasks syntax
   - Add your notes and thoughts
   - Move to appropriate PARA folder when ready

### Manual Sync

For urgent emails that need immediate syncing:

1. Star/flag the email
2. Click the **ribbon icon** (envelope with sync arrows) in Obsidian
3. Plugin immediately checks both email accounts
4. New notes appear within seconds

### Note Format

Each synced email creates a note like this:

**Filename:** `2025-11-13 - Follow up on Q4 budget review.md`

**Location:** `0 - INBOX/`

**Content:**
```markdown
---
tags: [all, para/inbox, email-task]
created: 2025-11-13
email-source: gmail
email-id: "msg_abc123"
email-from: "sender@example.com"
email-subject: "Follow up on Q4 budget review"
email-date: 2025-11-13
email-link: "https://mail.google.com/mail/u/0/#inbox/msg_abc123"
synced: 2025-11-13T10:30:00Z
---

# Subject: Follow up on Q4 budget review

**From:** Manager Name <manager@example.com>
**Date:** November 13, 2025 10:45 AM
**Source:** [View in Gmail](https://mail.google.com/mail/u/0/#inbox/msg_abc123)

---

## Email Content

Hi Mark,

Can you review the **Q4 budget** projections and send feedback by Friday?

Key areas to focus on:
- Marketing spend
- Engineering headcount
- Infrastructure costs

Thanks!

---

## Tasks

- [ ] Review Q4 budget projections 📅 2025-11-15

## Notes

[Your notes here]
```

### What Happens When You Un-Star/Un-Flag?

**Nothing.** Once a note is created in Obsidian, it's independent of the email flag status. You can:
- Un-star the email in Gmail → note remains in Obsidian
- Delete the email → note remains in Obsidian
- Archive the note → email remains starred

This is intentional: once you've imported an email as a task, you manage it in Obsidian.

## Settings

### Account Management

**Gmail:**
- Enable/disable Gmail sync
- Connection status
- Connected email address
- Disconnect/reconnect buttons

**Outlook:**
- Enable/disable Outlook sync
- Connection status
- Connected email address
- Disconnect/reconnect buttons

### Sync Settings

**Sync Interval:** 30 minutes (default)
- How often plugin checks for new emails
- Options: 15, 30, 60 minutes
- Lower = more frequent sync, higher API usage

**Inbox Folder:** `0 - INBOX` (default)
- Where notes are created
- Must exist in your vault
- Plugin will create if missing

**Include Email Body:** ON (default)
- Include full email content in notes
- If off, only metadata + link saved

**Auto-Sync:** ON (default)
- Automatically sync on interval
- If off, only manual sync via ribbon

## Troubleshooting

### "Not connected" even after OAuth flow

**Solution:**
1. Check browser didn't block popup
2. Try connecting again
3. Verify OAuth app is configured correctly (see Setup Guide)
4. Check Obsidian console for errors (Ctrl/Cmd + Shift + I)

### "Token expired" error

**Solution:**
- Plugin should auto-refresh tokens
- If persists, disconnect and reconnect account
- Check OAuth app refresh token settings

### No new notes appearing after starring email

**Checklist:**
1. Is auto-sync enabled in settings?
2. Is the email account connected and enabled?
3. Have you waited 30 minutes (or clicked manual sync)?
4. Does the `0 - INBOX/` folder exist?
5. Check sync state file: `.obsidian/plugins/email-to-para-sync/.sync-state.json`

### Duplicate notes created

**Possible causes:**
- Sync state file was deleted
- Email ID changed (very rare)
- Multiple plugin instances running

**Solution:**
1. Check `.sync-state.json` for email ID
2. Manually delete duplicate notes
3. Restart Obsidian

### OAuth redirect doesn't work

**Solution:**
1. Verify redirect URI in OAuth app matches plugin:
   - Gmail: Check Google Cloud Console credentials
   - Outlook: Check Azure app authentication settings
2. Default is `http://localhost:42813/callback`
3. Make sure port isn't blocked by firewall

### Email body not converting properly

**Issues:**
- Weird formatting
- Missing images
- Broken tables

**Solutions:**
1. Check original email in web client
2. Complex HTML may not convert perfectly
3. Use "View in Gmail/Outlook" link for full formatting
4. Report issue with example email (redacted)

### Large inbox causing slow sync

**Solution:**
1. Only star/flag emails you need in Obsidian
2. Un-star old emails (notes remain in Obsidian)
3. Plugin only syncs starred/flagged, not entire inbox

## Privacy & Security

### What data is accessed?

**Gmail:**
- Read-only access to Gmail messages
- Only fetches starred emails
- Cannot send, delete, or modify emails

**Outlook:**
- Read-only access to email messages
- Only fetches flagged emails
- Cannot send, delete, or modify emails

### Where are tokens stored?

**Multi-tier security approach:**

**Access Tokens:**
- Kept in memory only during plugin session
- Never written to disk
- Automatically refreshed when expired

**Refresh Tokens:**
- **Best security (recommended):** Stored in OS keychain
  - macOS: Keychain Access
  - Windows: Credential Manager
  - Linux: libsecret/GNOME Keyring
- **Fallback:** If keychain unavailable, encrypted with AES-256-GCM
  - You'll be prompted for a vault-specific passphrase
  - Only ciphertext + nonce stored in `data.json`
- **Last resort:** If you decline passphrase, auto-sync disabled (manual sync only)

**What's in data.json:**
- Keychain identifiers (not actual tokens)
- Or encrypted ciphertext (if using passphrase fallback)
- Never plaintext tokens
- Safe to sync `.obsidian/` to Git/iCloud (only encrypted data)

**Important:** OAuth client IDs/secrets should remain private (don't commit to public repos)

### Can the plugin access all my emails?

**No.** The plugin only:
- Queries for starred (Gmail) or flagged (Outlook) emails
- Ignores all other emails
- Has read-only permissions

### What if I revoke access?

1. Plugin will show "Not connected" status
2. Auto-sync will stop
3. Existing notes remain in Obsidian (unaffected)
4. Reconnect account to resume syncing

### How to revoke access?

**Gmail:**
1. Go to [Google Account Security](https://myaccount.google.com/permissions)
2. Find "Obsidian Email Sync"
3. Click **Remove access**

**Outlook:**
1. Go to [Microsoft Account Permissions](https://account.microsoft.com/privacy/app-access)
2. Find "Obsidian Email Sync"
3. Click **Remove**

## Uninstallation

### To temporarily disable:

1. Open Obsidian Settings → Community Plugins
2. Toggle **Email to PARA Sync** OFF

### To completely remove:

1. Disable plugin (see above)
2. Delete plugin folder:
   ```bash
   rm -rf "/path/to/vault/.obsidian/plugins/email-to-para-sync"
   ```
3. Existing synced notes remain in your vault (safe to keep or delete)

### To revoke email access:

See **Privacy & Security** → **How to revoke access**

## FAQ

### Does this work on mobile?

**Not yet.** OAuth flows require browser redirects which are complex on mobile. Desktop only for v1.0. Mobile support planned for v2.0.

### Can I sync multiple Gmail accounts?

**Not in v1.0.** One Gmail account and one Outlook account. Multi-account support planned for v2.0.

### What about other email providers (iCloud, ProtonMail)?

**Not yet.** Currently Gmail and Outlook only. Other providers may be added in future versions based on demand.

### Can I customize the note template?

**Not in v1.0.** Note format is fixed. Template customization planned for v1.1.

### What happens to email attachments?

**Not downloaded.** Attachments remain in email only. Use the "View in Gmail/Outlook" link to access them. Attachment downloads planned for v2.0.

### Can I sync emails to a project folder instead of inbox?

**Not automatically.** All emails go to inbox folder. You manually move notes to appropriate PARA folders after processing. Smart folder routing planned for v2.0.

### Does un-starring delete the note?

**No.** Once created, notes are independent. Un-starring the email doesn't affect the Obsidian note.

### Can I mark email as done from Obsidian?

**Not yet.** No bi-directional sync in v1.0. Marking note as done in Obsidian doesn't un-star the email. Bi-directional sync planned for v2.0.

## Advanced

### Sync State File

Plugin tracks synced emails in:
```
.obsidian/plugins/email-to-para-sync/.sync-state.json
```

**Format:**
```json
{
  "version": "1.0.0",
  "lastSync": "2025-11-13T10:30:00Z",
  "gmail": {
    "historyId": "123456",
    "retryAfter": null
  },
  "outlook": {
    "deltaToken": null,
    "retryAfter": null
  },
  "syncedEmails": {
    "gmail": {
      "msg_id_1": {"processedAt": "2025-11-13T10:30:00Z"},
      "msg_id_2": {"processedAt": "2025-11-13T10:35:00Z"}
    },
    "outlook": {
      "msg_id_3": {"processedAt": "2025-11-13T10:40:00Z"}
    }
  }
}
```

**Features:**
- **Incremental sync:** `historyId` (Gmail) allows fetching only new/changed starred emails
- **Timestamps:** `processedAt` tracks when each email was synced
- **Auto-pruning:** Entries older than 90 days automatically removed
- **Rate limiting:** `retryAfter` prevents API hammering during throttling

**If corrupted or missing:**
- Plugin recreates automatically
- May cause duplicate notes for previously synced emails
- Use "Reset sync state" in settings to rebuild safely

### Manual Sync Trigger

You can trigger sync programmatically:

```javascript
// In Obsidian developer console (Ctrl/Cmd + Shift + I)
app.plugins.plugins['email-to-para-sync'].syncEmails()
```

### Recovery Commands

**Re-import Specific Email:**
1. Open Command Palette (Ctrl/Cmd + P)
2. Search for "Email to PARA: Re-import email"
3. Enter the email's message ID (from frontmatter `email-id` field)
4. Email removed from sync state and re-processed immediately

**Use case:** Email was imported incorrectly, want to re-sync with latest content

**Reset Sync State:**
1. Open plugin settings
2. Click "Reset sync state" button
3. Confirm warning dialog
4. State cleared, plugin fetches only last 20 starred/flagged emails

**Use case:** Vault migration, want to re-import recent emails without downloading entire history

### Debug Mode

Enable verbose logging:

1. Open Obsidian developer console (Ctrl/Cmd + Shift + I)
2. Run:
   ```javascript
   app.plugins.plugins['email-to-para-sync'].settings.debugMode = true
   ```
3. Sync activity logged to console
4. Disable: set `debugMode = false`

## Support

### Report Bugs

1. Check **Troubleshooting** section first
2. Enable debug mode (see Advanced section)
3. Reproduce issue
4. Copy console logs
5. Create issue in repository with:
   - Obsidian version
   - Plugin version
   - Steps to reproduce
   - Console logs (redact sensitive info)

### Feature Requests

See **Future Enhancements** in `SUMMARY.md` for planned features. For new requests:

1. Check if already planned
2. Create issue describing use case
3. Explain why current features don't meet need

## Changelog

### v1.0.0 (Planned)

**Initial release:**
- Gmail OAuth authentication
- Outlook OAuth authentication
- Sync starred/flagged emails every 30 minutes
- HTML to markdown conversion
- Note creation in inbox folder
- Manual sync via ribbon icon
- Deduplication via state file
- Settings UI for account management

## License

MIT License - See repository for details

## Credits

**Author:** Mark Riechers

**Built with:**
- Obsidian Plugin API
- Gmail API v1
- Microsoft Graph API
- TurndownJS (HTML to markdown conversion)

**Inspired by:**
- PARA Method by Tiago Forte
- "Getting Things Done" by David Allen
- Obsidian community plugins

## Related Documentation

- `DESIGN.md` - Technical specification and architecture
- `CODE_REVIEW_REQUEST.md` - Implementation questions and decisions
- `SUMMARY.md` - High-level overview and feature summary
