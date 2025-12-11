# CE-RISE DPP Record Custody

[![DOI](https://zenodo.org/badge/DOI/TOBEOBTAINED.svg)](https://doi.org/TOBEOBTAINED) [![Schemas](https://img.shields.io/badge/Schema%20Files-LinkML%2C%20JSON%2C%20SHACL%2C%20OWL-32CD32)](https://ce-rise-models.codeberg.page/dpp-record-custody/)

Repository for the CE-RISE DPP Record Custody data model, part of the DPP Metadata Layer in the Digital Product Passport architecture. This model represents the chain of custody and governance history of the DPP record itself. It captures custody events (creation, update, transfer, archival), identifies custodians, records authorized transfers, and includes integrity evidence such as signatures or hashes. This ensures auditability, accountability, and secure lifecycle governance of the DPP as an information object, complementing the structural metadata (dpp-record-metadata) and operational governance (dpp-access-and-governance).


---

## Data Model Structure
This data model provides a comprehensive audit trail of who did what to DPP records and when, capturing the complete chain of custody from creation through archival or disposal. It ensures forensic-level traceability of all changes, transfers, and governance actions applied to DPP data throughout its lifecycle.

### Key Design Principles
1. **Event-based tracking**: Every significant action is captured as a timestamped event
2. **Custodian identification**: Clear identification of all parties who handle the data
3. **Integrity preservation**: Cryptographic evidence for all custody changes
4. **Transfer authorization**: Explicit recording of authority for custody transfers
5. **Immutable history**: Append-only event log that cannot be altered
6. **Compliance-ready**: Supports regulatory requirements for data governance auditing


### Core Hierarchy

```
DPPRecordCustody (root)
├── 1. CustodyEvents (multivalued, chronological)
│   ├── EventMetadata
│   │   ├── EventID (unique identifier)
│   │   ├── EventType (creation, update, transfer, validation, archival, disposal)
│   │   ├── EventTimestamp (ISO 8601)
│   │   ├── EventDescription
│   │   └── EventJustification (reason for the event)
│   ├── EventActor
│   │   ├── ActorID (unique identifier)
│   │   ├── ActorName
│   │   ├── ActorRole (creator, editor, validator, custodian, auditor)
│   │   ├── ActorOrganization
│   │   └── ActorAuthentication (how actor was authenticated)
│   └── EventIntegrity
│       ├── PreviousHash (hash of previous event for chain integrity)
│       ├── CurrentHash (hash of current event data)
│       ├── HashAlgorithm (SHA-256, SHA-3, etc.)
│       ├── DigitalSignature (optional)
│       └── SignatureAlgorithm (if signed)
│
├── 2. CustodianHistory (multivalued)
│   ├── CustodianIdentity
│   │   ├── CustodianID
│   │   ├── CustodianName
│   │   ├── CustodianType (individual, organization, system)
│   │   ├── CustodianContact
│   │   └── CustodianJurisdiction
│   ├── CustodyPeriod
│   │   ├── CustodyStartTime
│   │   ├── CustodyEndTime
│   │   └── CustodyDuration (calculated)
│   └── CustodyResponsibilities
│       ├── DataStewardship (what they were responsible for)
│       ├── AccessGranted (permissions during custody)
│       └── ComplianceObligations (regulatory requirements)
│
├── 3. TransferRecords (multivalued)
│   ├── TransferAuthorization
│   │   ├── AuthorizationID
│   │   ├── AuthorizingParty
│   │   ├── AuthorizationDocument (reference)
│   │   ├── LegalBasis
│   │   └── TransferConditions
│   ├── TransferDetails
│   │   ├── TransferID
│   │   ├── TransferTimestamp
│   │   ├── FromCustodian
│   │   ├── ToCustodian
│   │   └── TransferMethod (API, manual, migration, etc.)
│   └── TransferVerification
│       ├── VerificationMethod
│       ├── VerificationTimestamp
│       ├── VerificationResult
│       └── VerificationEvidence
│
├── 4. IntegrityEvidence
│   ├── DataFingerprint
│   │   ├── FingerprintValue
│   │   ├── FingerprintAlgorithm
│   │   ├── FingerprintScope (full record, specific fields)
│   │   └── FingerprintTimestamp
│   ├── BlockchainAnchoring (optional)
│   │   ├── BlockchainNetwork
│   │   ├── TransactionHash
│   │   ├── BlockNumber
│   │   └── SmartContractAddress
│   └── ExternalAttestation
│       ├── AttestationProvider
│       ├── AttestationID
│       ├── AttestationTimestamp
│       └── AttestationProof
│
└── 5. GovernanceActions
    ├── ValidationRecords
    │   ├── ValidationID
    │   ├── ValidationType (schema, business rules, regulatory)
    │   ├── ValidationTimestamp
    │   ├── Validator (person/system)
    │   └── ValidationOutcome
    ├── AuditTrail
    │   ├── AuditID
    │   ├── AuditType (internal, external, regulatory)
    │   ├── AuditTimestamp
    │   ├── Auditor
    │   └── AuditFindings
    └── DisposalRecord (when applicable)
        ├── DisposalAuthorization
        ├── DisposalTimestamp
        ├── DisposalMethod
        ├── DisposalVerification
        └── RetentionException (if any data retained)

```

### Workflow Sequence

#### **Step 1: Custody Event Capture**
Record every significant action on the DPP record:
- **EventMetadata**: Unique ID, type, timestamp, description, justification
- **EventActor**: Who performed the action with authentication details
- **EventIntegrity**: Cryptographic chain linking with hashes and optional signatures

#### **Step 2: Custodian Management**
Track all parties who have had custody of the data:
- **CustodianIdentity**: Full identification of the responsible party
- **CustodyPeriod**: When they had custody with precise timestamps
- **CustodyResponsibilities**: What they were authorized to do and compliance requirements

#### **Step 3: Transfer Documentation**
Record all authorized transfers between custodians:
- **TransferAuthorization**: Legal basis and conditions for transfer
- **TransferDetails**: Who, when, and how the transfer occurred
- **TransferVerification**: Proof that transfer was completed successfully

#### **Step 4: Integrity Preservation**
Maintain cryptographic evidence of data integrity:
- **DataFingerprint**: Hashes of the data at specific points in time
- **BlockchainAnchoring**: Optional immutable ledger recording
- **ExternalAttestation**: Third-party verification services

#### **Step 5: Governance Recording**
Document all governance actions taken on the data:
- **ValidationRecords**: Schema and business rule validations
- **AuditTrail**: Internal and external audit activities
- **DisposalRecord**: When and how data was disposed of (if applicable)

### Data Properties

Each custody element serves a specific audit and governance purpose. Most fields are required to ensure complete forensic traceability, with optional fields for enhanced governance scenarios.

#### SQL Identifiers

Every data point includes a `sql_identifier` annotation following the pattern:

**Pattern**: `dprc_[category]_[specific_name]`

**Features:**
- **DPP Record Custody Prefix**: All identifiers start with `dprc_`
- **Hierarchical Namespacing**: Category prefixes (`event_`, `custodian_`, `transfer_`, `integrity_`, `governance_`)
- **Database-Friendly**: Uses underscores, avoids special characters
- **Unique Across Model**: No duplicate identifiers
- **Forensic Focus**: Names reflect audit and custody concepts

**Examples:**
- `dprc_event_id` - Unique event identifier
- `dprc_event_timestamp` - When the event occurred
- `dprc_custodian_id` - Custodian identifier
- `dprc_transfer_authorization` - Transfer authorization reference
- `dprc_integrity_hash` - Integrity hash value
- `dprc_governance_audit_id` - Audit record identifier

---

## Development Roadmap

| Step | Component | Sub-Components | Criticalities Identified | Solutions Planned | Status | Missing/TODO |
|------|-----------|---------------|-------------------------|-------------------|--------|--------------|
| **1** | **Custody Events** | • EventMetadata<br>• EventActor<br>• EventIntegrity | • Event ordering and sequencing<br>• Actor authentication verification<br>• Hash chain integrity<br>• Event type standardization<br>• Timestamp precision | • Chronological event list<br>• Authentication methods<br>• SHA-256/SHA-3 hashing<br>• Event type enumeration<br>• ISO 8601 timestamps | **PLANNED** | • Event replay protection<br>• Multi-signature support<br>• Event correlation<br>• Batch event processing |
| **2** | **Custodian History** | • CustodianIdentity<br>• CustodyPeriod<br>• CustodyResponsibilities | • Custodian verification<br>• Overlapping custody periods<br>• Responsibility mapping<br>• Jurisdiction handling<br>• Contact management | • Identity verification<br>• Period validation<br>• RBAC integration<br>• Legal jurisdiction<br>• Contact schemas | **PLANNED** | • Custodian registry<br>• Delegation chains<br>• Backup custodians<br>• Emergency access |
| **3** | **Transfer Records** | • TransferAuthorization<br>• TransferDetails<br>• TransferVerification | • Authorization validation<br>• Transfer atomicity<br>• Cross-system transfers<br>• Legal compliance<br>• Verification methods | • Auth document refs<br>• Atomic transfers<br>• Transfer protocols<br>• Legal basis tracking<br>• Verification proofs | **PLANNED** | • Smart contracts<br>• Escrow mechanisms<br>• Rollback capability<br>• Transfer templates |
| **4** | **Integrity Evidence** | • DataFingerprint<br>• BlockchainAnchoring<br>• ExternalAttestation | • Hash algorithm selection<br>• Blockchain integration<br>• Attestation providers<br>• Evidence preservation<br>• Scope definition | • Multiple hash algos<br>• Blockchain adapters<br>• Attestation APIs<br>• Evidence storage<br>• Granular scoping | **PLANNED** | • Quantum-safe hashing<br>• Multi-chain support<br>• Notarization<br>• Evidence archival |
| **5** | **Governance Actions** | • ValidationRecords<br>• AuditTrail<br>• DisposalRecord | • Validation rules<br>• Audit completeness<br>• Disposal verification<br>• Retention policies<br>• Regulatory compliance | • Validation framework<br>• Audit standards<br>• Disposal workflows<br>• Retention rules<br>• Compliance tracking | **PLANNED** | • Auto-validation<br>• Audit analytics<br>• Disposal automation<br>• Hold management |

### Integration Opportunities

- **W3C PROV-O** - Provenance Ontology for tracking entity derivation
- **PREMIS** - Preservation metadata for digital objects
- **Dublin Core** - Metadata terms for resource description
- **FOAF** - Friend of a Friend for agent identification
- **Time Ontology** - Temporal concepts and relations
- **Verifiable Credentials** - W3C standard for tamper-proof claims
- **EPCIS** - Events for supply chain visibility
- **ISO 15489** - Records management standards
- **Chain of Custody standards** - ISO 27037 for digital evidence
- **Blockchain ontologies** - EthOn for Ethereum, BLONDiE for distributed ledgers



---

## Publishing

Release artifacts for each version (`schema.json`, `shacl.ttl`, `model.owl`)  
are served directly from this URL:
```
https://ce-rise-models.codeberg.page/dpp-record-custody/
```


---

## Accessing Previous Releases

If you want to view the files published for version `v1.2.0`, open:

```
https://codeberg.org/CE-RISE-models/dpp-record-custody/src/tag/pages-v1.2.0/generated/
```

Files available in that directory typically include:

- schema.yaml
- schema.json
- shacl.ttl
- model.ttl
- index.html


---
<a href="https://europa.eu" target="_blank" rel="noopener noreferrer">
  <img src="https://ce-rise.eu/wp-content/uploads/2023/01/EN-Funded-by-the-EU-PANTONE-e1663585234561-1-1.png" alt="EU emblem" width="200"/>
</a>

Funded by the European Union under Grant Agreement No. 101092281 — CE-RISE.  
Views and opinions expressed are those of the author(s) only and do not necessarily reflect those of the European Union or the granting authority (HADEA).  
Neither the European Union nor the granting authority can be held responsible for them.

© 2025 CE-RISE consortium.  
Licensed under [Creative Commons Attribution–NonCommercial 4.0 International (CC BY-NC 4.0)](https://creativecommons.org/licenses/by-nc/4.0/).  
Attribution: CE-RISE project (Grant Agreement No. 101092281) and the individual authors/partners as indicated.

<a href="https://www.nilu.com" target="_blank" rel="noopener noreferrer">
  <img src="https://nilu.no/wp-content/uploads/2023/12/nilu-logo-seagreen-rgb-300px.png" alt="NILU logo" width="40"/>
</a>

Developed by NILU (Riccardo Boero — ribo@nilu.no) within the CE-RISE project.  



