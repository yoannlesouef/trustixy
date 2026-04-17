# Stripe Integration

## Overview

Two distinct Stripe integrations:
1. **Stripe Subscriptions** — organizations pay €99/month (Pro) or €399/month (Integrator)
2. **Stripe Connect Express** — prescriber partners receive per-act co-signature payouts

---

## Products & Prices to Create in Stripe Dashboard

| Product | Price ID env var | Amount | Interval | Metadata |
|---|---|---|---|---|
| Trustixy Pro | `STRIPE_PRO_PRICE_ID` | €99 | Monthly | `plan=pro` |
| Trustixy Integrator | `STRIPE_INTEGRATOR_PRICE_ID` | €399 | Monthly | `plan=integrator` |

Create these in Stripe dashboard before first deployment. Store price IDs in environment variables — never hardcode.

---

## Subscription Flow (Pro / Integrator)

### Checkout

1. User clicks [Upgrade to Pro] or [Upgrade to Integrator]
2. Server action calls `stripe.checkout.sessions.create()`:

```typescript
const session = await stripe.checkout.sessions.create({
  mode: 'subscription',
  customer_email: user.email,
  line_items: [{ price: process.env.STRIPE_PRO_PRICE_ID, quantity: 1 }],
  success_url: `${APP_URL}/settings/billing?success=true`,
  cancel_url: `${APP_URL}/settings/billing?canceled=true`,
  metadata: {
    organization_id: org.id,
    plan: 'pro',
  },
  subscription_data: {
    metadata: { organization_id: org.id },
  },
});

redirect(session.url);
```

3. User completes payment on Stripe-hosted page
4. Stripe fires `checkout.session.completed` webhook
5. Webhook handler updates `organizations.plan` and stores `stripe_customer_id`

### Organizations table additions

Add to `organizations`:
- `stripe_customer_id (text)` — nullable; set on first checkout
- `stripe_subscription_id (text)` — nullable; set on subscription created
- `subscription_status (text)` — `active` | `past_due` | `canceled` | `trialing`
- `current_period_end (timestamp)` — when current billing period ends

---

## Webhook Handler (`/api/webhooks/stripe`)

```typescript
// Verify signature
const sig = request.headers.get('stripe-signature');
const event = stripe.webhooks.constructEvent(body, sig, process.env.STRIPE_WEBHOOK_SECRET);

switch (event.type) {
  case 'checkout.session.completed':
    // Set organization plan + stripe_customer_id + stripe_subscription_id
    break;

  case 'customer.subscription.updated':
    // Handle plan change (upgrade/downgrade) + update subscription_status
    break;

  case 'customer.subscription.deleted':
    // Downgrade to free plan
    // Do NOT delete data — just restrict access
    break;

  case 'invoice.payment_failed':
    // Set subscription_status = past_due
    // Send payment failure email via Resend
    break;

  case 'invoice.payment_succeeded':
    // Reset subscription_status = active
    break;
}
```

Always return 200 to Stripe even if processing fails internally — log the error, retry via Supabase job. Stripe retries webhooks for up to 3 days on non-200 responses.

---

## Plan Enforcement

Plan gates are checked server-side in server actions and API routes — never trust client-side plan state.

```typescript
// lib/plan.ts
export async function requirePro(organizationId: string) {
  const org = await getOrganization(organizationId);
  if (org.plan === 'free') {
    throw new PlanGateError('pro');
  }
}
```

**Free plan limits:**
- Max 3 agents: checked on agent creation
- 30-day session retention: enforced via Supabase scheduled job (delete sessions older than 30 days where org is on free plan — runs nightly)
- Co-signature requests: blocked server-side, returns 402

**Grace period:** When `subscription_status = past_due`, grant 7-day grace period before downgrading access. Send payment failure email on day 1, reminder on day 5.

---

## Stripe Connect Express (Partner Payouts)

### Onboarding

1. Partner clicks [Connect Stripe account] in Partner Portal → Settings → Billing
2. Server action calls:

```typescript
const account = await stripe.accounts.create({ type: 'express' });

// Store account.id in partners.stripe_account_id
await supabase.from('partners').update({
  stripe_account_id: account.id
}).eq('id', partnerId);

// Generate onboarding link
const accountLink = await stripe.accountLinks.create({
  account: account.id,
  refresh_url: `${APP_URL}/partner/settings/billing?refresh=true`,
  return_url: `${APP_URL}/partner/settings/billing?connected=true`,
  type: 'account_onboarding',
});

redirect(accountLink.url);
```

3. Partner completes Stripe Express onboarding (~5 minutes)
4. On return: check `stripe.accounts.retrieve(account.id)` for `charges_enabled = true`
5. If enabled: mark partner as payout-ready in UI

### Co-signature Payment Transfer

Triggered when prescriber approves a co-signature.

```typescript
// 1. Charge the client organization (charge on their stored payment method)
const paymentIntent = await stripe.paymentIntents.create({
  amount: Math.round(partner.cosignature_fee * 100), // in cents
  currency: 'eur',
  customer: org.stripe_customer_id,
  payment_method: org.default_payment_method_id,
  confirm: true,
  transfer_data: {
    destination: partner.stripe_account_id,
    amount: Math.round((partner.cosignature_fee - TRUSTIXY_COMMISSION) * 100),
  },
  metadata: {
    document_id: documentId,
    partner_id: partnerId,
    client_organization_id: clientOrgId,
  },
});

// 2. Record in cosignature_transactions
await supabase.from('cosignature_transactions').insert({
  document_signature_id: signatureId,
  partner_id: partnerId,
  client_organization_id: clientOrgId,
  amount: partner.cosignature_fee,
  trustixy_commission: TRUSTIXY_COMMISSION, // 25
  stripe_transfer_id: paymentIntent.transfer_data?.transfer,
  status: 'completed',
});
```

`TRUSTIXY_COMMISSION = 25` (€). Stored as a constant, not in the database.

### Per-act payment method

For per-act co-signatures, the client organization must have a saved payment method. This is stored separately from the subscription (which may not exist on Free plan clients who get co-signatures through a prescriber-referred path).

Add to `organizations`:
- `default_payment_method_id (text)` — nullable; set when client first pays for a co-signature

On first co-signature request from a Free org: trigger Stripe SetupIntent to save payment method before the request is submitted.

---

## Customer Portal

Self-serve plan management (upgrade, downgrade, cancel, update payment method):

```typescript
const portalSession = await stripe.billingPortal.sessions.create({
  customer: org.stripe_customer_id,
  return_url: `${APP_URL}/settings/billing`,
});
redirect(portalSession.url);
```

Accessible from Settings → Billing → [Manage subscription].

---

## Testing

Use Stripe test mode with test cards:
- `4242 4242 4242 4242` — successful payment
- `4000 0000 0000 9995` — insufficient funds (test payment_failed webhook)
- `4000 0025 0000 3155` — requires 3D Secure

Test webhooks locally with Stripe CLI:
```bash
stripe listen --forward-to localhost:3000/api/webhooks/stripe
```
