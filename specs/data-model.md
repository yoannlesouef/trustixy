# Data Model

# Database Schema

## organizations

* id (uuid)
* name (text)
* created_at (timestamp)

## partners

* id (uuid)
* organization_id (uuid) — the partner's own org
* tier (text) — referral | reseller | embedded | group
* white_label_config (jsonb) — logo, colors, custom domain
* commission_rate (numeric)
* status (text) — pending | active | suspended
* created_at (timestamp)

## partner_clients

* id (uuid)
* partner_id (uuid)
* client_organization_id (uuid)
* invited_at (timestamp)
* status (text) — invited | active

## users

* id (uuid)
* organization_id (uuid)
* email (text)
* role (text) — admin | member
* created_at (timestamp)

## ai_systems

* id (uuid)
* organization_id (uuid)
* name (text)
* description (text)
* use_case (text)
* data_used (text)
* autonomy_level (text)
* owner (text)
* created_at (timestamp)

## classifications

* id (uuid)
* ai_system_id (uuid)
* risk_level (text)
* explanation (text)
* obligations (jsonb)
* created_at (timestamp)

## documents

* id (uuid)
* ai_system_id (uuid)
* content (text)
* created_at (timestamp)

## audit_logs

* id (uuid)
* ai_system_id (uuid)
* user_id (uuid)
* action (text)
* metadata (jsonb)
* created_at (timestamp)

