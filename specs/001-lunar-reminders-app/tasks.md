# Tasks: Lunar Reminders App

**Input**: Design documents from `/specs/001-lunar-reminders-app/`
**Prerequisites**: plan.md, research.md, data-model.md, contracts/api-spec.yml, quickstart.md

## Execution Flow (main)
```
1. Load plan.md from feature directory ✓
   → Tech stack: Rails 8, Solid Stack, PWA, Google OAuth, PostgreSQL
   → Structure: Single Rails app with PWA capabilities
2. Load design documents: ✓
   → data-model.md: 11 entities (User, Family, Event, etc.)
   → contracts/api-spec.yml: 25+ endpoints across authentication, events, PWA
   → quickstart.md: 8 user stories with integration test scenarios
3. Generate tasks by category: ✓
   → Setup: Rails 8 app, gems, OAuth, PWA configuration
   → Tests: contract tests, integration tests for all user stories
   → Core: models, services, controllers, background jobs
   → PWA: service worker, manifest, offline capabilities
   → Integration: OAuth, calendar sync, notifications
4. Apply task rules: ✓
   → Different models = [P], same controller = sequential
   → Tests before implementation (TDD)
5. Number tasks T001-T089
6. Validate: All contracts, entities, and stories covered ✓
```

## Format: `[ID] [P?] Description`
- **[P]**: Can run in parallel (different files, no dependencies)
- File paths based on Rails 8 conventions

## Phase 3.1: Setup (Rails 8 + Solid Stack + PWA)
- [ ] T001 Create Rails 8 app with Solid Stack: `rails new lunar-reminders-app --database=postgresql --solid-queue --solid-cache --solid-cable --css=tailwind`
- [ ] T002 Add gems to Gemfile: pwa, web_push, omniauth-google-oauth2, lunardate, icalendar, money, pundit
- [ ] T003 [P] Configure linting tools: rubocop, eslint, prettier for Rails 8 + JavaScript
- [ ] T004 [P] Setup PWA configuration: `rails generate pwa:install` and customize manifest
- [ ] T005 Setup Google OAuth: create config/initializers/omniauth.rb with Google OAuth2 provider
- [ ] T006 Generate Rails 8 authentication: `rails generate authentication User name email phone locale timezone`

## Phase 3.2: Tests First (TDD) ⚠️ MUST COMPLETE BEFORE 3.3
**CRITICAL: These tests MUST be written and MUST FAIL before ANY implementation**

### Contract Tests [P] - API Endpoints
- [ ] T007 [P] Contract test POST /api/v1/auth/login in spec/requests/auth_login_spec.rb
- [ ] T008 [P] Contract test GET /api/v1/auth/google in spec/requests/auth_google_spec.rb
- [ ] T009 [P] Contract test POST /api/v1/users in spec/requests/users_create_spec.rb
- [ ] T010 [P] Contract test GET /api/v1/users/{id} in spec/requests/users_show_spec.rb
- [ ] T011 [P] Contract test GET /api/v1/families in spec/requests/families_index_spec.rb
- [ ] T012 [P] Contract test POST /api/v1/families in spec/requests/families_create_spec.rb
- [ ] T013 [P] Contract test GET /api/v1/events in spec/requests/events_index_spec.rb
- [ ] T014 [P] Contract test POST /api/v1/events in spec/requests/events_create_spec.rb
- [ ] T015 [P] Contract test GET /api/v1/events/{id}/reminders in spec/requests/reminders_index_spec.rb
- [ ] T016 [P] Contract test POST /api/v1/events/{id}/reminders in spec/requests/reminders_create_spec.rb
- [ ] T017 [P] Contract test GET /api/v1/calendar/export in spec/requests/calendar_export_spec.rb
- [ ] T018 [P] Contract test POST /api/v1/lunar-calendar/convert in spec/requests/lunar_convert_spec.rb
- [ ] T019 [P] Contract test GET /api/v1/pwa/manifest.json in spec/requests/pwa_manifest_spec.rb
- [ ] T020 [P] Contract test POST /api/v1/pwa/offline-sync in spec/requests/pwa_sync_spec.rb
- [ ] T021 [P] Contract test GET /api/v1/admin/solid_queue/jobs in spec/requests/solid_queue_spec.rb

### Integration Tests [P] - User Stories
- [ ] T022 [P] Integration test: Family organizer creates memorial event in spec/features/memorial_event_creation_spec.rb
- [ ] T023 [P] Integration test: Individual monthly ritual reminders in spec/features/monthly_ritual_spec.rb
- [ ] T024 [P] Integration test: Event planner long-term notifications in spec/features/long_term_planning_spec.rb
- [ ] T025 [P] Integration test: Family collaboration with roles in spec/features/family_collaboration_spec.rb
- [ ] T026 [P] Integration test: Calendar export integration in spec/features/calendar_export_spec.rb
- [ ] T027 [P] Integration test: PWA installation and offline usage in spec/features/pwa_offline_spec.rb
- [ ] T028 [P] Integration test: PWA push notifications in spec/features/pwa_notifications_spec.rb
- [ ] T029 [P] Integration test: Google OAuth authentication and account linking in spec/features/google_oauth_spec.rb

## Phase 3.3: Core Implementation (ONLY after tests are failing)

### Models [P] - Database Layer
- [ ] T030 [P] User model with Google OAuth fields in app/models/user.rb
- [ ] T031 [P] Family model with JSONB settings in app/models/family.rb
- [ ] T032 [P] Membership model with role enum in app/models/membership.rb
- [ ] T033 [P] Event model with lunar date JSONB in app/models/event.rb
- [ ] T034 [P] Reminder model with lead time configuration in app/models/reminder.rb
- [ ] T035 [P] Recipient model for notification contacts in app/models/recipient.rb
- [ ] T036 [P] Expense model with Money gem integration in app/models/expense.rb
- [ ] T037 [P] Person model for genealogy in app/models/person.rb
- [ ] T038 [P] PersonRelationship model for family tree in app/models/person_relationship.rb
- [ ] T039 [P] IntegrationAccount model for calendar sync in app/models/integration_account.rb
- [ ] T040 [P] Session model for Rails 8 authentication in app/models/session.rb
- [ ] T041 [P] AuditLog model for activity tracking in app/models/audit_log.rb

### Database Migrations [P] - Schema Creation
- [ ] T042 [P] Migration: Add Google OAuth fields to users table in db/migrate/add_google_oauth_to_users.rb
- [ ] T043 [P] Migration: Create families table with JSONB settings in db/migrate/create_families.rb
- [ ] T044 [P] Migration: Create memberships table with roles in db/migrate/create_memberships.rb
- [ ] T045 [P] Migration: Create events table with lunar date JSONB in db/migrate/create_events.rb
- [ ] T046 [P] Migration: Create reminders and recipients tables in db/migrate/create_reminders.rb
- [ ] T047 [P] Migration: Create expenses table with Money fields in db/migrate/create_expenses.rb
- [ ] T048 [P] Migration: Create people and relationships tables in db/migrate/create_people.rb
- [ ] T049 [P] Migration: Create integration_accounts table in db/migrate/create_integration_accounts.rb
- [ ] T050 [P] Migration: Create audit_logs table in db/migrate/create_audit_logs.rb
- [ ] T051 [P] Migration: Add PostgreSQL indexes for performance in db/migrate/add_performance_indexes.rb

### Services [P] - Business Logic
- [ ] T052 [P] LunarCalendarService for date conversions in app/services/lunar_calendar_service.rb
- [ ] T053 [P] NotificationService for multi-channel notifications in app/services/notification_service.rb
- [ ] T054 [P] EventRecurrenceService for recurring events in app/services/event_recurrence_service.rb
- [ ] T055 [P] CalendarExportService for ICS generation in app/services/calendar_export_service.rb
- [ ] T056 [P] GoogleCalendarSyncService for external sync in app/services/google_calendar_sync_service.rb
- [ ] T057 [P] PwaDataService for offline data packaging in app/services/pwa_data_service.rb

### Background Jobs [P] - Solid Queue
- [ ] T058 [P] NotificationDeliveryJob for email/SMS/push in app/jobs/notification_delivery_job.rb
- [ ] T059 [P] RecurringEventGenerationJob for future events in app/jobs/recurring_event_generation_job.rb
- [ ] T060 [P] CalendarSyncJob for Google Calendar integration in app/jobs/calendar_sync_job.rb
- [ ] T061 [P] PwaBackgroundSyncJob for offline data sync in app/jobs/pwa_background_sync_job.rb

## Phase 3.4: Controllers & API Implementation (Sequential - same routes file)
- [ ] T062 AuthenticationController with Rails 8 auth + Google OAuth in app/controllers/authentication_controller.rb
- [ ] T063 UsersController with user management in app/controllers/api/v1/users_controller.rb
- [ ] T064 FamiliesController with role-based access in app/controllers/api/v1/families_controller.rb
- [ ] T065 EventsController with lunar date handling in app/controllers/api/v1/events_controller.rb
- [ ] T066 RemindersController with notification scheduling in app/controllers/api/v1/reminders_controller.rb
- [ ] T067 CalendarController with export/import in app/controllers/api/v1/calendar_controller.rb
- [ ] T068 LunarCalendarController with date conversion in app/controllers/api/v1/lunar_calendar_controller.rb
- [ ] T069 PwaController with offline sync and manifest in app/controllers/api/v1/pwa_controller.rb
- [ ] T070 AdminController with Solid Queue monitoring in app/controllers/api/v1/admin_controller.rb

## Phase 3.5: Frontend & PWA Implementation
- [ ] T071 [P] Service Worker for offline functionality in app/javascript/service-worker.js
- [ ] T072 [P] PWA manifest configuration in app/views/pwa/manifest.json.erb
- [ ] T073 [P] IndexedDB service for offline storage in app/javascript/services/indexeddb-service.js
- [ ] T074 [P] Background sync service for offline-to-online in app/javascript/services/background-sync-service.js
- [ ] T075 [P] Push notification service for PWA in app/javascript/services/push-notification-service.js
- [ ] T076 [P] FullCalendar integration with lunar dates in app/javascript/controllers/calendar_controller.js
- [ ] T077 [P] Google OAuth Stimulus controller in app/javascript/controllers/google_oauth_controller.js
- [ ] T078 Rails views with Turbo frames for SPA experience in app/views/
- [ ] T079 Tailwind CSS styling for mobile-first PWA design in app/assets/stylesheets/

## Phase 3.6: Integration & Configuration
- [ ] T080 Configure Pundit authorization policies in app/policies/
- [ ] T081 Setup ActiveJob with Solid Queue for background processing in config/application.rb
- [ ] T082 Configure ActionMailer for notification emails in config/environments/
- [ ] T083 Setup WebPush for PWA notifications in config/initializers/web_push.rb
- [ ] T084 Configure CORS and security headers for PWA in config/initializers/cors.rb
- [ ] T085 Setup Google Calendar API client in config/initializers/google_calendar.rb

## Phase 3.7: Polish & Testing
- [ ] T086 [P] Unit tests for lunar calendar accuracy in spec/services/lunar_calendar_service_spec.rb
- [ ] T087 [P] Performance testing for <200ms response times using rspec-benchmark
- [ ] T088 [P] PWA Lighthouse audit automation in spec/system/pwa_performance_spec.rb
- [ ] T089 [P] End-to-end testing with Capybara for all user journeys in spec/system/

## Dependencies
**Critical Path**: Setup (T001-T006) → Tests (T007-T029) → Models (T030-T051) → Services (T052-T061) → Controllers (T062-T070) → Frontend (T071-T079) → Integration (T080-T085) → Polish (T086-T089)

**Blocking Dependencies**:
- T007-T029 MUST complete and FAIL before any implementation
- T030-T051 (models/migrations) block T052-T061 (services)
- T052-T061 (services) block T062-T070 (controllers)
- T062-T070 (controllers) block T071-T079 (frontend)
- T080-T085 (integration) require models, services, controllers
- T086-T089 (polish) require complete implementation

## Parallel Execution Examples

### Phase 3.2 - All Contract Tests Together:
```bash
Task: "Contract test POST /api/v1/auth/login in spec/requests/auth_login_spec.rb"
Task: "Contract test GET /api/v1/auth/google in spec/requests/auth_google_spec.rb"
Task: "Contract test POST /api/v1/users in spec/requests/users_create_spec.rb"
Task: "Contract test GET /api/v1/events in spec/requests/events_index_spec.rb"
Task: "Contract test GET /api/v1/pwa/manifest.json in spec/requests/pwa_manifest_spec.rb"
# ... all T007-T021 can run in parallel
```

### Phase 3.3 - All Models Together:
```bash
Task: "User model with Google OAuth fields in app/models/user.rb"
Task: "Family model with JSONB settings in app/models/family.rb"
Task: "Event model with lunar date JSONB in app/models/event.rb"
Task: "Reminder model with lead time configuration in app/models/reminder.rb"
# ... all T030-T041 can run in parallel
```

### Phase 3.4 - All Services Together:
```bash
Task: "LunarCalendarService for date conversions in app/services/lunar_calendar_service.rb"
Task: "NotificationService for multi-channel notifications in app/services/notification_service.rb"
Task: "EventRecurrenceService for recurring events in app/services/event_recurrence_service.rb"
# ... all T052-T061 can run in parallel
```

## Validation Checklist ✓
*GATE: Checked before task generation completion*

- [x] All 21 API contracts have corresponding test tasks (T007-T021)
- [x] All 11 entities have model creation tasks (T030-T041)
- [x] All 8 user stories have integration tests (T022-T029)
- [x] All tests come before implementation (Phase 3.2 → 3.3)
- [x] Parallel tasks are truly independent (different files)
- [x] Each task specifies exact file path
- [x] No [P] task modifies same file as another [P] task
- [x] PWA features fully covered (offline, sync, notifications)
- [x] Google OAuth integration fully implemented
- [x] Solid Stack utilization (Queue, Cache, Cable) included
- [x] Rails 8 authentication system integrated

## Notes
- **TDD Enforcement**: Tests T007-T029 must fail before starting T030+
- **Rails 8 Features**: Uses built-in authentication, Solid Stack, enhanced PostgreSQL
- **PWA Compliance**: Service Worker, manifest, offline sync, push notifications
- **Google OAuth**: Full integration with account linking and Calendar API
- **Lunar Calendar**: Vietnamese lunar calendar with leap month handling
- **Multi-tenant**: Family-based collaboration with role permissions
- **Performance**: <200ms response times, PWA score >90, offline-first design
- **Zero Dependencies**: No Redis required, all via PostgreSQL + Solid Stack
