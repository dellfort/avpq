# Form System: JSON Schema-Based Dynamic Form Generation

## Overview

Forms in the AVPQ system are **not static UI components**. Instead:

1. Camunda references a **form type** and **form state** in each User Task
2. The form engine requests the corresponding JSON Schema
3. The UI renders the form dynamically from the schema
4. Form submission data is correlated back to the process instance

## Form Types

### 1. Main Application Form
- **Schema File**: `pq.application.v1.json`
- **Usage**: User Task for applicant company
- **Fields**: Company registration data, managing director list, initial documents
- **Validation**: Business logic defined in JSON Schema constraints

### 2. Managing Director Self-Declaration
- **Schema File**: `pq.selfdeclaration.gf.v1.json`
- **Usage**: Parallel User Tasks (one per managing director)
- **Fields**: Personal data, declarations on exclusion grounds, signature placeholder
- **Validation**: Mandatory field checks, legal text acknowledgment

### 3. Supplementary Documents Request
- **Schema File**: `pq.supplement.documents.v1.json`
- **Usage**: Catch Message Event + dynamic User Task
- **Fields**: List of required documents, upload points, deadline display
- **Validation**: File type/size constraints, deadline enforcement

## Form Versioning

All schemas follow semantic versioning: `<domain>.<purpose>.<version>.json`

Example:
- `pq.application.v1.json` → Version 1 of the application schema
- Future: `pq.application.v2.json` → Updated with additional fields

This allows:
- Multiple schema versions in production simultaneously
- Backward-compatible form rendering
- Explicit migration paths

## Form State Transitions

| Form Type | State | Next State | Trigger |
|-----------|-------|-----------|---------|
| Application | Draft | Submitted | User submits |
| Application | Submitted | In Review | ABSt starts validation |
| Self-Declaration | Pending | Completed | Managing director submits |
| Supplement Request | Open | Submitted | Applicant uploads documents |
| Supplement Request | Submitted | Reviewed | ABSt re-validates |

## Technical Notes

- All form data is stored in **process variables** in Camunda
- Form IDs are tracked for audit purposes
- User authentication is enforced at form rendering time
- No form state is persisted outside the process engine
