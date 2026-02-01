# BPMN Process Specification: Digitale Vorqualifizierung

## High-Level Process Flow

```
Application Start
    ↓
[Company submits application]
    ↓
[Parallel self-declarations requested from all managing directors]
    ↓
[ABSt validates documents]
    ↓ (if incomplete)
[Request supplementary documents via message]
    ↓ (when documents received, message correlation)
[Resume validation]
    ↓ (if valid)
[IHK reviews and decides]
    ↓
[Approval or Rejection]
    ↓
Process End
```

## Process Elements (Planned)

### 1. Application Reception
- **Type**: User Task
- **Form Schema**: `pq.application.v1.json`
- **Output**: Application ID, company data, managing director list

### 2. Self-Declaration Tasks (Parallel)
- **Type**: Multi-Instance User Task
- **Form Schema**: `pq.selfdeclaration.gf.v1.json`
- **Instances**: One per managing director
- **Correlation**: By applicant name and application ID

### 3. Document Validation (ABSt)
- **Type**: External Task
- **Topic**: `validate-application-documents`
- **Output**: Validation result, required supplements (if any)

### 4. Supplementary Document Request
- **Type**: Catch Message Event + User Task
- **Message Name**: `SupplementaryDocumentsRequested`
- **Form Schema**: `pq.supplement.documents.v1.json`
- **Correlation**: By application ID

### 5. Final Decision (IHK)
- **Type**: User Task
- **Output**: Approval/Rejection decision with reasoning

## Message Correlation

External systems trigger process continuation by sending messages:

```
POST /engine-rest/message
{
  "messageName": "SupplementaryDocumentsRequested",
  "businessKey": "APP-001",
  "processVariables": {
    "supplementaryDocuments": { "value": [...], "type": "Object" }
  }
}
```

## Error Handling (Planned)

- Retry logic for failed validation tasks
- Escalation paths for blocked applications
- Audit logging for all state transitions

## Security & Compliance

- All form submissions require authentication and authorization
- Process instances linked to authenticated users
- Immutable audit trail of all decisions
- Compliance with data protection (DSGVO/GDPR)
