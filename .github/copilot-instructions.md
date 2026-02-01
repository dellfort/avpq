# GitHub Copilot Instructions – AVPQ Project

**AVPQ** is a legally regulated German public-sector pre-qualification workflow system. It orchestrates a multi-actor administrative process using Camunda 7 as the process engine, JSON Schema for dynamic forms, and Node.js external task workers for validation and integration.

## Critical Architecture Understanding

### Process Flow (See `docs/process.bpmn-spec.md`)
1. **Application Submission** → User Task, schema: `pq.application.v1.json`
2. **Parallel Self-Declarations** → Multi-instance User Tasks (one per managing director), schema: `pq.selfdeclaration.gf.v1.json`
3. **Document Validation** → External Task (ABSt worker), topic: `validate-application-documents`
4. **Supplementary Requests** → Catch Message Event + User Task, triggered via `SupplementaryDocumentsRequested` message, schema: `pq.supplement.documents.v1.json`
5. **Final Decision** → User Task (IHK decision maker)

### Key Design: No Static Forms
- Forms are **dynamically generated from JSON Schema**, not static UI templates
- Each User Task references a schema file in `schemas/` (e.g., `pq.application.v1.json`)
- Form data flows directly into Camunda process variables
- Versioning: `<domain>.<purpose>.<version>.json` (e.g., `v1` → `v2` for backward compatibility)

### External Task Pattern
Long-running operations (validation, integration) are handled by **stateless workers** subscribing to the Camunda External Tasks API:
- Each worker subscribes to a specific `topicName` (e.g., `validate-application-documents`)
- Workers are idempotent and horizontally scalable
- Failure handling and retries are managed by Camunda, not the worker

### Message Correlation
External systems trigger process continuation by posting messages to `POST /engine-rest/message`:
- **Message Name**: Matches a catch event in the BPMN (e.g., `SupplementaryDocumentsRequested`)
- **Business Key**: Application ID used to correlate to correct process instance
- **Process Variables**: Payload merged into process context

## Roles & Compliance Context
Read `docs/context.md` before designing any user-facing features:
- **Applicant Company**: Submits application
- **Managing Directors** (Geschäftsführer): Provide legally binding self-declarations (parallel tasks)
- **ABSt (External Examination Body)**: Validates documents, may request supplements
- **IHK (Chamber of Commerce)**: Authority making final approval/rejection decision

All form interactions must be immutably logged for audit compliance with German administrative law (VwVfG) and DSGVO.

## Developer Workflows & Code Patterns

### When Adding a New Form
1. Create schema in `schemas/` following JSON Schema v7 and existing naming convention
2. Reference it in the BPMN process (add User Task with `formKey` pointing to schema)
3. Document the form in `docs/forms.md` (state transitions, validation rules)
4. Forms auto-validate via schema; business logic validation happens in Camunda mappings or workers

### When Adding an External Task Worker
1. Create in `camunda/workers/` with filename matching task responsibility
2. Subscribe to specific `topicName` matching BPMN External Task definition
3. Workers must be **stateless** – all context flows through process variables
4. Return success via `complete(task, variables)` or failure via `failure(task, errorMessage)` (Camunda SDK)
5. Retry logic configured in BPMN, not in worker code

### Running & Testing
- Start Camunda 7 locally (Docker recommended)
- Run workers via Node.js (npm run dev / npm run build, then start)
- Test message correlation with curl or HTTP client to `http://localhost:8080/engine-rest/message`
- Verify process state in Camunda Admin Console (typically http://localhost:8080/camunda)

## File Reference Guide

| Path | Purpose |
|------|---------|
| `bpmn/praequalifizierung.bpmn` | Main BPMN 2.0 process definition (currently placeholder) |
| `schemas/*.json` | JSON Schema v7 files for dynamic forms; name pattern: `pq.<domain>.<purpose>.<version>.json` |
| `camunda/workers/` | External task worker implementations (not yet created) |
| `docs/process.bpmn-spec.md` | Textual specification of process flow, elements, error handling |
| `docs/context.md` | Roles, terminology, legal framework—read before user-facing design |
| `docs/forms.md` | Form versioning strategy and state transitions |
| `README.md` | Project overview and getting started |

## When Writing Code

- **Reference BPMN**: All worker topics, message names, and variable names must match the BPMN definition
- **Schema compliance**: Forms must output/validate against their declared schema in `schemas/`
- **Audit-safe**: Never modify process data outside of explicit BPMN mappings or worker completion calls
- **Stateless workers**: No in-memory state; context always from Camunda process variables
- **Error messages**: Must be descriptive for administrators reviewing Admin Console (e.g., "Document PDF validation failed: missing text layer")
- **German compliance**: This is a regulated procedure—maintain immutable audit trail and support deadline enforcement

## Current Phase

🚧 **Scaffold Phase**: Directory structure and documentation complete. No business logic yet.
- Next: Implement BPMN process definition
- Then: External task workers (ValidationWorker, NotificationWorker)
- Then: Form rendering engine integration
