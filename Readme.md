# DocFlow Constitution

## Core Principles

### I. The Specification is the Source of Truth (NON-NEGOTIABLE)
All work begins with a specification that defines the **what** and the **why**, not the **how**. The specification's acceptance criteria form the basis for our tests. The workflow is absolute: Specification Written → Specification Approved → Failing Tests Implemented → Code Implemented to Pass Tests. An approved specification is the contract with stakeholders.

### II. Vertical Slices as the Quantum of Change
Every feature is a self-contained Vertical Slice. A slice is the smallest deployable unit of business value, encapsulating everything from API endpoints to data access. This ensures that features are delivered cohesively and can be developed, tested, and maintained with minimal cross-feature dependencies.

### III. Modules Communicate via Asynchronous Events
For inter-module or cross-slice communication, we will prioritize asynchronous, event-based messaging over direct, synchronous calls. This decouples our modules, enhances resilience, and provides the foundation for scaling individual components independently. Direct calls are only permissible for read-only queries that do not mutate state in another module.

### IV. Contracts, Not Concretions
Modules expose their capabilities through well-defined, explicit contracts. These are not just code interfaces but formal agreements like OpenAPI specifications for APIs or published message schemas for events. Consumers of a module MUST only depend on these public contracts, never on internal implementation details.

**Event Contract Requirements:**
- All event schemas must be formally defined (e.g., JSON Schema, Avro)
- Schema evolution must maintain backward compatibility
- Breaking changes require new event types or major version increments
- Event contracts are subject to the same review process as API contracts

### V. Security is Non-Negotiable
Security is not a feature or an afterthought; it is a foundational requirement. We will adhere to the principle of least privilege for all system and user interactions. All data will be treated as sensitive by default, with encryption at rest and in transit. Security considerations must be addressed in every specification.

### VI. Observability by Default
All services and pipeline stages MUST be designed with observability in mind. This includes structured logging for all significant events, application metrics for performance monitoring (e.g., processing time, error rates), and distributed tracing to follow a request through the system. A feature is not "done" until it is observable.

### VII. Pragmatic Simplicity (Justify Complexity)
Always choose the simplest solution that meets the specification's requirements (YAGNI). If a more complex solution is proposed, it must be explicitly justified in the specification by demonstrating how the simpler alternative is insufficient for current or near-future, concrete requirements.

**Complexity Hierarchy (in order of precedence):**
1. Security requirements (Section V) always justify necessary complexity
2. Observability requirements (Section VI) justify infrastructure complexity
3. Contract compliance (Section IV) justifies interface complexity
4. All other complexity must be explicitly justified against YAGNI principles

## Development & Deployment

### Automated, Repeatable Deployments
The only path to any environment is through our automated CI/CD pipeline. All builds must be immutable and containerized. The pipeline will enforce all quality gates, including security scans, static analysis, and the execution of the full test suite. Manual deployments are strictly forbidden.

### Definition of "Done"
A feature or slice is considered "done" only when:
1.  It fully implements its approved specification.
2.  All automated tests (unit, integration, and contract) are passing.
    - Contract tests must verify both API and event schema compliance
    - Integration tests must validate asynchronous event flows end-to-end
3.  It meets all security and observability requirements defined in this constitution.
4.  Its documentation and specifications have been updated to include:
    - Updated API/event contracts
    - Observability runbooks and alert definitions
5.  The implementation has been peer-reviewed and merged.

## Governance

This Constitution is the supreme governing document for all technical design and development. All specifications and reviews must explicitly validate compliance with its principles. Any proposal to amend this constitution requires a formal write-up, a review by the technical leadership, and a clear transition plan if the change impacts existing architecture.

### Principle Conflict Resolution
When constitutional principles appear to conflict during implementation:
1. **Security (Section V)** takes precedence over simplicity concerns
2. **Observability (Section VI)** requirements cannot be compromised for simplicity
3. **Contract obligations (Section IV)** must be maintained even if more complex
4. **Specification compliance (Section I)** overrides all other considerations
5. When genuine conflicts arise, escalate to technical leadership for constitutional interpretation

**Version**: 1.2.0 | **Ratified**: 2025-09-17 | **Last Amended**: 2025-09-17

# spec-kit process & prompts used

```bash
/specify We are building a digital document processing system. The application will consist of an API, Database and a user portal (SPA). All of the business logic will be handled by the API. The SPA will surface the following to the system users: User management,Upload Files,Manage Files,View/Share Files.
The application will eventually need to accept files from external sources to allow flexibility. Users could potentially email a document to our system or securely upload via FTP. Once a file is obtained by the document processing system it will be processed. There will be a default processing pipeline to begin with. It will be mult-stage and some stages will be long running. Some multi-stage pipelines may require user interaction in the middle of the otherwise automated processing pipeline.
```

```bash

/plan the application uses dotnet 9: aspire project containing webapi, blazor, postgres. Microsoft Entra ID is used for user identity and access management.
```