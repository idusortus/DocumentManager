# GitHub Copilot Instructions: Document Processing System

**Project**: Digital Document Processing System  
**Architecture**: .NET 9 Aspire with Blazor + Web API  
**Updated**: 2025-09-17  
**Feature**: 001-we-are-building  

## Project Context

This is a digital document processing system built with .NET 9 Aspire orchestration. The system handles document upload, multi-stage processing pipelines, user management, and document sharing with role-based access control.

### Core Architecture
- **Frontend**: Blazor Server with SignalR for real-time updates
- **Backend**: ASP.NET Core Web API with background services
- **Database**: PostgreSQL with Entity Framework Core
- **Storage**: Azure Blob Storage for document files
- **Authentication**: Microsoft Entra ID with OAuth2/OIDC
- **Testing**: xUnit with integration and contract testing
- **Orchestration**: .NET Aspire for service management and observability

### Key Technologies
- .NET 9, C# 12
- ASP.NET Core 9.0, Blazor Server
- Entity Framework Core 9.0
- Microsoft Entra ID, OAuth2/OIDC
- PostgreSQL, Azure Blob Storage
- xUnit, Moq, FluentAssertions
- SignalR for real-time communication
- Background services for pipeline processing

## Constitutional Principles

### Specification-Driven Development
- All features start from approved specifications in `/specs/`
- Tests are written based on acceptance criteria
- Implementation follows TDD: Red → Green → Refactor

### Vertical Slices
- Features are complete user journeys from UI to database
- Minimal cross-feature dependencies
- Each slice is independently deployable

### Event-Driven Architecture
- Processing pipeline communicates via events
- Background services handle async processing
- SignalR broadcasts real-time updates to UI

### Contract-First APIs
- OpenAPI specification defines all endpoints
- Event schemas defined in JSON Schema
- Contract tests verify API compliance

### Security by Default
- All endpoints require authentication
- Role-based authorization throughout
- Audit logging for all operations
- Encryption at rest and in transit

### Observability
- Structured logging with Serilog
- Health checks for all dependencies
- Metrics and distributed tracing via Aspire
- Performance monitoring for pipelines

## Project Structure

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

## Key Entities

### User
- Microsoft Entra ID integration
- Role-based access (Admin, Standard, ReadOnly)
- Audit trail for all actions

### Document
- Blob storage for files, metadata in database
- Processing status tracking
- Soft delete with retention policies

### ProcessingJob
- Multi-stage pipeline execution
- User interaction support
- Retry policies and error handling

### ShareLink
- Time-limited document sharing
- Access level controls (ViewOnly, Download)
- Anonymous access support

## Development Guidelines

### Code Style
- Follow Microsoft C# coding conventions
- Use nullable reference types throughout
- Async/await for all I/O operations
- Dependency injection for all services

### Testing Strategy
- Unit tests for business logic
- Integration tests for API endpoints
- Contract tests for OpenAPI compliance
- Component tests for Blazor components

### Error Handling
- Use Result patterns for operation outcomes
- Global exception handling middleware
- Structured error responses with correlation IDs
- Graceful degradation for non-critical features

### Performance
- Async programming throughout
- Database query optimization with EF Core
- Blob storage CDN for file delivery
- Background processing for long operations

## Recent Changes (Last 3 Features)

### 001-we-are-building (Current)
- **Added**: Complete system architecture and data model
- **Added**: API contracts for all user interactions
- **Added**: Event schemas for pipeline communication
- **Added**: Multi-stage processing pipeline design
- **Tech Stack**: .NET 9 Aspire, Blazor, PostgreSQL, Entra ID

## API Patterns

### Authentication
```csharp
[Authorize]
[ApiController]
[Route("api/[controller]")]
public class DocumentsController : ControllerBase
{
    // Endpoints require authentication by default
}
```

### Error Responses
```csharp
public class ErrorResponse
{
    public string Code { get; set; }
    public string Message { get; set; }
    public object? Details { get; set; }
}
```

### Async Operations
```csharp
[HttpPost]
public async Task<ActionResult<Document>> UploadAsync(
    IFormFile file, 
    CancellationToken cancellationToken)
{
    // Async/await pattern throughout
}
```

## Event Patterns

### Event Publishing
```csharp
public class DocumentUploadedEvent
{
    public Guid DocumentId { get; set; }
    public string FileName { get; set; }
    public Guid OwnerId { get; set; }
    // ... other properties
}

// Publish event after successful upload
await _eventPublisher.PublishAsync(new DocumentUploadedEvent { ... });
```

### Background Processing
```csharp
public class ProcessingPipelineService : BackgroundService
{
    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        // Process pipeline stages asynchronously
    }
}
```

## Blazor Patterns

### Component Structure
```razor
@page "/documents"
@inject DocumentService DocumentService
@inject IJSRuntime JSRuntime

<PageTitle>My Documents</PageTitle>

@* Component content *@

@code {
    // Component logic with async data loading
}
```

### Real-time Updates
```csharp
// SignalR hub for processing updates
public class ProcessingHub : Hub
{
    public async Task JoinProcessingGroup(string documentId)
    {
        await Groups.AddToGroupAsync(Context.ConnectionId, $"document-{documentId}");
    }
}
```

## Database Patterns

### Entity Configuration
```csharp
public class DocumentConfiguration : IEntityTypeConfiguration<Document>
{
    public void Configure(EntityTypeBuilder<Document> builder)
    {
        builder.HasQueryFilter(d => !d.IsDeleted); // Global soft delete filter
        builder.Property(d => d.Metadata)
               .HasConversion<JsonValueConverter<Dictionary<string, object>>>();
    }
}
```

### Repository Pattern
```csharp
public interface IDocumentRepository
{
    Task<Document?> GetByIdAsync(Guid id, CancellationToken cancellationToken);
    Task<IPagedResult<Document>> GetByOwnerAsync(Guid ownerId, DocumentFilter filter, CancellationToken cancellationToken);
}
```

## Key Commands

```bash
# Start development environment
dotnet run --project src/DocumentManager.AppHost

# Run all tests
dotnet test

# Update database
dotnet ef database update --project src/DocumentManager.Data

# Generate migration
dotnet ef migrations add MigrationName --project src/DocumentManager.Data
```

---

*This file is automatically updated when new features are planned. Manual edits between feature markers will be preserved.*