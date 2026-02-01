# AVPQ – Digitale Vorqualifizierung

**Digitale Vorqualifizierung** (AVPQ) is a legally regulated administrative procedure for pre-qualifying companies in German public-sector procurement processes.

## Project Overview

This is a Node.js + TypeScript implementation of a Camunda 7–driven workflow system for:

- **Applicants**: Companies submitting pre-qualification applications
- **Managing Directors**: Natural persons providing legally binding self-declarations
- **ABSt (External Examination Bodies)**: Validating applications and documents
- **IHK (Chambers of Commerce)**: Making final pre-qualification decisions

### Key Characteristics

✓ Process orchestration via **Camunda 7 BPMN 2.0**  
✓ **JSON Schema–based dynamic form system** (no static UI forms)  
✓ **Message correlation** for external events (e.g., document uploads, supplementary requests)  
✓ **Multi-instance tasks** for parallel managing director self-declarations  
✓ **External task workers** for document validation and system integration  
✓ Built-in **audit logging** and compliance with German administrative law  

## Project Structure

```
avpq/
├── bpmn/
│   └── praqualifizierung.bpmn         # Main process definition (Camunda 7 BPMN 2.0)
├── schemas/
│   ├── pq.application.v1.json         # Main application form schema
│   ├── pq.selfdeclaration.gf.v1.json  # Managing director self-declaration schema
│   └── pq.supplement.documents.v1.json # Supplementary documents request schema
├── camunda/
│   └── workers/
│       └── README.md                  # External task worker documentation
├── docs/
│   ├── context.md                     # Roles, terminology, legal framework
│   ├── process.bpmn-spec.md           # Textual BPMN specification
│   └── forms.md                       # Form system and schema management
├── README.md                          # This file
└── .gitignore                         # Git configuration
```

## Getting Started

### Prerequisites

- **Node.js** 16+ and npm
- **Camunda 7** running locally or remote (Docker recommended)
- **TypeScript** knowledge

### Installation

```bash
npm install
```

### Running the Project

(Instructions to be added as development progresses)

```bash
npm run dev
```

### Building

```bash
npm run build
```

## Architecture Highlights

### BPMN Process Engine (Camunda 7)

The process is defined in [`bpmn/praqualifizierung.bpmn`](bpmn/praqualifizierung.bpmn):

- User Tasks for form interactions (application, self-declarations, decisions)
- External Tasks for document validation and system integration
- Message events for asynchronous external notifications (e.g., supplementary document uploads)
- Parallel Multi-Instance tasks for managing directors

### Dynamic Forms (JSON Schema)

Forms are **not static UI templates**. Instead:

1. Each User Task references a form schema (e.g., `pq.application.v1.json`)
2. The frontend requests the schema from the form engine
3. The UI renders the form dynamically
4. On submission, form data is stored in process variables

See [`docs/forms.md`](docs/forms.md) for details.

### External Task Workers

Long-running or system-integrating tasks are handled by **external workers**:

- Subscribe to Camunda via the External Tasks API
- Process tasks (e.g., document validation)
- Report completion or failure back to Camunda
- Coordinate via Camunda's built-in retry and error handling

See [`camunda/workers/README.md`](camunda/workers/README.md) for implementation details.

## Documentation

| Document | Purpose |
|----------|---------|
| [`docs/context.md`](docs/context.md) | Roles, terminology, legal context |
| [`docs/process.bpmn-spec.md`](docs/process.bpmn-spec.md) | Textual BPMN process flow |
| [`docs/forms.md`](docs/forms.md) | Form types, schemas, versioning |
| [`camunda/workers/README.md`](camunda/workers/README.md) | External task worker architecture |

## Current Status

🚧 **Project Scaffold Phase**

- ✅ Directory structure created
- ✅ JSON Schema placeholders established
- ✅ BPMN placeholder created
- ✅ Documentation skeleton in place
- ⏳ BPMN process definition (detailed)
- ⏳ External task worker implementations
- ⏳ Form rendering engine
- ⏳ Camunda integration tests

## Development Workflow

1. **Process Design**: Refine BPMN in [`bpmn/praqualifizierung.bpmn`](bpmn/praqualifizierung.bpmn)
2. **Schema Definition**: Expand JSON schemas in [`schemas/`](schemas/)
3. **Worker Implementation**: Build external task handlers in [`camunda/workers/`](camunda/workers/)
4. **Integration Testing**: Validate end-to-end process flows
5. **Deployment**: Package for production Camunda instance

## Legal & Compliance

This project implements a **legally regulated administrative procedure** under German law:

- Formal deadline management
- Document preservation requirements
- Audit trails for all decisions
- Compliance with VwVfG (Verwaltungsverfahrensgesetz)
- DSGVO/GDPR data protection

## Repository Setup

```bash
git init
git add .
git commit -m "Initial project scaffold: AVPQ workspace structure"
git remote add origin <your-github-repo-url>
git push -u origin main
```

## License

(To be determined)

## Contact

(To be determined)
