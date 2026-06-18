# Mails Bridge

Multi-account email access for OpenAI Codex via IMAP/SMTP. Add Gmail, Outlook, Yahoo, iCloud, or any email provider — then search, read, send, and organize emails right from your terminal.

## Setup

### 1. Install the plugin

**From the marketplace:**

```bash
codex plugin marketplace add pavankalyanm/codex-plugins
codex plugin install mails-bridge
```

**Or add directly via CLI:**

```bash
git clone https://github.com/pavankalyanm/codex-plugins.git
codex mcp add mails-bridge -- uv run --directory /path/to/codex-plugins/mails-bridge/server python main.py
```

### 2. Create an App Password

| Provider | How to get an App Password |
|----------|---------------------------|
| **Gmail** | Enable 2FA → [myaccount.google.com/apppasswords](https://myaccount.google.com/apppasswords) → Create for "Mail" |
| **Outlook / Hotmail** | [account.live.com/proofs/AppPassword](https://account.live.com/proofs/AppPassword) |
| **Yahoo** | [login.yahoo.com/account/security/app-passwords](https://login.yahoo.com/account/security/app-passwords) |
| **iCloud** | [appleid.apple.com](https://appleid.apple.com) → Sign-In and Security → App-Specific Passwords |
| **Zoho** | [accounts.zoho.com](https://accounts.zoho.com/home#security/security_apppassword) → Security → App Passwords |
| **Fastmail** | [fastmail.com/settings/security/devicekeys](https://www.fastmail.com/settings/security/devicekeys) |
| **ProtonMail** | Requires [ProtonMail Bridge](https://proton.me/mail/bridge) running locally |
| **Other** | Use your account password with custom IMAP/SMTP servers |

### 3. Add your account

Tell Codex:

```
add my personal email user@gmail.com xxxx xxxx xxxx xxxx
```

The server auto-detects IMAP/SMTP settings from the email domain. For custom providers:

```
add my work email user@company.com password123 with imap server mail.company.com and smtp server smtp.company.com
```

### 4. Verify

```
show my email profile
```

You'll see inbox total, unread count, and the detected provider.

## Commands

### Account management

| Command | What it does |
|---------|-------------|
| `email_add_account(alias, email, password)` | Add an account (auto-detects provider) |
| `email_add_account(alias, email, password, imap_host, imap_port, smtp_host, smtp_port)` | Add with custom servers |
| `email_remove_account(alias)` | Remove an account |
| `email_list_accounts()` | List all accounts and providers |
| `email_supported_providers()` | Show all auto-detected providers |

### Reading email

| Command | What it does |
|---------|-------------|
| `email_search(query, account, max_results, folder)` | Search via IMAP — `"UNSEEN"`, `"FROM \"boss@co.com\""`, `"SUBJECT \"meeting\""` |
| `email_read_message(uid, account, folder)` | Read full message by UID |
| `email_read_thread(subject, account)` | Read all messages in a thread |

### Composing

| Command | What it does |
|---------|-------------|
| `email_create_draft(to, subject, body, account)` | Save a draft |
| `email_send(to, subject, body, account)` | Send an email (always confirms first) |

### Organization

| Command | What it does |
|---------|-------------|
| `email_list_drafts(account)` | List draft emails |
| `email_list_folders(account)` | List all IMAP folders/labels |
| `email_move_message(uid, destination, account)` | Move to another folder |
| `email_mark_read(uid, account)` | Mark as read |
| `email_mark_unread(uid, account)` | Mark as unread |
| `email_star_message(uid, account)` | Star/flag a message |
| `email_get_profile(account)` | Inbox stats |

## Use case: Automated email workflows with Codex

Codex's strength is **automation and scripting**. Mails Bridge turns email into a programmable interface you can wire into your dev workflows.

### CI/CD notification digest

**Prompt:**

> "Check my sde account for all unread emails from GitHub and summarize failed CI runs. If any PR review was requested, show me the PR links."

**What Codex does:**

1. `email_search('UNSEEN FROM "notifications@github.com"', account="sde")`
2. Reads each notification, filters for failed checks and review requests
3. Outputs a structured summary:

```
Failed CI runs:
  - PR #142: fix-auth-flow — Tests failed (3 failures in auth_test.py)
  - PR #156: update-deps — Build timeout

Review requested:
  - PR #148: add-rate-limiting — requested by @sarah
    https://github.com/team/api/pull/148
```

### Deploy notification emails

**Prompt:**

> "After I deploy, send a release email from my work account to team@company.com with the git log since the last tag."

**What Codex does:**

1. Runs `git log $(git describe --tags --abbrev=0)..HEAD --oneline` to get changes
2. Composes the email body with the changelog
3. Calls `email_send()` from your work account (after confirmation)

### Job search automation

**Prompt:**

> "Search my job account for any emails from lever.co, greenhouse.io, or ashbyhq.com in the last 7 days. List them by company with the status (application received, interview invite, rejection)."

**What Codex does:**

1. Runs parallel searches: `email_search('FROM "lever.co" SINCE 10-Jun-2026', account="job")`
2. Reads each message to classify the status
3. Outputs:

```
| Company    | Subject                        | Status              | Date       |
|------------|--------------------------------|---------------------|------------|
| Stripe     | Interview: Technical Screen    | Interview invite    | Jun 15     |
| Anthropic  | Application received           | Applied             | Jun 12     |
| Datadog    | Next steps                     | Interview invite    | Jun 11     |
```

### Batch inbox cleanup

**Prompt:**

> "In my personal account, find all read emails older than 60 days and move them to archive. Show me the count before and after."

**What Codex does:**

1. `email_search('SEEN BEFORE 18-Apr-2026', account="personal")` 
2. Loops through results calling `email_move_message(uid, '"[Gmail]/All Mail"')`
3. Reports: "Archived 47 messages. Inbox: 312 → 265"

### Multi-account monitoring script

**Prompt:**

> "Check all my accounts for unread emails every time I run this. Show a one-line summary per account."

**Codex output:**

```
personal  — 3 unread (latest: "Your order has shipped" from amazon.com)
work      — 12 unread (latest: "Q3 planning" from manager@company.com)
sde       — 1 unread (latest: "PR review requested" from github.com)
job       — 2 unread (latest: "Interview confirmation" from lever.co)
```

## Supported providers

| Provider | Domains | Auto-detected |
|----------|---------|:---:|
| Gmail | gmail.com, googlemail.com | Yes |
| Outlook | outlook.com, hotmail.com, live.com, msn.com | Yes |
| Yahoo | yahoo.com, ymail.com | Yes |
| iCloud | icloud.com, me.com, mac.com | Yes |
| AOL | aol.com | Yes |
| Zoho | zoho.com | Yes |
| Fastmail | fastmail.com | Yes |
| ProtonMail | protonmail.com, proton.me | Yes* |
| Custom | any domain | Pass imap_host/smtp_host |

*ProtonMail requires ProtonMail Bridge running locally.

## Requirements

- Python 3.11+
- [uv](https://docs.astral.sh/uv/) package manager
