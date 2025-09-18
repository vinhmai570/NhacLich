# Quickstart Guide: Lunar Reminders App

**Date**: 2025-09-09
**Phase**: 1 - Integration Test Scenarios (Rails 8 + Solid Stack)
**Purpose**: Executable validation scenarios for core user stories

## Related Integration Tests & Implementation Tasks
- **Story 1: Memorial Event** (Lines 103-132) → T022 (integration test), T033/T034/T045/T052 (implementation)
- **Story 2: Monthly Rituals** (Lines 153-177) → T023 (integration test), T033/T054/T053 (implementation)
- **Story 3: Long-term Planning** (Lines 178-202) → T024 (integration test), T034/T035/T053 (implementation)
- **Story 4: Family Collaboration** (Lines 204-227) → T025 (integration test), T031/T032/T080 (implementation)
- **Story 5: Calendar Export** (Lines 229-262) → T026 (integration test), T055/T017 (implementation)
- **Story 6: PWA Installation** (Lines 265-294) → T027 (integration test), T071/T072/T073/T074 (implementation)
- **Story 7: PWA Notifications** (Lines 297-328) → T028 (integration test), T075/T053 (implementation)
- **Story 8: Google OAuth** (Lines 329-372) → T029 (integration test), T030/T077/T056 (implementation)
- **Accuracy Tests** (Lines 375-380) → Lunar calendar validation for T052 (LunarCalendarService)
- **Performance Tests** (Lines 403-409) → Benchmarks for all implementation tasks

## Prerequisites

- Ruby 3.3+ installed
- PostgreSQL 16+ running
- Rails 8.0+ installed
- No Redis required (Solid Stack eliminates external dependencies)

## Setup Commands

```bash
# Create new Rails 8 app with Solid Stack and PWA features
rails new lunar-reminders-app --database=postgresql --solid-queue --solid-cache --solid-cable
cd lunar-reminders-app

# Add PWA and OAuth gems to Gemfile
echo 'gem "pwa"' >> Gemfile
echo 'gem "web_push"' >> Gemfile
echo 'gem "omniauth"' >> Gemfile
echo 'gem "omniauth-google-oauth2"' >> Gemfile
echo 'gem "omniauth-rails_csrf_protection"' >> Gemfile
bundle install

# Generate authentication system
rails generate authentication User name email phone locale timezone

# Generate PWA configuration
rails generate pwa:install

# Setup OAuth configuration (requires Google OAuth credentials)
# Create config/initializers/omniauth.rb with Google OAuth setup

# Setup database and run migrations
rails db:create db:migrate db:seed

# Run test suite (Rails 8 includes improved testing)
rails test

# Start development server with Solid Stack + PWA
rails server -p 3000
```

## Solid Stack Verification

```bash
# Verify Solid Queue is working
rails solid_queue:check

# Monitor background jobs
rails solid_queue:dashboard

# Check Solid Cache status
rails runner "Rails.cache.write('test', 'value'); puts Rails.cache.read('test')"

# Test Solid Cable (WebSocket)
# Visit http://localhost:3000/cable for WebSocket endpoint
```

## OAuth & PWA Verification

```bash
# Check OAuth routes
rails routes | grep auth

# Check PWA manifest
curl http://localhost:3000/pwa/manifest.json

# Test Google OAuth (requires Google OAuth app setup)
# 1. Create Google OAuth app in Google Cloud Console
# 2. Set authorized redirect URI: http://localhost:3000/auth/google/callback
# 3. Configure credentials in Rails credentials or environment variables
# 4. Visit http://localhost:3000/auth/google to test OAuth flow

# Test PWA installation (in browser)
# 1. Visit http://localhost:3000 in Chrome/Edge
# 2. Look for install prompt or + icon in address bar
# 3. Click to install as PWA app

# Test offline functionality
# 1. Install PWA app
# 2. Open installed app
# 3. Disconnect internet
# 4. Verify calendar still loads from cache
# 5. Create events offline
# 6. Reconnect internet and verify sync

# Lighthouse PWA audit
npx lighthouse http://localhost:3000 --only-categories=pwa --chrome-flags="--headless"

# Service Worker registration check
# Open browser dev tools → Application → Service Workers
# Should see registered Service Worker for PWA
```

## Core User Stories Validation

### Story 1: Family Organizer Creates Memorial Event

**Test Scenario**: Create annual lunar memorial event with family notifications

```bash
# Browser Test Flow
1. Visit http://localhost:3000
2. Sign up as new user: "organizer@family.com"
3. Create family: "Nguyen Family"
4. Navigate to calendar view
5. Click on today's date
6. Create event:
   - Title: "Memorial Day for Grandfather"
   - Date Type: Lunar
   - Date: 15th day, 7th lunar month
   - Recurrence: Yearly
   - Add family members as recipients
   - Set reminder: 3 days before, 7 AM
7. Save event
8. Verify event appears on calendar
9. Verify lunar-to-gregorian conversion displayed
10. Check that notification job is scheduled
```

**Expected Results**:
- Event created with correct lunar date
- Gregorian date automatically calculated and displayed
- Annual recurrence scheduled for future years
- Notification job queued for 3 days before gregorian date
- Event visible to all family members

**API Test**:
```bash
curl -X POST http://localhost:3000/api/v1/events \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Memorial Day for Grandfather",
    "date_type": "lunar",
    "date_value": "2024-08-19",
    "lunar_date": {
      "year": 2024,
      "month": 7,
      "day": 15,
      "leap_month": false
    },
    "recurrence": "yearly",
    "family_id": "<family_id>"
  }'
```

### Story 2: Individual Monthly Ritual Reminders

**Test Scenario**: Set up monthly lunar ritual notifications

```bash
# Browser Test Flow
1. Login as individual user
2. Create personal event:
   - Title: "Monthly Offering Ritual"
   - Date Type: Lunar
   - Day: 10th of each lunar month
   - Recurrence: Monthly
   - Reminder: 1 day before at 7 AM
   - Notification: Email + SMS
3. Save event
4. Verify monthly recurrences generated
5. Check notification schedule
```

**Expected Results**:
- Monthly recurring event created
- Next 12 occurrences calculated and displayed
- Notifications scheduled for each occurrence
- Lunar day 10 correctly mapped to gregorian dates

### Story 3: Event Planner Long-term Notifications

**Test Scenario**: Create client event with 3-month advance notice

```bash
# Browser Test Flow
1. Login as event planner
2. Create client event:
   - Title: "Anniversary Celebration - Smith Family"
   - Date Type: Gregorian
   - Date: 6 months from now
   - Multiple recipients (client family members)
   - Reminder: 3 months, 1 month, 1 week, 1 day before
   - Channels: Email, SMS
3. Save event
4. Verify all reminder schedules created
5. Check notification preview shows exact send times
```

**Expected Results**:
- Event saved with future date
- Multiple reminders scheduled at different intervals
- All recipients configured for each reminder
- Notification preview displays calculated send timestamps

### Story 4: Family Collaboration with Roles

**Test Scenario**: Test role-based permissions in family group

```bash
# Browser Test Flow
1. Family Owner creates family and invites members:
   - Editor: family_editor@example.com
   - Viewer: family_viewer@example.com
2. Editor logs in and:
   - Can create/edit family events ✓
   - Cannot delete family or manage members ✗
3. Viewer logs in and:
   - Can view all family events ✓
   - Cannot create/edit events ✗
   - Cannot access family settings ✗
4. Test permission boundaries
```

**Expected Results**:
- Role-based access properly enforced
- Editor can modify events but not family settings
- Viewer has read-only access to events
- Unauthorized actions return appropriate errors

### Story 5: Calendar Export Integration

**Test Scenario**: Export events to external calendar

```bash
# Browser Test Flow
1. Create multiple events (lunar and gregorian)
2. Navigate to export section
3. Export to ICS format:
   - Single event export
   - Full calendar export
   - Family calendar export
4. Import ICS into Google Calendar
5. Verify events appear correctly with proper dates
```

**API Test**:
```bash
# Export single event
curl -X GET "http://localhost:3000/api/v1/calendar/export?event_id=<event_id>" \
  -H "Authorization: Bearer <token>" \
  -H "Accept: text/calendar"

# Export family calendar
curl -X GET "http://localhost:3000/api/v1/calendar/export?family_id=<family_id>" \
  -H "Authorization: Bearer <token>" \
  -H "Accept: text/calendar"
```

**Expected Results**:
- ICS files generated with proper RFC5545 format
- Lunar events converted to gregorian dates in export
- Recurring events properly formatted
- Calendar imports successfully into external apps

### Story 6: PWA Installation and Offline Usage

**Test Scenario**: Install PWA and test offline functionality

```bash
# PWA Installation Test Flow
1. Visit app in Chrome/Edge/Safari
2. Look for install prompt or install icon
3. Install app to desktop/home screen
4. Launch installed PWA app
5. Verify native app-like experience (no browser UI)
6. Test app icon, splash screen, theme colors

# Offline Functionality Test
1. Open installed PWA app while online
2. Browse calendar, view events
3. Disconnect internet connection
4. Verify calendar still loads from cache
5. Create new events while offline
6. Modify existing events while offline
7. Reconnect internet
8. Verify offline changes sync automatically
9. Check for conflict resolution if needed
```

**Expected Results**:
- PWA installs successfully on all platforms
- App functions identically to native app
- Complete offline calendar access
- Background sync when connectivity restored
- Push notifications work even when browser closed

### Story 7: PWA Push Notifications

**Test Scenario**: Test push notifications in PWA context

```bash
# PWA Push Notification Test Flow
1. Install PWA app
2. Grant notification permissions when prompted
3. Create event with reminder notification
4. Close PWA app completely
5. Wait for scheduled notification time
6. Verify push notification appears
7. Click notification to open PWA app
8. Verify app opens to relevant event

# API Test for Push Registration
curl -X POST http://localhost:3000/api/v1/pwa/push-subscription \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "endpoint": "https://fcm.googleapis.com/fcm/send/...",
    "keys": {
      "p256dh": "key_data",
      "auth": "auth_data"
    }
  }'
```

**Expected Results**:
- Push notifications work even when PWA app is closed
- Notifications contain relevant event information
- Clicking notification opens PWA to correct page
- Multiple notification channels supported (reminder types)

### Story 8: Google OAuth Authentication and Account Linking

**Test Scenario**: Test Google OAuth login and account linking functionality

```bash
# Google OAuth Authentication Test Flow
1. Visit app home page
2. Click "Sign in with Google" button
3. Redirected to Google OAuth consent screen
4. Grant calendar permissions (for Google Calendar sync)
5. Redirected back to app with authenticated session
6. Verify user profile populated from Google account
7. Test automatic Google Calendar sync integration

# Account Linking Test Flow
1. Create account with email/password
2. Login with email/password
3. Go to account settings
4. Click "Link Google Account"
5. Complete Google OAuth flow
6. Verify account now has both authentication methods
7. Test login works with both email/password AND Google OAuth

# API Test for Account Linking
curl -X POST http://localhost:3000/api/v1/auth/link-google \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "google_code": "google_oauth_code_from_callback"
  }'

# API Test for Google Calendar Sync (using same OAuth token)
curl -X GET http://localhost:3000/api/v1/calendar/google-sync \
  -H "Authorization: Bearer <token>"
```

**Expected Results**:
- Google OAuth flow works seamlessly
- User accounts created automatically for new Google users
- Account linking allows both authentication methods
- Google Calendar sync uses same OAuth tokens
- PWA works identically with Google OAuth authentication
- Family invitations work with Google accounts

## Integration Test Checklist

### Lunar Calendar Accuracy
- [ ] Lunar-to-gregorian conversion for current year
- [ ] Leap month handling (test with known leap month years)
- [ ] Annual recurrence across multiple years
- [ ] Monthly recurrence calculation
- [ ] Edge cases: lunar month boundaries, year transitions

### Multi-Channel Notifications
- [ ] Email notifications sent via ActionMailer
- [ ] SMS notifications via Twilio adapter
- [ ] Push notifications for web browsers
- [ ] Notification failure handling and retries
- [ ] Delivery status tracking

### Family Collaboration
- [ ] User can belong to multiple families
- [ ] Role permissions enforced at API and UI level
- [ ] Family invitation system with tokens
- [ ] Event visibility scoped by family membership
- [ ] Concurrent editing conflict resolution

### Data Export/Import
- [ ] ICS export matches RFC5545 standard
- [ ] CSV export includes all event data
- [ ] JSON export preserves metadata
- [ ] Import handles invalid data gracefully
- [ ] Google Calendar sync authentication

### Performance Requirements
- [ ] Event creation < 200ms response time
- [ ] Calendar view loads < 2 seconds
- [ ] Lunar date conversion < 50ms
- [ ] Bulk operations handle 1000+ events
- [ ] Database queries optimized with indexes

### PWA Compliance
- [ ] Lighthouse PWA score > 90
- [ ] Web App Manifest properly configured
- [ ] Service Worker registers and functions
- [ ] App installs on all major platforms (iOS, Android, Desktop)
- [ ] Offline functionality works completely
- [ ] Background sync operates when connection restored
- [ ] Push notifications work when app closed
- [ ] App icon and splash screen display correctly
- [ ] Standalone display mode functions properly

### PWA Offline Capabilities
- [ ] Calendar views cached and accessible offline
- [ ] Events can be created offline and sync later
- [ ] Lunar calendar data precomputed for offline use
- [ ] IndexedDB storage works for offline events
- [ ] Background sync resolves conflicts properly
- [ ] Offline indicator shows connection status
- [ ] Service Worker updates without breaking app

## Error Scenarios

### Authentication & Authorization
```bash
# Test unauthorized access
curl -X GET http://localhost:3000/api/v1/events
# Expected: 401 Unauthorized

# Test insufficient permissions
curl -X DELETE http://localhost:3000/api/v1/families/<id> \
  -H "Authorization: Bearer <viewer_token>"
# Expected: 403 Forbidden
```

### Data Validation
```bash
# Test invalid lunar date
curl -X POST http://localhost:3000/api/v1/events \
  -H "Authorization: Bearer <token>" \
  -d '{
    "title": "Test Event",
    "date_type": "lunar",
    "lunar_date": {
      "month": 13,
      "day": 32
    }
  }'
# Expected: 422 Validation Error
```

### External Service Failures
- Calendar export service unavailable
- Email delivery service timeout
- SMS service rate limits
- Database connection failures

## Success Criteria

✅ **All user stories complete successfully**  
✅ **All API endpoints return expected responses**  
✅ **All integration tests pass**  
✅ **Performance benchmarks met**  
✅ **Error scenarios handled gracefully**  
✅ **External calendar integration works**  

## Continuous Validation

```bash
# Run full test suite
bundle exec rspec

# Run integration tests only
bundle exec rspec spec/features/

# Run API contract tests
bundle exec rspec spec/requests/

# Performance benchmarks
bundle exec rake performance:benchmark

# Lunar calendar accuracy tests
bundle exec rspec spec/lib/lunar_calendar_spec.rb
```

## Troubleshooting

### Common Issues

**Lunar date conversion errors**:
- Check lunardate gem version
- Verify timezone handling
- Test with known reference dates

**Notification delivery failures**:
- Check background job queue status
- Verify third-party service credentials
- Monitor rate limits and quotas

**Calendar export issues**:
- Validate ICS format with online validators
- Check timezone information in exports
- Test with multiple calendar applications

**Performance issues**:
- Monitor database query performance
- Check background job queue health
- Profile memory usage during peak loads

This quickstart guide serves as both documentation and an executable test suite to validate the core functionality of the Lunar Reminders App.