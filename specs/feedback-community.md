# Feedback & Community

In-app feature allowing users to submit bugs and enhancement proposals, vote on items, and interact with the platform admin. Linked to GitHub issues for development tracking.

---

## Access

| Role | Capabilities |
|---|---|
| `member` / `admin` (org users) | Submit, view all, upvote, see admin responses |
| `platform_admin` | All of the above + manage statuses, post responses, push to GitHub |

Accessible at `/feedback` — visible in the dashboard sidebar for all authenticated users.

---

## User-Facing Board (`/feedback`)

### Feedback list

Sorted by votes descending by default. Filterable by type and status.

Each item displays:
- Type badge: `Bug` | `Enhancement`
- Title
- Vote count + upvote button (one vote per user; togglable)
- Status badge: `Open` | `Planned` | `In Progress` | `Done` | `Declined`
- Admin response excerpt (if present)
- GitHub issue link (if linked, shown as external link icon)

### Submit form

Fields:
- **Type** — `Bug` | `Enhancement` (radio)
- **Title** — short, required (max 120 chars)
- **Description** — what happened / what you'd like, required (max 1000 chars)

One submission per user per day (soft limit — not a hard block, just a nudge). No captcha.

### Item detail view

Expanded view on click:
- Full description
- Admin response (full text, with admin name and date)
- GitHub issue link (if linked)
- Vote button
- Submitter: "by [org name]" (no individual name exposed)

---

## Admin View (`/admin/feedback`)

### Ranked list

Sorted by votes descending. Filterable by: type, status, date range.

Each row shows:
- Title, type, vote count, status, submitted by (org name + plan), created date
- GitHub issue URL (if linked)
- Action buttons: **Respond** | **Push to GitHub** | **Change status**

### Status management

Admin can change status to any value:
- `Open` → default on submission
- `Planned` → admin confirmed it will be worked on
- `In Progress` → actively being developed
- `Done` → shipped (optionally link to changelog or release)
- `Declined` → won't fix / out of scope (admin response required)

Status change is visible immediately to all users on the board.

### Admin response

Free-text response from the platform admin. Displayed on the item detail view with a visual "Team response" highlight. Editable after posting. Not required for most statuses, required for `Declined`.

### Push to GitHub

One-click action that creates a GitHub issue via the GitHub API.

**Issue format:**

```
Title: [Bug] / [Enhancement] <feedback title>

Labels: community, bug / enhancement

Body:
> Submitted via Trustixy in-app feedback
> Votes: 42 | Plan: Pro | Status: Planned

## Description
<user description>

## Admin notes
<admin response if present>

---
_Linked from: trustixy.com/feedback/<id>_
```

On success: stores `github_issue_number` and `github_issue_url` in the feedback row. Button becomes a link to the issue. Subsequent clicks open the issue — no duplicate creation.

Works on private and public repos. Requires `GITHUB_TOKEN` env var with `issues: write` scope on the target repo.

---

## Community Social Link

Platform admin can set a single community URL (Discord server, Telegram group, Slack invite, etc.) from `/admin` settings.

**Behavior:**
- If `platform_settings.community_social_url` is null or empty → link is completely hidden from the dashboard
- If set → a "Join our community" link appears in the dashboard sidebar (below the feedback link) and on the `/feedback` page header

**Editable from:** `/admin` → Settings → Community  
Field label: "Community link (Discord, Telegram, etc.)" — plain URL input, saved on blur.

No validation beyond basic URL format check. Admin responsibility to keep it valid.

---

## Data Model

### `feedback`

| Column | Type | Notes |
|---|---|---|
| id | uuid | PK |
| organization_id | uuid | org that submitted |
| created_by | uuid | user id |
| type | text | `bug` \| `enhancement` |
| title | text | max 120 chars |
| description | text | max 1000 chars |
| status | text | `open` \| `planned` \| `in_progress` \| `done` \| `declined` |
| admin_response | text | nullable |
| admin_responded_at | timestamp | nullable |
| github_issue_number | integer | nullable |
| github_issue_url | text | nullable |
| vote_count | integer | denormalized counter, default 0 |
| created_at | timestamp | |
| updated_at | timestamp | |

### `feedback_votes`

| Column | Type | Notes |
|---|---|---|
| id | uuid | PK |
| feedback_id | uuid | FK → feedback |
| user_id | uuid | FK → users |
| created_at | timestamp | |

Unique constraint: `(feedback_id, user_id)` — one vote per user per item.

### `platform_settings`

Single-row table (id = fixed constant `'singleton'`). Upserted, never deleted.

| Column | Type | Notes |
|---|---|---|
| id | text | always `'singleton'` |
| community_social_url | text | nullable; the social link |
| updated_at | timestamp | |

---

## RLS Policies

**`feedback`**
- `SELECT` — any authenticated user
- `INSERT` — any authenticated user (own org only)
- `UPDATE` — `platform_admin` only (status, admin_response, github fields)

**`feedback_votes`**
- `SELECT` — any authenticated user
- `INSERT` — authenticated user, own user_id only
- `DELETE` — own vote only (toggling off)

**`platform_settings`**
- `SELECT` — any authenticated user (needed to show/hide social link)
- `UPDATE` — `platform_admin` only

---

## Environment Variables

```bash
# GitHub integration
GITHUB_TOKEN=ghp_...          # Personal access token or fine-grained token
GITHUB_REPO_OWNER=trustixy    # org or user owning the repo
GITHUB_REPO_NAME=platform     # repo name
```

`GITHUB_TOKEN` requires `issues: write` permission. Works on private repos.

---

## Routes

| Route | Who | Description |
|---|---|---|
| `/feedback` | All authenticated users | Public feedback board + submit form |
| `/feedback/[id]` | All authenticated users | Item detail with full description + admin response |
| `/admin/feedback` | `platform_admin` | Ranked admin view with all actions |

---

## Server Actions

| Action | Who | What |
|---|---|---|
| `submitFeedback(type, title, description)` | Org users | Insert into `feedback`, status = `open` |
| `toggleVote(feedbackId)` | Org users | Insert or delete from `feedback_votes`, update `vote_count` |
| `updateFeedbackStatus(id, status)` | `platform_admin` | Update status |
| `postAdminResponse(id, response)` | `platform_admin` | Update `admin_response` + `admin_responded_at` |
| `pushToGitHub(id)` | `platform_admin` | Call GitHub API, store issue number + URL |
| `updateCommunityUrl(url)` | `platform_admin` | Upsert `platform_settings.community_social_url` |

---

## UI Notes

- Upvote button: filled icon if user already voted, outline if not. Optimistic update.
- Status badge colors: `Open` grey · `Planned` blue · `In Progress` amber · `Done` green · `Declined` red
- "Team response" block on item detail: distinct background (e.g. blue-50), "Trustixy Team" label, date
- GitHub issue link: opens in new tab, small external link icon, only shown when linked
- Community link in sidebar: shown as a simple text link with an icon (Discord/Telegram logo if URL domain matches, otherwise generic link icon). Hidden entirely if `community_social_url` is null.
