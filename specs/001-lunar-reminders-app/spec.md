# Feature Specification: Lunar Reminders App

**Feature Branch**: `001-lunar-reminders-app`  
**Created**: 2025-09-09  
**Status**: Draft  
**Input**: User description: "Lunar Reminders App — Structured Feature Specs (EN)"

## Execution Flow (main)
```
1. Parse user description from Input
   → ✓ Complete feature description provided
2. Extract key concepts from description
   → ✓ Identified: families/individuals/planners, lunar/gregorian events, multi-channel notifications
3. For each unclear aspect:
   → ✓ Marked unclear billing models and specific technical integrations
4. Fill User Scenarios & Testing section
   → ✓ Clear user flows identified for all personas
5. Generate Functional Requirements
   → ✓ Each requirement is testable and specific
6. Identify Key Entities (if data involved)
   → ✓ 9 key entities identified with relationships
7. Run Review Checklist
   → ✓ No implementation details, focused on user needs
8. Return: SUCCESS (spec ready for planning)
```

---

## ⚡ Quick Guidelines
- ✅ Focus on WHAT users need and WHY
- ❌ Avoid HOW to implement (no tech stack, APIs, code structure)
- 👥 Written for business stakeholders, not developers

### Section Requirements
- **Mandatory sections**: Must be completed for every feature
- **Optional sections**: Include only when relevant to the feature
- When a section doesn't apply, remove it entirely (don't leave as "N/A")

### For AI Generation
When creating this spec from a user prompt:
1. **Mark all ambiguities**: Use [NEEDS CLARIFICATION: specific question] for any assumption you'd need to make
2. **Don't guess**: If the prompt doesn't specify something (e.g., "login system" without auth method), mark it
3. **Think like a tester**: Every vague requirement should fail the "testable and unambiguous" checklist item
4. **Common underspecified areas**:
   - User types and permissions
   - Data retention/deletion policies  
   - Performance targets and scale
   - Error handling behaviors
   - Integration requirements
   - Security/compliance needs

---

## User Scenarios & Testing *(mandatory)*

### Primary User Story
A family organizer creates a family calendar to track important lunar dates like memorial days (giỗ), birthdays, and ceremonies. They set up automatic notifications to be sent to multiple family members via email, SMS, and push notifications at configurable lead times (days, weeks, or months before events). The system automatically converts lunar dates to gregorian equivalents each year, handling leap months correctly.

### Acceptance Scenarios

**Scenario 1: Create Annual Lunar Memorial Event**
1. **Given** a family organizer is logged in with a family group created, **When** they create a memorial event on lunar date 15/07 with annual recurrence, **Then** the system converts it to the correct gregorian date and schedules yearly recurring reminders

**Scenario 2: Monthly Ritual Notifications**
2. **Given** an individual user has configured a monthly ritual on lunar day 10, **When** the lunar calendar advances, **Then** the system sends reminders 1-2 days before at the specified time (e.g., 07:00)

**Scenario 3: Long-term Event Planning**
3. **Given** an event planner creates a client event with 3-month lead notification, **When** the reminder time arrives, **Then** all specified recipients receive notifications via their chosen channels (email/SMS/push)

**Scenario 4: Family Collaboration**
4. **Given** a family group with Owner/Editor/Viewer roles, **When** an Editor adds a birthday event, **Then** all members can view the event but only Editors and Owners can modify it

**Scenario 5: Calendar Integration Export**
5. **Given** a user has created multiple events, **When** they export to ICS format, **Then** they can successfully import the events into Google Calendar, Outlook, or Apple Calendar

### Edge Cases
- What happens when a lunar date falls in a leap month and needs to carry over to the next year?
- How does system handle notification failures (email bounce, SMS delivery failure)?
- What occurs when multiple family members try to edit the same event simultaneously?
- How are timezone differences handled for recipients in different locations?
- What happens when external calendar sync fails or returns conflicts?

## Requirements *(mandatory)*

### Functional Requirements

**Core Calendar & Event Management**
- **FR-001**: System MUST support creating events based on lunar calendar dates with automatic gregorian conversion
- **FR-002**: System MUST handle annual recurrence of lunar events with correct leap month calculations
- **FR-003**: System MUST support monthly recurrence on specific lunar days (e.g., day 10 of each lunar month)
- **FR-004**: System MUST allow creating gregorian-based events with standard recurrence options
- **FR-005**: System MUST provide calendar views showing both lunar and gregorian date labels
- **FR-006**: System MUST support quick event creation by tapping calendar dates (Flow A) and detailed creation via Add button (Flow B)

**Multi-Channel Notifications**
- **FR-007**: System MUST send notifications via push notifications, email, SMS, and [NEEDS CLARIFICATION: Zalo OA integration requirements and costs]
- **FR-008**: System MUST support configurable lead times from days to 6 months before events
- **FR-009**: System MUST allow multiple notification schedules per event (e.g., 1 month, 1 week, 2 days, 1 day)
- **FR-010**: System MUST send notifications at user-specified times of day per recipient group
- **FR-011**: System MUST support multiple email addresses and phone numbers per event
- **FR-012**: System MUST allow creation and reuse of recipient groups (e.g., "Paternal family")

**Family/Group Collaboration**
- **FR-013**: System MUST support family/group spaces with multiple member access
- **FR-014**: System MUST enforce role-based permissions (Owner/Editor/Viewer) for all operations
- **FR-015**: System MUST allow inviting members via email, phone, or shareable links
- **FR-016**: System MUST support family tree creation with parent/child/spouse relationships
- **FR-017**: System MUST store member profiles including name, photo, lunar/gregorian birthdays, and biographical information
- **FR-018**: System MUST track memorial site locations and related information

**Expense & Cost Tracking**
- **FR-019**: System MUST allow recording ceremony expenses with items, amounts, and categories
- **FR-020**: System MUST provide yearly expense summaries and statistics per family/group

**Data Integration & Export**
- **FR-021**: System MUST export individual events or full calendars to ICS format
- **FR-022**: System MUST export event data, member information, and expenses to CSV and JSON formats
- **FR-023**: System MUST import events from CSV and ICS files where data mapping is possible
- **FR-024**: System MUST sync with Google Calendar and Microsoft Outlook [NEEDS CLARIFICATION: bi-directional sync or export-only?]

**Billing & Quotas**
- **FR-025**: System MUST provide free tier with [NEEDS CLARIFICATION: specific quotas for push notifications and email]
- **FR-026**: System MUST charge for SMS and premium notification services [NEEDS CLARIFICATION: pricing model - credits vs subscription]
- **FR-027**: System MUST track and enforce channel quotas per user and per group
- **FR-028**: System MUST log notification delivery status and failures for billing accuracy

**User Experience & Accessibility**
- **FR-029**: System MUST support Vietnamese and English localization
- **FR-030**: System MUST provide accessibility features including large font and high contrast modes
- **FR-031**: System MUST show notification preview with calculated send dates and times
- **FR-032**: System MUST warn users of overlapping events within the same group
- **FR-033**: System MUST support event attachments such as ceremony checklists and expense sheets

**Security & Privacy**
- **FR-034**: System MUST implement secure authentication [NEEDS CLARIFICATION: OAuth providers, email/password, or other methods?]
- **FR-035**: System MUST encrypt sensitive data at rest and in transit
- **FR-036**: System MUST provide granular event visibility controls (private/group/public)
- **FR-037**: System MUST obtain explicit consent before accessing device contacts or calendars
- **FR-038**: System MUST handle timezone conversions accurately for all users and notifications

**Performance & Reliability**
- **FR-039**: System MUST create events within 200ms response time
- **FR-040**: System MUST precompute reminder schedules to ensure timely delivery
- **FR-041**: System MUST implement retry mechanisms with backoff for failed notifications
- **FR-042**: System MUST provide audit logs for all user actions and system operations

### Key Entities *(include if feature involves data)*

- **User**: Individual account with personal profile, contact information, locale preferences, and timezone settings
- **Group/Family**: Collaborative space containing multiple users with defined roles and shared events
- **Person**: Genealogy record within a family group, including relationships, biographical data, and memorial information
- **Event**: Calendar item with lunar or gregorian dates, recurrence rules, location, and associated metadata
- **Reminder**: Notification configuration linked to events, specifying lead times, channels, and recipient lists
- **Recipient**: Contact information for notification delivery, can be grouped for easy reuse
- **Expense**: Financial record associated with ceremony events, including itemized costs and categorization
- **Integration Account**: External service credentials for calendar synchronization and data export
- **Audit Log**: System activity record for tracking user actions, data changes, and notification delivery

---

## Review & Acceptance Checklist
*GATE: Automated checks run during main() execution*

### Content Quality
- [x] No implementation details (languages, frameworks, APIs)
- [x] Focused on user value and business needs
- [x] Written for non-technical stakeholders
- [x] All mandatory sections completed

### Requirement Completeness
- [ ] No [NEEDS CLARIFICATION] markers remain (6 areas need clarification)
- [x] Requirements are testable and unambiguous  
- [x] Success criteria are measurable
- [x] Scope is clearly bounded
- [x] Dependencies and assumptions identified

---

## Execution Status
*Updated by main() during processing*

- [x] User description parsed
- [x] Key concepts extracted
- [x] Ambiguities marked (6 clarification points)
- [x] User scenarios defined
- [x] Requirements generated (42 functional requirements)
- [x] Entities identified (9 key entities)
- [ ] Review checklist passed (pending clarification resolution)

---