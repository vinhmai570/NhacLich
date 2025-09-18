# Data Model: Lunar Reminders App

**Date**: 2025-09-09
**Phase**: 1 - Data Model Design (Rails 8 + Solid Stack)

## Related Implementation Tasks
- **User entity**: T030 (model), T042 (migration) → Lines 22-70
- **Family entity**: T031 (model), T043 (migration) → Lines 72-102
- **Membership entity**: T032 (model), T044 (migration) → Lines 104-126
- **Person/PersonRelationship**: T037-T038 (models), T048 (migration) → Lines 128-166
- **Event entity**: T033 (model), T045 (migration) → Lines 168-212
- **Reminder/Recipient**: T034-T035 (models), T046 (migration) → Lines 214-256
- **Expense entity**: T036 (model), T047 (migration) → Lines 258-281
- **IntegrationAccount**: T039 (model), T049 (migration) → Lines 282-304
- **AuditLog entity**: T041 (model), T050 (migration) → Lines 306-328
- **Session entity (Rails 8)**: T040 (model) → Lines 330-342
- **Database constraints & indexes**: T051 (performance migration) → Lines 377-423

## Entity Relationships

```
User (1) ←→ (n) Membership (n) ←→ (1) Family
User (1) ←→ (n) Event
Family (1) ←→ (n) Event
Family (1) ←→ (n) Person
Event (1) ←→ (n) Reminder
Event (1) ←→ (n) Expense
Reminder (1) ←→ (n) Recipient
User (1) ←→ (n) IntegrationAccount
User/Event ←→ AuditLog
```

## Core Entities

### User
**Purpose**: Individual user account with Rails 8 built-in authentication and preferences

**Fields**:
- `id` (UUID, Primary Key)
- `email` (String, Unique, Required)
- `password_digest` (String, Optional - Rails 8 authentication, null for OAuth-only users)
- `name` (String, Required)
- `phone` (String, Optional)
- `locale` (String, Default: 'en', Values: ['en', 'vi'])
- `timezone` (String, Default: 'UTC')
- `avatar` (ActiveStorage attachment, Optional)
- `confirmed_at` (DateTime, Optional - Rails 8 confirmation)
- `recovery_codes` (Text Array, Rails 8 recovery codes)
- `google_uid` (String, Optional, Unique - Google OAuth user ID)
- `google_access_token` (Text, Encrypted, Optional - for Calendar API)
- `google_refresh_token` (Text, Encrypted, Optional)
- `google_expires_at` (DateTime, Optional)
- `auth_method` (Enum: ['password', 'google', 'both'], Default: 'password')
- `created_at` (DateTime)
- `updated_at` (DateTime)

**Rails 8 + OAuth Authentication Features**:
- Built-in password hashing with `has_secure_password` (optional)
- Google OAuth integration with `omniauth-google-oauth2`
- Email confirmation system
- Password reset functionality (for password auth)
- Recovery codes for account access
- Session management
- OAuth token refresh handling
- Account linking (password + Google OAuth)

**Validations**:
- Email format validation with Rails 8 improvements
- Password strength validation (Rails 8 defaults, when password_digest present)
- Name minimum 2 characters
- Phone format validation (international)
- Locale in allowed list
- Timezone from Rails TimeZone list
- Google UID uniqueness when present
- At least one authentication method (password_digest OR google_uid)

**Relationships**:
- `has_many :memberships`
- `has_many :families, through: :memberships`
- `has_many :events, dependent: :destroy`
- `has_many :integration_accounts, dependent: :destroy`
- `has_many :sessions, dependent: :destroy` (Rails 8 sessions)

### Family
**Purpose**: Collaborative group workspace for shared events and genealogy

**Fields**:
- `id` (UUID, Primary Key)
- `name` (String, Required)
- `description` (Text, Optional)
- `settings` (JSONB, Default: {})
- `created_at` (DateTime)
- `updated_at` (DateTime)

**Validations**:
- Name minimum 2 characters, maximum 100
- Settings JSON schema validation

**Relationships**:
- `has_many :memberships, dependent: :destroy`
- `has_many :users, through: :memberships`
- `has_many :events, dependent: :destroy`
- `has_many :people, dependent: :destroy`

**Settings Schema**:
```json
{
  "default_timezone": "UTC",
  "default_locale": "en",
  "notification_defaults": {
    "channels": ["email"],
    "lead_times": ["1_day"]
  }
}
```

### Membership
**Purpose**: Junction table for User-Family relationship with roles

**Fields**:
- `id` (UUID, Primary Key)
- `user_id` (UUID, Foreign Key, Required)
- `family_id` (UUID, Foreign Key, Required)
- `role` (Enum: ['owner', 'editor', 'viewer'], Required)
- `invited_at` (DateTime, Optional)
- `joined_at` (DateTime, Required)
- `invitation_token` (String, Optional, Unique)
- `created_at` (DateTime)
- `updated_at` (DateTime)

**Validations**:
- Unique user per family
- At least one owner per family
- Invitation token expires after 7 days

**Relationships**:
- `belongs_to :user`
- `belongs_to :family`

### Person
**Purpose**: Genealogy record within a family for biography and relationships

**Fields**:
- `id` (UUID, Primary Key)
- `family_id` (UUID, Foreign Key, Required)
- `name` (String, Required)
- `names` (JSONB, Additional names/nicknames)
- `birthday_lunar` (Date, Optional)
- `birthday_gregorian` (Date, Optional)
- `deceased_date` (Date, Optional)
- `biography` (Text, Optional)
- `location_data` (JSONB, Memorial site coordinates)
- `photo` (ActiveStorage attachment, Optional)
- `created_at` (DateTime)
- `updated_at` (DateTime)

**Validations**:
- Name minimum 2 characters
- At least one birthday type required
- Location data JSON schema validation

**Relationships**:
- `belongs_to :family`
- `has_many :relationships, class_name: 'PersonRelationship'`

### PersonRelationship
**Purpose**: Family tree relationships between people

**Fields**:
- `id` (UUID, Primary Key)
- `person_id` (UUID, Foreign Key, Required)
- `related_person_id` (UUID, Foreign Key, Required)
- `relationship_type` (Enum: ['parent', 'child', 'spouse', 'sibling'])
- `created_at` (DateTime)

**Validations**:
- Cannot relate person to themselves
- Reciprocal relationships must be consistent

### Event
**Purpose**: Calendar event with lunar/gregorian dates and recurrence

**Fields**:
- `id` (UUID, Primary Key)
- `user_id` (UUID, Foreign Key, Optional - can be family-level)
- `family_id` (UUID, Foreign Key, Optional - can be personal)
- `title` (String, Required)
- `description` (Text, Optional)
- `location` (String, Optional)
- `date_type` (Enum: ['lunar', 'gregorian'], Required)
- `date_value` (Date, Required)
- `lunar_date` (JSONB, Lunar date components)
- `recurrence` (Enum: ['none', 'monthly', 'yearly'], Default: 'none')
- `recurrence_on_lunar` (Boolean, Default based on date_type)
- `all_day` (Boolean, Default: true)
- `start_time` (Time, Optional)
- `end_time` (Time, Optional)
- `tags` (String Array, Default: [])
- `visibility` (Enum: ['private', 'family', 'public'], Default: 'family')
- `metadata` (JSONB, Flexible event data)
- `created_at` (DateTime)
- `updated_at` (DateTime)

**Validations**:
- Title minimum 3 characters
- End time after start time when both present
- Either user_id or family_id must be present
- Lunar date components validation

**Relationships**:
- `belongs_to :user, optional: true`
- `belongs_to :family, optional: true`
- `has_many :reminders, dependent: :destroy`
- `has_many :expenses, dependent: :destroy`

**Lunar Date Schema**:
```json
{
  "year": 2024,
  "month": 7,
  "day": 15,
  "leap_month": false,
  "cycle_year": 41
}
```

### Reminder
**Purpose**: Notification configuration for events with scheduling

**Fields**:
- `id` (UUID, Primary Key)
- `event_id` (UUID, Foreign Key, Required)
- `lead_time_value` (Integer, Required)
- `lead_time_unit` (Enum: ['days', 'weeks', 'months'], Required)
- `notify_time` (Time, Default: '07:00')
- `channels` (String Array, Required, Values: ['email', 'sms', 'push', 'zalo'])
- `enabled` (Boolean, Default: true)
- `created_at` (DateTime)
- `updated_at` (DateTime)

**Validations**:
- Lead time value positive integer
- At least one channel selected
- Notify time in 24-hour format

**Relationships**:
- `belongs_to :event`
- `has_many :recipients, dependent: :destroy`

### Recipient
**Purpose**: Contact information for notification delivery

**Fields**:
- `id` (UUID, Primary Key)
- `reminder_id` (UUID, Foreign Key, Optional - can be reusable)
- `name` (String, Required)
- `email` (String, Optional)
- `phone` (String, Optional)
- `group_name` (String, Optional, for reusable groups)
- `created_at` (DateTime)
- `updated_at` (DateTime)

**Validations**:
- At least email or phone required
- Email format validation
- Phone international format

**Relationships**:
- `belongs_to :reminder, optional: true`

### Expense
**Purpose**: Financial tracking for ceremony events

**Fields**:
- `id` (UUID, Primary Key)
- `event_id` (UUID, Foreign Key, Required)
- `item` (String, Required)
- `amount_cents` (Integer, Required)
- `currency` (String, Default: 'USD')
- `category` (String, Optional)
- `note` (Text, Optional)
- `receipt` (ActiveStorage attachment, Optional)
- `expense_date` (Date, Required)
- `created_at` (DateTime)
- `updated_at` (DateTime)

**Validations**:
- Amount positive
- Currency ISO code validation
- Item minimum 2 characters

**Relationships**:
- `belongs_to :event`

### IntegrationAccount
**Purpose**: External service credentials for calendar sync

**Fields**:
- `id` (UUID, Primary Key)
- `user_id` (UUID, Foreign Key, Required)
- `provider` (Enum: ['google', 'microsoft'], Required)
- `provider_uid` (String, Required)
- `access_token` (Text, Encrypted)
- `refresh_token` (Text, Encrypted)
- `expires_at` (DateTime)
- `sync_preferences` (JSONB, Default: {})
- `last_sync_at` (DateTime)
- `created_at` (DateTime)
- `updated_at` (DateTime)

**Validations**:
- Unique provider per user
- Access token presence
- Valid JSON sync preferences

**Relationships**:
- `belongs_to :user`

### AuditLog
**Purpose**: Activity tracking for compliance and debugging

**Fields**:
- `id` (UUID, Primary Key)
- `actor_id` (UUID, Foreign Key, Optional)
- `actor_type` (String, Default: 'User')
- `action` (String, Required)
- `auditable_id` (UUID, Optional)
- `auditable_type` (String, Optional)
- `changes` (JSONB, Optional)
- `metadata` (JSONB, Optional)
- `ip_address` (String, Optional)
- `user_agent` (String, Optional)
- `timestamp` (DateTime, Required)

**Validations**:
- Action minimum 3 characters
- Valid JSON for changes and metadata

**Relationships**:
- `belongs_to :actor, polymorphic: true, optional: true`
- `belongs_to :auditable, polymorphic: true, optional: true`

### Session (Rails 8 Authentication)
**Purpose**: User session management for Rails 8 authentication

**Fields**:
- `id` (UUID, Primary Key)
- `user_id` (UUID, Foreign Key, Required)
- `user_agent` (String)
- `ip_address` (String)
- `created_at` (DateTime)
- `updated_at` (DateTime)

**Relationships**:
- `belongs_to :user`

## Solid Stack Tables

Rails 8 Solid Stack automatically creates these tables:

### solid_queue_jobs
**Purpose**: Background job storage for Solid Queue
- Notification delivery jobs
- Recurring event processing
- Calendar sync jobs
- Email/SMS dispatch

### solid_queue_scheduled_jobs
**Purpose**: Scheduled jobs for future execution
- Reminder notifications with lead times
- Recurring event generation
- Cleanup tasks

### solid_queue_claimed_executions
**Purpose**: Job execution tracking and monitoring

### solid_cache_entries
**Purpose**: Application cache storage replacing Redis
- Session data
- Calendar view cache
- Lunar date conversions
- User preferences

### solid_cable_messages
**Purpose**: Real-time WebSocket message storage
- Live calendar updates
- Family collaboration notifications
- Event change broadcasts

## Indexes

**Performance-critical indexes**:
- `users(email)` - unique
- `events(user_id, created_at)`
- `events(family_id, created_at)`
- `events(date_type, date_value)`
- `memberships(user_id, family_id)` - unique
- `reminders(event_id)`
- `audit_logs(actor_id, timestamp)`
- `audit_logs(auditable_type, auditable_id, timestamp)`

## Database Constraints

- Foreign key constraints on all relationships
- Check constraints for enum validations
- Unique constraints on user email, membership pairs
- Partial unique indexes for invitation tokens (where not null)

## Security Considerations

- All personally identifiable information encrypted at rest
- OAuth tokens encrypted with Rails credentials
- Row-level security through family_id scoping
- Audit logging for all data changes
- GDPR-compliant data export/deletion capabilities

## Migration Strategy

**Rails 8 Setup**:
1. `rails new lunar_reminders --database=postgresql --solid-queue --solid-cache --solid-cable`
2. `rails generate authentication User name email phone locale timezone`
3. Solid Stack tables created automatically

**Custom Migrations**:
1. Family and membership system
2. Core event and reminder system with JSONB optimizations
3. Extensions (expenses, integrations, genealogy)
4. PostgreSQL-specific optimizations and indexes
5. JSONB GIN indexes for lunar date queries
6. Partial indexes for performance optimization

**Rails 8 Advantages**:
- Single `rails new` command sets up entire Solid Stack
- Built-in authentication scaffold reduces custom code
- Optimized PostgreSQL configurations out-of-the-box
- Automatic migration rollback safety checks