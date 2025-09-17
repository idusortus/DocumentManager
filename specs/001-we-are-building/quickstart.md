# Quickstart Guide: Document Processing System

**Date**: 2025-09-17  
**Feature**: Document Processing System  
**Purpose**: Quick development setup and validation  

## Prerequisites

### Development Environment
- .NET 9 SDK
- Docker Desktop (for PostgreSQL and development dependencies)
- Visual Studio 2022 or VS Code with C# Dev Kit
- Azure CLI (for Entra ID configuration)
- Git

### External Services
- Microsoft Entra ID tenant
- Azure Storage Account (for blob storage)
- SMTP server (for notifications) - optional for Phase 1

## Quick Setup (5-minute start)

### 1. Clone and Setup Solution
```bash
# Clone repository
git clone <repository-url>
cd DocumentManager

# Restore dependencies
dotnet restore

# Install Aspire workload
dotnet workload install aspire

# Start infrastructure dependencies
docker compose up -d postgres redis
```

### 2. Configure Entra ID
```bash
# Login to Azure (if not already logged in)
az login

# Create app registration (run once)
az ad app create --display-name "DocumentManager-Dev" \
  --web-redirect-uris "https://localhost:7001/signin-oidc" \
  --enable-id-token-issuance
```

### 3. Environment Configuration
Create `src/DocumentManager.AppHost/appsettings.Development.json`:
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Database=documentmanager;Username=postgres;Password=dev123",
    "BlobStorage": "DefaultEndpointsProtocol=https;AccountName=devstorageaccount;AccountKey=..."
  },
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "TenantId": "your-tenant-id",
    "ClientId": "your-client-id",
    "CallbackPath": "/signin-oidc"
  }
}
```

### 4. Database Setup
```bash
# Run migrations
dotnet ef database update --project src/DocumentManager.Data

# Seed initial data
dotnet run --project src/DocumentManager.Data -- seed
```

### 5. Start Application
```bash
# Start Aspire orchestrator
dotnet run --project src/DocumentManager.AppHost

# Application will be available at:
# - Dashboard: https://localhost:15000
# - Web App: https://localhost:7001
# - API: https://localhost:7002
```

## Validation Scenarios

### Scenario 1: User Authentication
**Goal**: Verify OAuth2 integration with Microsoft Entra ID

**Steps**:
1. Navigate to `https://localhost:7001`
2. Click "Login" button
3. Authenticate with Entra ID credentials
4. Verify user profile appears in header
5. Check user record created in database

**Expected Result**: 
- Successful login redirect
- User profile visible
- AuditLog entry for LOGIN action

**Validation**:
```sql
SELECT * FROM Users WHERE Email = 'your-email@domain.com';
SELECT * FROM AuditLogs WHERE Action = 'LOGIN' ORDER BY Timestamp DESC LIMIT 1;
```

### Scenario 2: Document Upload
**Goal**: Verify file upload and processing initiation

**Steps**:
1. Login as authenticated user
2. Navigate to "Upload Document" page
3. Select a PDF file (<10MB)
4. Add optional metadata
5. Click "Upload"
6. Verify processing job started

**Expected Result**:
- File uploaded to blob storage
- Document record created with status "Processing"
- ProcessingJob created with status "Queued"
- DocumentUploadedEvent published

**Validation**:
```sql
SELECT d.*, pj.Status as ProcessingStatus 
FROM Documents d 
LEFT JOIN ProcessingJobs pj ON d.Id = pj.DocumentId 
ORDER BY d.UploadedAt DESC LIMIT 1;
```

### Scenario 3: Processing Pipeline
**Goal**: Verify multi-stage processing execution

**Steps**:
1. Upload a document (from Scenario 2)
2. Monitor processing status in real-time
3. Wait for stages to complete
4. Verify final status

**Expected Result**:
- Document status changes: Uploaded → Processing → Processed
- Each pipeline stage executes in order
- Progress updates visible in UI
- ProcessingJobCompletedEvent published

**Validation**:
```sql
SELECT ps.StageName, ps.Status, ps.Duration 
FROM PipelineStages ps 
JOIN ProcessingJobs pj ON ps.ProcessingJobId = pj.Id 
WHERE pj.DocumentId = 'document-id-from-scenario-2'
ORDER BY ps.OrderIndex;
```

### Scenario 4: Document Management
**Goal**: Verify document listing and search

**Steps**:
1. Upload multiple documents with different names
2. Navigate to "My Documents" page
3. Verify all documents listed
4. Use search functionality
5. Test status filtering

**Expected Result**:
- All user documents displayed
- Search returns relevant results
- Filters work correctly
- Pagination functions properly

### Scenario 5: Document Sharing
**Goal**: Verify share link creation and access

**Steps**:
1. Select a processed document
2. Click "Share" button
3. Configure share settings (ViewOnly, expires in 1 day)
4. Generate share link
5. Open share link in incognito browser
6. Verify access works

**Expected Result**:
- Share link created successfully
- Anonymous access works
- Access level respected (view-only)
- DocumentSharedEvent published

**Validation**:
```sql
SELECT * FROM ShareLinks WHERE DocumentId = 'your-document-id';
SELECT * FROM AuditLogs WHERE Action = 'SHARE' ORDER BY Timestamp DESC LIMIT 1;
```

### Scenario 6: Admin Configuration
**Goal**: Verify admin capabilities

**Steps**:
1. Login as admin user
2. Navigate to "Administration" section
3. Update file size limit
4. View audit logs
5. Check user management

**Expected Result**:
- Configuration changes saved
- Audit logs visible and filterable
- User role management works
- SystemConfigurationChangedEvent published

## Performance Validation

### Load Testing Document Upload
```bash
# Install k6 for load testing
# Test concurrent uploads
k6 run scripts/load-test-upload.js

# Expected: Handle 50 concurrent uploads within 30 seconds
# Monitor: Memory usage, database connections, blob storage throughput
```

### Processing Pipeline Performance
- **Target**: Process 10 documents concurrently
- **Metric**: Average processing time <2 minutes per document
- **Monitor**: Background service queues, database performance

## Health Checks

### Application Health
```bash
# Check all services
curl https://localhost:7002/health

# Expected response: 200 OK with service statuses
```

### Infrastructure Health
```bash
# Database connectivity
curl https://localhost:7002/health/database

# Blob storage connectivity  
curl https://localhost:7002/health/storage

# Message queue health
curl https://localhost:7002/health/messaging
```

## Troubleshooting

### Common Issues

**Entra ID Authentication Fails**
- Verify tenant ID and client ID in configuration
- Check redirect URI matches app registration
- Ensure user has access to the tenant

**Document Upload Fails**
- Check file size limits
- Verify blob storage connection string
- Check allowed file types configuration

**Processing Pipeline Stalls**
- Check background service logs
- Verify message queue connectivity
- Review pipeline stage configuration

**Database Connection Issues**
- Verify PostgreSQL container is running
- Check connection string format
- Ensure database exists and migrations applied

### Logs and Monitoring

**Application Logs**
```bash
# View Aspire dashboard
https://localhost:15000

# Check structured logs in console output
dotnet run --project src/DocumentManager.AppHost --verbosity detailed
```

**Database Queries**
```sql
-- Recent errors
SELECT * FROM AuditLogs WHERE Level = 'Error' ORDER BY Timestamp DESC LIMIT 10;

-- Processing job status
SELECT Status, COUNT(*) FROM ProcessingJobs GROUP BY Status;

-- Recent uploads
SELECT COUNT(*) as UploadsToday FROM Documents 
WHERE UploadedAt >= CURRENT_DATE;
```

## Next Steps

1. **Run Integration Tests**: `dotnet test`
2. **Set up CI/CD**: Configure GitHub Actions
3. **Deploy to Staging**: Use Azure Container Apps
4. **Configure Monitoring**: Set up Application Insights
5. **Security Review**: Run security scanning tools

## Constitutional Compliance Checklist

- [x] **Specification Driven**: All scenarios map to specification requirements
- [x] **Vertical Slice**: Complete user flows from authentication to processing
- [x] **Event-Driven**: Processing pipeline uses asynchronous events
- [x] **Contract First**: API contracts defined and testable
- [x] **Security**: Authentication required, audit logging active
- [x] **Observability**: Health checks, structured logging, metrics
- [x] **Simplicity**: Aspire simplifies deployment and monitoring

This quickstart validates the core document processing workflow while demonstrating constitutional compliance and providing a foundation for further development.