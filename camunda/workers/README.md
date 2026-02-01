# Camunda 7 External Task Workers

This directory contains worker implementations for the Camunda 7 process engine.

## Architecture

Workers implement the External Task pattern and Message Correlation pattern:

- **External Tasks**: Long-running or system integration tasks
  - Example: Document validation by ABSt
  - Example: Data enrichment from external sources

- **Message Correlation**: React to external events
  - Example: Self-declarations received from managing directors
  - Example: Supplementary documents uploaded by applicants
  - Correlated via `businessKey` or process instance variables

## Worker Responsibilities (Planned)

- `ValidationWorker`: Document and application validation
- `NotificationWorker`: Email/notification dispatch
- `EventCorrelationWorker`: Handle message events from external systems
- `FormSchemaWorker`: Resolve and manage JSON Schema form definitions

## Implementation Notes

- All workers use Node.js with TypeScript
- Each worker subscribes to a specific `topicName` on the Camunda engine
- Workers are designed to be stateless and horizontally scalable
- Error handling includes automatic retry logic via Camunda's built-in failure handling

## Getting Started

See the main [README.md](../../README.md) for setup and development instructions.
