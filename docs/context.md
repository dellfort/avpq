# AVPQ Context: Roles, Terminology, and Legal Framework

## Acronyms

- **AVPQ**: Absprache Vergabe Prä-Qualifizierung (digitale Vorqualifizierung)
- **BPMN**: Business Process Model and Notation
- **ABSt**: Externe Prüfstelle (External Examination Body)
- **IHK**: Industrie- und Handelskammer (Chamber of Commerce)
- **VOL/B**: Verdingungsordnung für Leistungen außerhalb VOL/A

## Key Roles

### Applicant Company (Antragsteller)
- Submits the main application via the digital form system
- Is a legal entity (e.g., GmbH, AG)
- Provides managing director list and company data
- Uploads supporting documents

### Managing Director (Geschäftsführer)
- Natural persons who hold executive responsibility
- Must provide individual self-declarations (`pq.selfdeclaration.gf.v1.json`)
- Self-declarations are legally binding commitments
- Multiple managing directors in one application → parallel self-declaration tasks

### External Examination Body (ABSt)
- Validates documents and application completeness
- May request supplementary documents via message correlation
- Prepares assessment report for IHK
- Does not make final decisions

### Chamber of Commerce (IHK)
- Legal decision authority
- Reviews ABSt assessment and application
- Issues final approval or rejection
- Responsible for administrative accountability

## Legal Context

This process implements a **legally regulated administrative procedure** with:
- Formal deadline management
- Document preservation requirements
- Audit trails for all decisions
- Compliance with German administrative law (VwVfG)

## Technical Integration Points

- **Process Engine**: Camunda 7 (BPMN 2.0 executable)
- **Form System**: JSON Schema–based dynamic form generation
- **Messaging**: Asynchronous message correlation for external events
- **Integration**: Node.js workers handle external system interactions

## Glossary

| Term | Definition |
|------|-----------|
| **Eigenerklärung** | Self-declaration; legally binding statement from an individual |
| **Nachforderung** | Request for supplementary information or documents |
| **Ausschlussgründe** | Exclusion criteria (e.g., criminal convictions, financial insolvency) |
| **Geschäftsführerhaftung** | Personal liability of managing directors |
