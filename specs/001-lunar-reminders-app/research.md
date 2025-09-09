# Research: Lunar Reminders App

**Date**: 2025-09-09
**Phase**: 0 - Research & Analysis (Updated for Rails 8 + Solid Stack)

## Research Areas

### 1. Lunar Calendar Conversion Library

**Decision**: Use `lunardate` Ruby gem with custom Vietnamese lunar calendar extensions
**Rationale**:
- Accurate lunar-to-gregorian conversion algorithms
- Handles leap months correctly
- Supports Vietnamese lunar calendar specifics
- Can be extended for custom business logic

**Alternatives considered**:
- Build from scratch: Too complex, high maintenance
- JavaScript lunar libraries: Limited server-side processing
- Chinese lunar calendar libs: Different rules from Vietnamese system

**Implementation notes**:
- Gem: `lunardate` for core calculations
- Custom service layer for Vietnamese-specific rules
- Precompute recurring events to avoid runtime calculations

### 2. Multi-Channel Notifications

**Decision**: Rails ActionMailer + Sidekiq + third-party SMS/push services
**Rationale**:
- ActionMailer handles email templating and delivery
- Sidekiq provides reliable background job processing
- Modular design allows adding new notification channels
- Built-in retry mechanisms and failure handling

**Alternatives considered**:
- Rails 8 Solid Queue: Simpler but less mature than Sidekiq
- Direct API calls: No retry mechanisms, poor reliability
- All-in-one services: Vendor lock-in, expensive

**Implementation notes**:
- Email: ActionMailer with SendGrid/Mailgun
- SMS: Twilio API integration
- Push: Web Push Protocol for browsers
- Zalo OA: Custom adapter using their API

### 3. Calendar Integration & Export

**Decision**: ICS generation with Ruby `icalendar` gem + Google/Outlook API integration
**Rationale**:
- ICS is universal standard for calendar data
- Icalendar gem handles RFC5545 compliance
- Direct API integration for two-way sync
- Users can import/export to any calendar app

**Alternatives considered**:
- CalDAV protocol: More complex, limited client support
- Only export: Users want bidirectional sync
- CSV export only: Not standard for calendar apps

**Implementation notes**:
- Gem: `icalendar` for ICS generation
- Google Calendar API v3 for sync
- Microsoft Graph API for Outlook
- OAuth2 for secure authentication

### 4. Frontend Calendar Component

**Decision**: FullCalendar JS with Rails Hotwire integration
**Rationale**:
- FullCalendar is mature, feature-rich calendar library
- Supports lunar date display with custom rendering
- Integrates well with Turbo Frames for dynamic updates
- Mobile-responsive out of the box

**Alternatives considered**:
- Build custom calendar: Massive development effort
- Hotwire-only solution: Limited calendar UI capabilities
- React/Vue components: Adds complexity to Rails stack

**Implementation notes**:
- FullCalendar v6 with TimeGrid and DayGrid views
- Custom lunar date plugin for date conversion display
- Stimulus controllers for Rails integration
- Turbo Frames for event creation/editing

### 5. Family Collaboration & Permissions

**Decision**: Rails built-in role-based access control with multi-tenancy
**Rationale**:
- Rails has mature authorization patterns
- Multi-tenant architecture supports family groups
- Role-based permissions are well-understood pattern
- Can leverage Rails associations for data scoping

**Alternatives considered**:
- Third-party auth services: Unnecessary complexity
- Simple boolean permissions: Too limited for family hierarchies
- ACL gems: Overkill for straightforward role system

**Implementation notes**:
- Models: User, Family, Membership with role enum
- Pundit gem for authorization policies
- Row-level security with family_id scoping
- Invitation system with signed tokens

### 6. Expense Tracking

**Decision**: Simple Rails models with JSON metadata storage
**Rationale**:
- PostgreSQL JSONB for flexible expense categories
- Rails Money gem for currency handling
- Simple reporting with ActiveRecord aggregations
- No complex accounting features needed initially

**Alternatives considered**:
- Full accounting system: Over-engineered for use case
- Separate microservice: Unnecessary complexity
- Third-party service: Integration overhead

**Implementation notes**:
- Models: Expense belongs_to Event
- JSONB fields for flexible categorization
- Money gem for proper currency handling
- Simple reporting views with Rails

### 7. Rails 8 Solid Stack Integration

**Decision**: Full Rails 8 Solid Stack (Solid Queue, Solid Cache, Solid Cable)
**Rationale**:
- Native Rails 8 integrated solution, zero external dependencies
- PostgreSQL-backed for all caching, jobs, and real-time features
- Eliminates Redis dependency completely
- Built-in monitoring and admin interfaces
- Optimized for Rails 8 performance improvements

**Alternatives considered**:
- Sidekiq + Redis: More mature but adds infrastructure complexity
- ActionCable with Redis: Requires separate Redis instance
- Mixed approach: Inconsistent and more complex

**Rails 8 Solid Stack Components**:
- **Solid Queue**: Background job processing with PostgreSQL storage
- **Solid Cache**: Database-backed caching replacing Redis cache
- **Solid Cable**: Real-time WebSocket connections via PostgreSQL
- Enhanced Kamal deployment with built-in health checks
- Built-in authentication generators and improvements

**Implementation notes**:
- Solid Queue for notification delivery jobs
- Solid Cache for session storage and event caching
- Solid Cable for real-time calendar updates
- Single PostgreSQL database for all persistence needs
- Simplified deployment with Kamal 2.0

### 8. Rails 8 New Features Integration

**Decision**: Leverage Rails 8 authentication and performance improvements
**Rationale**:
- Built-in authentication system reduces third-party dependencies
- Improved Hotwire integration for better SPA-like experience
- Enhanced Active Record performance for complex queries
- Better PostgreSQL support for JSONB operations

**Rails 8 Features to Utilize**:
- **Built-in Authentication**: Replace Devise with Rails 8 authentication generators
- **Enhanced Turbo**: Better morphing and frame updates for calendar interactions
- **Improved Active Job**: Better error handling and retry mechanisms
- **PostgreSQL Enhancements**: Improved JSONB query performance
- **Kamal 2.0**: Simplified deployment with health checks and zero-downtime deployments

**Implementation notes**:
- Use `rails generate authentication` for user system with Google OAuth integration
- Add `omniauth-google-oauth2` gem for Google Sign-In
- Leverage enhanced Turbo morphing for calendar updates
- Utilize improved JSONB operators for lunar date queries
- Deploy with Kamal 2.0 for production scaling

### 9. Testing Strategy

**Decision**: RSpec + FactoryBot + Capybara integration tests
**Rationale**:
- RSpec is Rails testing standard
- FactoryBot for flexible test data generation
- Capybara for full-stack integration tests
- Real database testing for data integrity

**Alternatives considered**:
- Minitest: Less expressive for complex scenarios
- Mocking external services: Hides integration issues
- Unit tests only: Misses integration problems

**Implementation notes**:
- RSpec for unit/integration tests
- FactoryBot for test data
- Capybara + Selenium for browser tests
- VCR for external API testing
- Database cleaner for test isolation

## Resolution Summary

All technical unknowns have been researched and resolved with specific implementation decisions. The architecture leverages Rails 8's new Solid Stack for a zero-dependency, PostgreSQL-only solution with enhanced performance and simplified deployment.

**Key Decisions**:
1. Ruby on Rails 8.0 with PostgreSQL 16+ (single database for all needs)
2. Full Solid Stack: Solid Queue, Solid Cache, Solid Cable
3. Rails 8 built-in authentication (no Devise dependency)
4. Hotwire + FullCalendar JS with enhanced Turbo morphing
5. Kamal 2.0 for simplified production deployment
6. TDD with RSpec + Capybara integration testing

**Rails 8 Advantages**:
- Zero external dependencies (no Redis, no separate job queue)
- Simplified infrastructure and deployment
- Enhanced performance with PostgreSQL optimizations
- Built-in monitoring and admin interfaces
- Better real-time capabilities with Solid Cable

### 10. Progressive Web App (PWA) Integration

**Decision**: Full PWA implementation with Rails 8 + Hotwire + Service Worker  
**Rationale**:
- Native app-like experience without app store dependencies
- Offline calendar access crucial for family event planning
- Push notifications work seamlessly with existing notification system
- Cross-platform installation (iOS, Android, desktop)
- Superior performance with Service Worker caching
- Background sync capabilities for when users return online

**PWA Features to Implement**:
- **Web App Manifest**: App metadata, icons, display modes, theme colors
- **Service Worker**: Offline functionality, background sync, push notifications
- **IndexedDB**: Client-side storage for offline calendar data
- **Cache-First Strategy**: Assets and calendar views cached for performance
- **Background Sync**: Sync pending events when connection restored
- **Install Prompt**: Native installation experience
- **Responsive Design**: Optimized for mobile, tablet, desktop

**Alternatives considered**:
- Native mobile apps: Higher development/maintenance cost, app store approval
- Hybrid frameworks: Added complexity, performance overhead
- Web-only: Limited offline capability, no installation

**Implementation notes**:
- Rails PWA gem for manifest generation
- Custom Service Worker with Hotwire integration
- IndexedDB for offline event storage and lunar date caching
- Background sync for notification scheduling and event updates
- WebPush API integration with existing notification system
- Lighthouse audits for PWA performance optimization

**Rails 8 + PWA Integration Benefits**:
- Solid Cache provides server-side caching for PWA assets
- Solid Cable enables real-time sync when online
- Hotwire Turbo works seamlessly with Service Worker
- Rails asset pipeline optimized for PWA manifest generation

## Resolution Summary

All technical unknowns have been researched and resolved with specific implementation decisions. The architecture leverages Rails 8's new Solid Stack for a zero-dependency backend with comprehensive PWA capabilities for native app-like experience and offline functionality.

**Key Decisions**:
1. Ruby on Rails 8.0 with PostgreSQL 16+ (single database for all needs)
2. Full Solid Stack: Solid Queue, Solid Cache, Solid Cable
3. Complete PWA implementation with Service Worker and offline capabilities
4. Rails 8 built-in authentication (no Devise dependency)
5. Hotwire + FullCalendar JS with enhanced Turbo morphing and PWA integration
6. IndexedDB for offline data storage with background sync
7. Kamal 2.0 for simplified production deployment
8. TDD with RSpec + Capybara + Lighthouse PWA audits

**Rails 8 + PWA Advantages**:
- Zero external backend dependencies (no Redis, no separate services)
- Native app experience without app store complexity
- Complete offline functionality for calendar access
- Background sync when connectivity restored
- Cross-platform installation (iOS, Android, desktop)
- Push notifications integrated with server-side notification system
- Superior performance with Service Worker + Solid Cache combination

### 11. Authentication Strategy Enhancement

**Decision**: Rails 8 built-in authentication + Google OAuth integration  
**Rationale**:
- Rails 8 authentication provides solid foundation with password-based auth
- Google OAuth reduces friction for user onboarding
- Family collaboration benefits from Google account integration
- Calendar sync with Google Calendar more seamless with same auth
- PWA installation prompts work better with recognized Google accounts

**Authentication Methods**:
- **Primary**: Google OAuth 2.0 with automatic account creation
- **Secondary**: Email/password with Rails 8 built-in system
- **Account Linking**: Allow users to link both methods to same account
- **Family Invitations**: Invite via Google accounts or email addresses

**Implementation notes**:
- `omniauth-google-oauth2` gem for Google Sign-In integration
- Rails 8 authentication generators for password-based backup auth
- User model supports both authentication methods
- Seamless account linking and merging functionality
- Google Calendar API integration leverages same OAuth tokens

**Security Benefits**:
- Google's robust security infrastructure
- Two-factor authentication through Google accounts
- Reduced password management burden on users
- OAuth token management for calendar sync integration

**Next Phase**: Proceed to Phase 1 design and contracts generation.
