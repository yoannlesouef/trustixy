# Email Sequences

All emails are transactional or behavioral — no newsletters. Sent from noreply@trustixy.com, reply-to support@trustixy.com. Plain text preferred; one clear CTA per email.

---

## 1. Activation Sequence

Triggered by signup. Goal: get the user to their first session within 48 hours.

### Email 1 — Sent immediately on signup

**Subject:** Your Trustixy API key is ready

```
Hi [first name],

Your API key: txk_live_[key]

Add it to your project in one line:

  const client = trustixy.wrap(new Anthropic(), {
    project: "my-app",
    agent: "code-assistant"
  });

Run your agent once — your first session will appear in Activity within seconds.

[Open dashboard →]

Takes 5 minutes. No config required.

— Trustixy
```

---

### Email 2 — Sent 24h after signup if no session yet

**Subject:** Haven't seen your first session yet

```
Hi [first name],

You signed up but we haven't seen any SDK activity yet.

Most common reason: the API key isn't set in the environment.

Make sure TRUSTIXY_API_KEY is available where your agent runs:

  export TRUSTIXY_API_KEY=txk_live_[key]

[View quickstart →]

If you're stuck, reply to this email — I read every reply.

— Yoann, Trustixy
```

---

### Email 3 — Sent when first session is detected

**Subject:** First session captured ✓

**Trigger:** `sessions` row inserted for this organization for the first time.

```
Hi [first name],

Your first agent session just appeared in Trustixy.

You can now see exactly what [agent-name] did: every LLM call,
file write, and API call — in order, with timing.

Next: classify [agent-name] for EU AI Act compliance.
It takes 3 minutes and the form is pre-filled from what the SDK observed.

[Classify [agent-name] →]

— Trustixy
```

---

### Email 4 — Sent 48h after first session if agent not classified

**Subject:** Do you know if [agent-name] is in scope for the EU AI Act?

**Trigger:** First session exists, no classification exists, 48h elapsed.

```
Hi [first name],

[agent-name] has been running for 2 days. We've captured [N] sessions.

One question: is it in scope for the EU AI Act?

The classification takes 3 minutes. The questionnaire is pre-filled
from observed behavior — autonomy level, data types, action patterns.
You review and confirm; we generate the risk level and obligations list.

[Classify [agent-name] →]

If it's clearly out of scope (internal tooling, no impact on people),
you can dismiss the prompt from the Agents page.

— Trustixy
```

---

## 2. Weekly Digest

**Trigger:** Every Monday 8:00 AM (user's timezone), if at least one session in the past 7 days.  
**Condition:** Only sent if organization has had activity. Never sent to inactive organizations.

**Subject:** [agent-name] last week: [N] sessions, [N] actions

```
Hi [first name],

Here's what your agents did last week:

  [agent-name]        [N] sessions · [N] actions · [N] file writes
  [agent-name-2]      [N] sessions · [N] actions

[2 sensitive file alerts] — [agent-name] accessed .env twice
→ [View alerts]

Compliance status:
  [agent-name]        ● Classified · High risk · 3 obligations pending
  [agent-name-2]      ○ Not classified

[Open Activity →]

— Trustixy
```

Fields left blank if zero. Sensitive file alerts section only shown if alerts exist that week.

---

## 3. Upgrade Nudge Sequence

### Nudge 1 — 4th agent detected (Free plan limit)

**Subject:** You've reached 3 agents on the Free plan

**Trigger:** 4th distinct agent slug detected in ingest for a Free organization.

```
Hi [first name],

[new-agent-name] just appeared in your SDK data — but your Free plan
covers 3 agents and you already have [agent-1], [agent-2], [agent-3].

To track [new-agent-name] and keep all your agents visible:

[Upgrade to Pro — €99/month →]

Pro includes unlimited agents, 1-year log retention, co-signature
requests, and full obligation tracking.

— Trustixy
```

---

### Nudge 2 — Log approaching 30-day limit

**Subject:** Sessions older than 30 days will be deleted in 3 days

**Trigger:** Free plan, oldest session is 27 days old.

```
Hi [first name],

Your Free plan retains sessions for 30 days. You have sessions
from [date] that will be deleted on [date+3].

If you need this history for compliance evidence, upgrade before [date+3]:

[Upgrade to Pro — €99/month →]

Pro retains your full session history for 1 year.

— Trustixy
```

---

### Nudge 3 — Co-signature blocked

**Trigger:** Free user clicks "Request co-signature" — blocked by plan gate.  
**Delivery:** In-app only (no email). See ux.md upgrade moments.

---

### Nudge 4 — 14 days on Free, no upgrade

**Subject:** What's blocking you from upgrading Trustixy?

**Trigger:** 14 days since signup, Free plan, at least 1 session, no upgrade.

```
Hi [first name],

You've been using Trustixy for 2 weeks. Your agents are being tracked —
but you haven't upgraded to Pro yet.

I'm curious: is something holding you back?

Reply to this email with a number:

  1. Price
  2. Not sure I need co-signature yet
  3. Still evaluating
  4. Missing a feature
  5. Other

Takes 10 seconds. I read every reply and it directly shapes what we build.

— Yoann, Trustixy
```

---

## 4. Prescriber Sequences

### Application received

**Trigger:** Partner application submitted.

**Subject:** Application received — response within 48h

```
Hi [first name],

We received your application for [organization].

We review every application manually. You'll hear back within 48 hours
with either approval + partner agreement, or a request for more info.

In the meantime, if you have questions:
reply to this email or email yoann@trustixy.com directly.

— Yoann, Trustixy
```

---

### Application approved

**Trigger:** Admin approves partner application.

**Subject:** You're a Trustixy partner — here's how to start

```
Hi [first name],

Your partner application for [organization] is approved.

[Access the Partner Portal →]

Three things to do first:

  1. Complete Stripe Connect (5 min) — required to receive co-signature payments
  2. Invite your first client — Partner Portal → Clients → Invite client
  3. Download the client one-pager — Settings → Marketing assets

Your partner link: trustixy.com/join?ref=[partner-slug]
Share it with clients for automatic account linking.

Reply with any questions.

— Yoann, Trustixy
```

---

### Co-signature request received (to prescriber)

**Trigger:** Client submits a co-signature request.

**Subject:** New co-signature request from [client-org] — [agent-name]

```
Hi [first name],

[client-org] has submitted a co-signature request for [agent-name].

  Risk level: [High / Limited / Minimal]
  Confidence score: [N]/100
  Obligations: [N] items

[Review request →]

Average review time is under 15 minutes for well-documented systems.

— Trustixy
```

---

### Co-signature completed (to end user)

**Trigger:** Prescriber approves co-signature.

**Subject:** [agent-name] compliance document co-signed by [prescriber-firm]

```
Hi [first name],

[prescriber-name] from [prescriber-firm] has reviewed and co-signed
the compliance document for [agent-name].

[Download PDF →]

The document is now in your audit trail. The prescriber's name, firm,
and review date are permanently embedded.

— Trustixy
```

---

## 5. Re-engagement

**Trigger:** No session in 14 days, organization previously had at least 5 sessions.

**Subject:** [agent-name] hasn't been seen in 2 weeks

```
Hi [first name],

We haven't seen any activity from [agent-name] in 14 days.

If the project is paused, no action needed — your data is safe.

If the SDK was removed or the key rotated, here's how to reconnect:
[Reconnect guide →]

— Trustixy
```

---

## Sending Rules

- Maximum 1 behavioral email per day per user
- Digest suppressed if user opened the app in the last 3 days
- Unsubscribe from marketing emails does not suppress transactional (API key, co-signature notifications)
- All sequences pause if the user upgrades (remove from upgrade nudge queue)
