# Data Model

# Database Schema

## ai_systems

* id (uuid)
* user_id (uuid)
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
* action (text)
* metadata (jsonb)
* created_at (timestamp)

