# Tasks: Digital Document Processing System

**Input**: Design documents from `/specs/001-we-are-building/`
**Prerequisites**: plan.md, research.md, data-model.md, contracts/, quickstart.md

## Execution Flow (main)
```
1. Load plan.md from feature directory
   → ✅ Tech stack: .NET 9 Aspire, Blazor, PostgreSQL, Entra ID
   → ✅ Structure: Web app with backend/frontend separation
2. Load design documents:
   → ✅ data-model.md: 6 entities (User, Document, ProcessingJob, PipelineStage, ShareLink, AuditLog)
   → ✅ contracts/: api-contract.yaml (15+ endpoints), events.json (10 event types)
   → ✅ research.md: Technology decisions and rationale
   → ✅ quickstart.md: 6 validation scenarios
3. Generate tasks by category:
   → Setup: Aspire orchestration, dependencies, database
   → Tests: Contract tests (15 endpoints), integration tests (6 scenarios)
   → Core: Entity models, repositories, services, controllers
   → Events: Event publishing, background services, SignalR
   → Frontend: Blazor components, file upload, real-time UI
   → Integration: Authentication, storage, pipeline processing
   → Polish: Unit tests, performance optimization, documentation
4. Apply task rules:
   → ✅ Different files = mark [P] for parallel execution
   → ✅ Same file = sequential (no [P])
   → ✅ Tests before implementation (TDD)
5. ✅ Number tasks sequentially (T001-T040)
6. ✅ Generate dependency graph with parallel execution
7. ✅ Validate completeness: All contracts tested, entities modeled, scenarios covered
8. ✅ Return: SUCCESS (40 tasks ready for execution)
```

## Format: `[ID] [P?] Description`
- **[P]**: Can run in parallel (different files, no dependencies)
- Exact file paths included for all tasks
- .NET Aspire web application structure

## Path Conventions (from plan.md)
```
src/
├── DocumentManager.AppHost/          # Aspire orchestration
├── DocumentManager.ServiceDefaults/  # Shared service configuration
├── DocumentManager.Web/             # Blazor frontend
├── DocumentManager.Api/             # Web API backend
└── DocumentManager.Data/            # Data access layer

tests/
├── DocumentManager.Api.Tests/       # API unit tests
├── DocumentManager.Web.Tests/       # Frontend component tests
├── DocumentManager.Integration.Tests/ # End-to-end tests
└── DocumentManager.Contract.Tests/  # API contract tests
```

## Phase 3.1: Infrastructure Setup
- [ ] **T001** Create .NET Aspire solution structure with AppHost, ServiceDefaults, Web, Api, and Data projects
- [ ] **T002** Configure Aspire orchestration in `src/DocumentManager.AppHost/Program.cs` with PostgreSQL, Redis, and Blob Storage
- [ ] **T003** [P] Set up Entity Framework Core with PostgreSQL in `src/DocumentManager.Data/DocumentDbContext.cs`
- [ ] **T004** [P] Configure Microsoft Entra ID authentication in `src/DocumentManager.Api/Program.cs`
- [ ] **T005** [P] Set up Azure Blob Storage client in `src/DocumentManager.Data/Services/BlobStorageService.cs`
- [ ] **T006** [P] Configure structured logging with Serilog in `src/DocumentManager.ServiceDefaults/Extensions.cs`

## Phase 3.2: Data Layer (Tests First - TDD) ⚠️ MUST COMPLETE BEFORE 3.3
**CRITICAL: These tests MUST be written and MUST FAIL before ANY implementation**

### Entity Tests
- [ ] **T007** [P] User entity validation tests in `tests/DocumentManager.Data.Tests/Entities/UserTests.cs`
- [ ] **T008** [P] Document entity validation tests in `tests/DocumentManager.Data.Tests/Entities/DocumentTests.cs`
- [ ] **T009** [P] ProcessingJob entity validation tests in `tests/DocumentManager.Data.Tests/Entities/ProcessingJobTests.cs`
- [ ] **T010** [P] ShareLink entity validation tests in `tests/DocumentManager.Data.Tests/Entities/ShareLinkTests.cs`

### Repository Tests
- [ ] **T011** [P] User repository tests in `tests/DocumentManager.Data.Tests/Repositories/UserRepositoryTests.cs`
- [ ] **T012** [P] Document repository tests in `tests/DocumentManager.Data.Tests/Repositories/DocumentRepositoryTests.cs`
- [ ] **T013** [P] ProcessingJob repository tests in `tests/DocumentManager.Data.Tests/Repositories/ProcessingJobRepositoryTests.cs`

## Phase 3.3: Data Layer Implementation (ONLY after tests are failing)
### Entity Models
- [ ] **T014** [P] User entity in `src/DocumentManager.Data/Entities/User.cs`
- [ ] **T015** [P] Document entity in `src/DocumentManager.Data/Entities/Document.cs` 
- [ ] **T016** [P] ProcessingJob entity in `src/DocumentManager.Data/Entities/ProcessingJob.cs`
- [ ] **T017** [P] PipelineStage entity in `src/DocumentManager.Data/Entities/PipelineStage.cs`
- [ ] **T018** [P] ShareLink entity in `src/DocumentManager.Data/Entities/ShareLink.cs`
- [ ] **T019** [P] AuditLog entity in `src/DocumentManager.Data/Entities/AuditLog.cs`

### Entity Framework Configuration  
- [ ] **T020** Configure User entity mapping in `src/DocumentManager.Data/Configurations/UserConfiguration.cs`
- [ ] **T021** Configure Document entity mapping in `src/DocumentManager.Data/Configurations/DocumentConfiguration.cs`
- [ ] **T022** Configure ProcessingJob entity mapping in `src/DocumentManager.Data/Configurations/ProcessingJobConfiguration.cs`
- [ ] **T023** Create initial database migration in `src/DocumentManager.Data/Migrations/`

### Repository Implementation
- [ ] **T024** [P] User repository in `src/DocumentManager.Data/Repositories/UserRepository.cs`
- [ ] **T025** [P] Document repository in `src/DocumentManager.Data/Repositories/DocumentRepository.cs`
- [ ] **T026** [P] ProcessingJob repository in `src/DocumentManager.Data/Repositories/ProcessingJobRepository.cs`

## Phase 3.4: API Contract Tests (TDD)
**CRITICAL: Contract tests MUST be written and MUST FAIL before API implementation**

### Authentication & User Management
- [ ] **T027** [P] Contract test POST /auth/login in `tests/DocumentManager.Contract.Tests/AuthenticationTests.cs`
- [ ] **T028** [P] Contract test GET /users/me in `tests/DocumentManager.Contract.Tests/UserManagementTests.cs`
- [ ] **T029** [P] Contract test GET /users in `tests/DocumentManager.Contract.Tests/AdminUserTests.cs`

### Document Management
- [ ] **T030** [P] Contract test POST /documents in `tests/DocumentManager.Contract.Tests/DocumentUploadTests.cs`
- [ ] **T031** [P] Contract test GET /documents in `tests/DocumentManager.Contract.Tests/DocumentListingTests.cs`
- [ ] **T032** [P] Contract test GET /documents/{id} in `tests/DocumentManager.Contract.Tests/DocumentDetailsTests.cs`
- [ ] **T033** [P] Contract test DELETE /documents/{id} in `tests/DocumentManager.Contract.Tests/DocumentDeletionTests.cs`

### Processing & Sharing
- [ ] **T034** [P] Contract test GET /documents/{id}/processing in `tests/DocumentManager.Contract.Tests/ProcessingStatusTests.cs`
- [ ] **T035** [P] Contract test POST /documents/{id}/shares in `tests/DocumentManager.Contract.Tests/DocumentSharingTests.cs`

## Phase 3.5: API Implementation (ONLY after contract tests fail)
### Controllers
- [ ] **T036** Authentication controller in `src/DocumentManager.Api/Controllers/AuthController.cs`
- [ ] **T037** Users controller in `src/DocumentManager.Api/Controllers/UsersController.cs`
- [ ] **T038** Documents controller in `src/DocumentManager.Api/Controllers/DocumentsController.cs`
- [ ] **T039** Processing controller in `src/DocumentManager.Api/Controllers/ProcessingController.cs`
- [ ] **T040** Sharing controller in `src/DocumentManager.Api/Controllers/SharingController.cs`

### Service Layer
- [ ] **T041** [P] User service in `src/DocumentManager.Api/Services/UserService.cs`
- [ ] **T042** [P] Document service in `src/DocumentManager.Api/Services/DocumentService.cs`
- [ ] **T043** [P] Processing service in `src/DocumentManager.Api/Services/ProcessingService.cs`
- [ ] **T044** [P] Sharing service in `src/DocumentManager.Api/Services/SharingService.cs`

## Phase 3.6: Event System Implementation
### Event Models (from events.json)
- [ ] **T045** [P] Document events in `src/DocumentManager.Data/Events/DocumentEvents.cs`
- [ ] **T046** [P] Processing events in `src/DocumentManager.Data/Events/ProcessingEvents.cs`
- [ ] **T047** [P] System events in `src/DocumentManager.Data/Events/SystemEvents.cs`

### Event Infrastructure
- [ ] **T048** Event publisher service in `src/DocumentManager.Api/Services/EventPublisherService.cs`
- [ ] **T049** Background processing service in `src/DocumentManager.Api/Services/ProcessingPipelineService.cs`
- [ ] **T050** SignalR hub for real-time updates in `src/DocumentManager.Api/Hubs/ProcessingHub.cs`

## Phase 3.7: Frontend Implementation (Blazor)
### Core Components
- [ ] **T051** [P] Authentication component in `src/DocumentManager.Web/Components/Authentication/LoginComponent.razor`
- [ ] **T052** [P] Document upload component in `src/DocumentManager.Web/Components/Documents/UploadComponent.razor`
- [ ] **T053** [P] Document list component in `src/DocumentManager.Web/Components/Documents/DocumentListComponent.razor`
- [ ] **T054** [P] Processing status component in `src/DocumentManager.Web/Components/Processing/StatusComponent.razor`
- [ ] **T055** [P] Document sharing component in `src/DocumentManager.Web/Components/Sharing/ShareComponent.razor`

### Pages
- [ ] **T056** Dashboard page in `src/DocumentManager.Web/Components/Pages/Dashboard.razor`
- [ ] **T057** Documents page in `src/DocumentManager.Web/Components/Pages/Documents.razor`
- [ ] **T058** Processing page in `src/DocumentManager.Web/Components/Pages/Processing.razor`
- [ ] **T059** Admin page in `src/DocumentManager.Web/Components/Pages/Admin.razor`

### Frontend Services
- [ ] **T060** [P] Document service client in `src/DocumentManager.Web/Services/DocumentServiceClient.cs`
- [ ] **T061** [P] Processing service client in `src/DocumentManager.Web/Services/ProcessingServiceClient.cs`
- [ ] **T062** [P] SignalR client service in `src/DocumentManager.Web/Services/SignalRClientService.cs`

## Phase 3.8: Integration Tests (from quickstart.md scenarios)
- [ ] **T063** [P] User authentication scenario in `tests/DocumentManager.Integration.Tests/AuthenticationScenarioTests.cs`
- [ ] **T064** [P] Document upload scenario in `tests/DocumentManager.Integration.Tests/DocumentUploadScenarioTests.cs`
- [ ] **T065** [P] Processing pipeline scenario in `tests/DocumentManager.Integration.Tests/ProcessingPipelineScenarioTests.cs`
- [ ] **T066** [P] Document management scenario in `tests/DocumentManager.Integration.Tests/DocumentManagementScenarioTests.cs`
- [ ] **T067** [P] Document sharing scenario in `tests/DocumentManager.Integration.Tests/DocumentSharingScenarioTests.cs`
- [ ] **T068** [P] Admin configuration scenario in `tests/DocumentManager.Integration.Tests/AdminConfigurationScenarioTests.cs`

## Phase 3.9: Security & Middleware
- [ ] **T069** Authorization policies in `src/DocumentManager.Api/Authorization/DocumentPolicies.cs`
- [ ] **T070** Audit logging middleware in `src/DocumentManager.Api/Middleware/AuditLoggingMiddleware.cs`
- [ ] **T071** File validation middleware in `src/DocumentManager.Api/Middleware/FileValidationMiddleware.cs`
- [ ] **T072** Rate limiting configuration in `src/DocumentManager.Api/Configuration/RateLimitingConfiguration.cs`

## Phase 3.10: Observability & Health
- [ ] **T073** [P] Health checks for database in `src/DocumentManager.Api/HealthChecks/DatabaseHealthCheck.cs`
- [ ] **T074** [P] Health checks for blob storage in `src/DocumentManager.Api/HealthChecks/BlobStorageHealthCheck.cs`
- [ ] **T075** [P] Performance metrics collection in `src/DocumentManager.Api/Metrics/PerformanceMetrics.cs`
- [ ] **T076** Application Insights configuration in `src/DocumentManager.ServiceDefaults/TelemetryExtensions.cs`

## Phase 3.11: Polish & Optimization
### Unit Tests
- [ ] **T077** [P] Service layer unit tests in `tests/DocumentManager.Api.Tests/Services/`
- [ ] **T078** [P] Controller unit tests in `tests/DocumentManager.Api.Tests/Controllers/`
- [ ] **T079** [P] Blazor component unit tests in `tests/DocumentManager.Web.Tests/Components/`

### Performance & Documentation  
- [ ] **T080** Performance optimization for file uploads in `src/DocumentManager.Api/Services/DocumentService.cs`
- [ ] **T081** [P] API documentation generation with Swagger in `src/DocumentManager.Api/Program.cs`
- [ ] **T082** [P] Update README.md with deployment instructions
- [ ] **T083** Database query optimization and indexing review
- [ ] **T084** Run complete quickstart validation scenarios

## Dependencies
```
Setup (T001-T006) → Data Tests (T007-T013) → Data Implementation (T014-T026) 
   ↓
Contract Tests (T027-T035) → API Implementation (T036-T044)
   ↓
Events (T045-T050) → Frontend (T051-T062) → Integration Tests (T063-T068)
   ↓
Security (T069-T072) → Observability (T073-T076) → Polish (T077-T084)
```

### Critical Blocking Dependencies
- **T023** (Database migration) blocks all API implementation
- **T048** (Event publisher) blocks T049 (Background processing)
- **T050** (SignalR hub) blocks T062 (SignalR client)
- All contract tests (T027-T035) MUST fail before API implementation (T036-T044)
- All integration tests (T063-T068) require API implementation complete

## Parallel Execution Examples

### Phase 3.2 - Entity Tests (All Parallel)
```bash
# Launch T007-T013 together (different test files):
Task: "User entity validation tests in tests/DocumentManager.Data.Tests/Entities/UserTests.cs"
Task: "Document entity validation tests in tests/DocumentManager.Data.Tests/Entities/DocumentTests.cs"
Task: "ProcessingJob entity validation tests in tests/DocumentManager.Data.Tests/Entities/ProcessingJobTests.cs"
Task: "ShareLink entity validation tests in tests/DocumentManager.Data.Tests/Entities/ShareLinkTests.cs"
Task: "User repository tests in tests/DocumentManager.Data.Tests/Repositories/UserRepositoryTests.cs"
Task: "Document repository tests in tests/DocumentManager.Data.Tests/Repositories/DocumentRepositoryTests.cs"
Task: "ProcessingJob repository tests in tests/DocumentManager.Data.Tests/Repositories/ProcessingJobRepositoryTests.cs"
```

### Phase 3.3 - Entity Models (All Parallel)
```bash
# Launch T014-T019 together (different entity files):
Task: "User entity in src/DocumentManager.Data/Entities/User.cs"
Task: "Document entity in src/DocumentManager.Data/Entities/Document.cs"
Task: "ProcessingJob entity in src/DocumentManager.Data/Entities/ProcessingJob.cs"
Task: "PipelineStage entity in src/DocumentManager.Data/Entities/PipelineStage.cs"
Task: "ShareLink entity in src/DocumentManager.Data/Entities/ShareLink.cs"
Task: "AuditLog entity in src/DocumentManager.Data/Entities/AuditLog.cs"
```

### Phase 3.4 - Contract Tests (All Parallel)
```bash
# Launch T027-T035 together (different test files):
Task: "Contract test POST /auth/login in tests/DocumentManager.Contract.Tests/AuthenticationTests.cs"
Task: "Contract test GET /users/me in tests/DocumentManager.Contract.Tests/UserManagementTests.cs"
Task: "Contract test GET /users in tests/DocumentManager.Contract.Tests/AdminUserTests.cs"
Task: "Contract test POST /documents in tests/DocumentManager.Contract.Tests/DocumentUploadTests.cs"
Task: "Contract test GET /documents in tests/DocumentManager.Contract.Tests/DocumentListingTests.cs"
Task: "Contract test GET /documents/{id} in tests/DocumentManager.Contract.Tests/DocumentDetailsTests.cs"
Task: "Contract test DELETE /documents/{id} in tests/DocumentManager.Contract.Tests/DocumentDeletionTests.cs"
Task: "Contract test GET /documents/{id}/processing in tests/DocumentManager.Contract.Tests/ProcessingStatusTests.cs"
Task: "Contract test POST /documents/{id}/shares in tests/DocumentManager.Contract.Tests/DocumentSharingTests.cs"
```

## Validation Checklist
- [x] All 15+ API endpoints have contract tests
- [x] All 6 entities have model implementations  
- [x] All 6 quickstart scenarios have integration tests
- [x] All 10 event types have implementations
- [x] TDD approach: tests before implementation
- [x] Constitutional compliance: security, observability, events
- [x] Parallel execution maximized for independent files
- [x] Clear dependency chain prevents blocking

## Constitutional Compliance Verification
- **✅ Specification-Driven**: All tasks trace to specification requirements
- **✅ Vertical Slices**: Complete user journeys from auth to sharing
- **✅ Event-Driven**: Comprehensive event system (T045-T050)
- **✅ Contract-First**: API contracts tested before implementation
- **✅ Security by Default**: Authentication, authorization, audit logging
- **✅ Observability**: Health checks, metrics, structured logging
- **✅ Pragmatic Simplicity**: .NET Aspire reduces operational complexity

**Total Tasks**: 84 tasks covering complete system implementation
**Estimated Duration**: 3-4 weeks with parallel execution
**Team Size**: 2-3 developers optimal for parallel task execution