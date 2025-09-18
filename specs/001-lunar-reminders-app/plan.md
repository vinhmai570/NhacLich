# Implementation Plan: Lunar Reminders App

**Branch**: `001-lunar-reminders-app` | **Date**: 2025-09-09 | **Spec**: [spec.md](/Users/mfv-computer-0213/per/NhacLich/specs/001-lunar-reminders-app/spec.md)
**Input**: Feature specification from `/specs/001-lunar-reminders-app/spec.md`

## Execution Flow (/plan command scope)
```
1. Load feature spec from Input path
   → If not found: ERROR "No feature spec at {path}"
2. Fill Technical Context (scan for NEEDS CLARIFICATION)
   → Detect Project Type from context (web=frontend+backend, mobile=app+api)
   → Set Structure Decision based on project type
3. Evaluate Constitution Check section below
   → If violations exist: Document in Complexity Tracking
   → If no justification possible: ERROR "Simplify approach first"
   → Update Progress Tracking: Initial Constitution Check
4. Execute Phase 0 → research.md
   → If NEEDS CLARIFICATION remain: ERROR "Resolve unknowns"
5. Execute Phase 1 → contracts, data-model.md, quickstart.md, agent-specific template file (e.g., `CLAUDE.md` for Claude Code, `.github/copilot-instructions.md` for GitHub Copilot, or `GEMINI.md` for Gemini CLI).
6. Re-evaluate Constitution Check section
   → If new violations: Refactor design, return to Phase 1
   → Update Progress Tracking: Post-Design Constitution Check
7. Plan Phase 2 → Describe task generation approach (DO NOT create tasks.md)
8. STOP - Ready for /tasks command
```

**IMPORTANT**: The /plan command STOPS at step 7. Phases 2-4 are executed by other commands:
- Phase 2: /tasks command creates tasks.md
- Phase 3-4: Implementation execution (manual or via tools)

## Summary
Progressive Web App (PWA) lunar calendar supporting event management with automatic lunar-to-gregorian conversion, multi-channel notifications (email/SMS/push), family collaboration with role-based permissions, expense tracking, and third-party calendar integration. Built using Rails 8 with the complete Solid Stack for zero-dependency backend architecture, enhanced with PWA features for offline functionality, native app-like experience, and cross-platform installation. Combines PostgreSQL as single data store with IndexedDB for offline PWA capabilities.

## Technical Context
**Language/Version**: Ruby 3.3+, JavaScript ES6+  
**Primary Dependencies**: Ruby on Rails 8.0, PostgreSQL 16+, FullCalendar JS, Hotwire (Turbo + Stimulus), Tailwind CSS, Solid Stack (Solid Queue, Solid Cache, Solid Cable), PWA features (Service Worker, Web App Manifest)  
**Storage**: PostgreSQL with JSONB for flexible event metadata, Solid Cache for session/cache, IndexedDB for offline PWA data  
**Testing**: RSpec for Ruby, Jest for JavaScript, Capybara for integration tests, Lighthouse for PWA audits  
**Target Platform**: Progressive Web App (PWA) with native app-like experience, installable on mobile/desktop, offline-capable
**Project Type**: web + pwa - determines frontend + backend structure with PWA capabilities  
**Performance Goals**: <200ms event creation response, <2s calendar view load, 1000+ concurrent users, PWA performance score >90  
**Constraints**: <200ms p95 response time, full offline calendar functionality, accurate lunar calendar calculations, native app UX  
**Scale/Scope**: 50k+ users initially, multi-tenant families/groups, international timezone support, cross-platform PWA installation

## Constitution Check
*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

**Simplicity**:
- Projects: 1 (Rails app with frontend/backend combined)
- Using framework directly? YES (Rails conventions, no unnecessary abstractions)
- Single data model? YES (ActiveRecord models, no DTOs for same-format data)
- Avoiding patterns? YES (Rails patterns preferred over Repository/UoW)

**Architecture**:
- EVERY feature as library? YES (Rails engines/gems for reusable components)
- Libraries listed: LunarCalendar (date conversion), NotificationQueue (multi-channel), EventRecurrence (scheduling logic)
- CLI per library: Rails generators and rake tasks with --help
- Library docs: llms.txt format planned? YES

**Testing (NON-NEGOTIABLE)**:
- RED-GREEN-Refactor cycle enforced? YES (RSpec tests written first)
- Git commits show tests before implementation? YES (TDD workflow)
- Order: Contract→Integration→E2E→Unit strictly followed? YES
- Real dependencies used? YES (PostgreSQL test DB, real email adapters)
- Integration tests for: lunar calendar accuracy, notification delivery, role permissions, calendar export
- FORBIDDEN: Implementation before test, skipping RED phase ✓

**Observability**:
- Structured logging included? YES (Rails structured logging + custom events)
- Frontend logs → backend? YES (Stimulus controllers log to backend)
- Error context sufficient? YES (detailed error tracking with context)

**Versioning**:
- Version number assigned? YES (1.0.0 initial release)
- BUILD increments on every change? YES (automated via CI/CD)
- Breaking changes handled? YES (API versioning, database migrations)

## Project Structure

### Documentation (this feature)
```
specs/[###-feature]/
├── plan.md              # This file (/plan command output)
├── research.md          # Phase 0 output (/plan command)
├── data-model.md        # Phase 1 output (/plan command)
├── quickstart.md        # Phase 1 output (/plan command)
├── contracts/           # Phase 1 output (/plan command)
└── tasks.md             # Phase 2 output (/tasks command - NOT created by /plan)
```

### Source Code (repository root)
```
# Option 1: Single project (DEFAULT)
src/
├── models/
├── services/
├── cli/
└── lib/

tests/
├── contract/
├── integration/
└── unit/

# Option 2: Web application (when "frontend" + "backend" detected)
backend/
├── src/
│   ├── models/
│   ├── services/
│   └── api/
└── tests/

frontend/
├── src/
│   ├── components/
│   ├── pages/
│   └── services/
└── tests/

# Option 3: Mobile + API (when "iOS/Android" detected)
api/
└── [same as backend above]

ios/ or android/
└── [platform-specific structure]
```

**Structure Decision**: Option 2: Web application (Rails app with separate frontend/backend concerns)

## Phase 0: Outline & Research
1. **Extract unknowns from Technical Context** above:
   - For each NEEDS CLARIFICATION → research task
   - For each dependency → best practices task
   - For each integration → patterns task

2. **Generate and dispatch research agents**:
   ```
   For each unknown in Technical Context:
     Task: "Research {unknown} for {feature context}"
   For each technology choice:
     Task: "Find best practices for {tech} in {domain}"
   ```

3. **Consolidate findings** in `research.md` using format:
   - Decision: [what was chosen]
   - Rationale: [why chosen]
   - Alternatives considered: [what else evaluated]

**Output**: research.md with all NEEDS CLARIFICATION resolved

## Phase 1: Design & Contracts
*Prerequisites: research.md complete*

1. **Extract entities from feature spec** → `data-model.md`:
   - Entity name, fields, relationships
   - Validation rules from requirements
   - State transitions if applicable

2. **Generate API contracts** from functional requirements:
   - For each user action → endpoint
   - Use standard REST/GraphQL patterns
   - Output OpenAPI/GraphQL schema to `/contracts/`

3. **Generate contract tests** from contracts:
   - One test file per endpoint
   - Assert request/response schemas
   - Tests must fail (no implementation yet)

4. **Extract test scenarios** from user stories:
   - Each story → integration test scenario
   - Quickstart test = story validation steps

5. **Update agent file incrementally** (O(1) operation):
   - Run `/scripts/update-agent-context.sh [claude|gemini|copilot]` for your AI assistant
   - If exists: Add only NEW tech from current plan
   - Preserve manual additions between markers
   - Update recent changes (keep last 3)
   - Keep under 150 lines for token efficiency
   - Output to repository root

**Output**: data-model.md, /contracts/*, failing tests, quickstart.md, agent-specific file

## Phase 2: Task Planning Approach
*This section describes what the /tasks command will do - DO NOT execute during /plan*

**Task Generation Strategy**:
- Load `/templates/tasks-template.md` as base
- Generate tasks from Phase 1 design docs (contracts, data model, quickstart)
- Each contract → contract test task [P]
- Each entity → model creation task [P] 
- Each user story → integration test task
- Implementation tasks to make tests pass

**Ordering Strategy**:
- TDD order: Tests before implementation 
- Dependency order: Models before services before UI
- Mark [P] for parallel execution (independent files)

**Estimated Output**: 25-30 numbered, ordered tasks in tasks.md

**IMPORTANT**: This phase is executed by the /tasks command, NOT by /plan

## Implementation Sequence Map
*Cross-references between phases and detailed documentation*

### Phase 3.2 Tests (T007-T029) → Detail Sources:
- **Contract tests**: `contracts/api-spec.yml` endpoints - each endpoint maps to specific test task
- **Integration tests**: `quickstart.md:103-372` user stories - each story validates implementation tasks
- **CRITICAL**: Must complete and FAIL before Phase 3.3 (TDD workflow enforced)

### Phase 3.3 Models (T030-T051) → Detail Sources:
- **Entity specifications**: `data-model.md:20-342` - complete field definitions, validations, relationships
- **Database design**: `data-model.md:344-423` - indexes, constraints, performance optimizations
- **Required for**: Services in Phase 3.4 depend on model completion

### Phase 3.4 Services (T052-T061) → Detail Sources:
- **Service decisions**: `research.md:7-311` - technology choices and implementation approaches
- **Implementation patterns**: `data-model.md` relationships guide service interactions
- **API integration**: `contracts/api-spec.yml` schemas define service interfaces

### Phase 3.5 Controllers (T062-T070) → Detail Sources:
- **API contracts**: `contracts/api-spec.yml` - exact request/response specifications
- **Authorization**: `data-model.md:105-126` (Membership roles) guides Pundit policies
- **Test validation**: Integration tests T022-T029 validate controller behavior

### Phase 3.6 Frontend/PWA (T071-T079) → Detail Sources:
- **PWA features**: `research.md:219-280` - offline strategy, service worker implementation
- **User journeys**: `quickstart.md:103-372` - test scenarios validate frontend behavior
- **API integration**: Controllers T062-T070 provide data endpoints

### Phase 3.4 Implementation Refinements → Critical Missing Elements:
- **Controllers (T062-T070)**: Each requires endpoint mapping to `contracts/api-spec.yml`, authentication integration, service dependencies, and JSON serialization
- **Background Jobs (T058-T061)**: Each requires perform method signature, error handling, retry strategies, and database interaction patterns
- **Routes Configuration**: Missing task for config/routes.rb mapping all 25+ API endpoints from contracts
- **JSON Serializers**: Missing tasks for API response formatting (UserSerializer, EventSerializer, etc.)
- **View Layer Integration**: T078 requires detailed Turbo Frame implementation with controller integration

## Phase 3+: Future Implementation
*These phases are beyond the scope of the /plan command*

**Phase 3**: Task execution (/tasks command creates tasks.md)  
**Phase 4**: Implementation (execute tasks.md following constitutional principles)  
**Phase 5**: Validation (run tests, execute quickstart.md, performance validation)

## Complexity Tracking
*Fill ONLY if Constitution Check has violations that must be justified*

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| [e.g., 4th project] | [current need] | [why 3 projects insufficient] |
| [e.g., Repository pattern] | [specific problem] | [why direct DB access insufficient] |


## Progress Tracking
*This checklist is updated during execution flow*

**Phase Status**:
- [x] Phase 0: Research complete (/plan command)
- [x] Phase 1: Design complete (/plan command)
- [x] Phase 2: Task planning complete (/plan command - describe approach only)
- [ ] Phase 3: Tasks generated (/tasks command)
- [ ] Phase 4: Implementation complete
- [ ] Phase 5: Validation passed

**Gate Status**:
- [x] Initial Constitution Check: PASS
- [x] Post-Design Constitution Check: PASS
- [x] All NEEDS CLARIFICATION resolved
- [ ] Complexity deviations documented (none required)

---
*Based on Constitution v2.1.1 - See `/memory/constitution.md`*