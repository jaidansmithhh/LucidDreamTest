# LUCID Document Intake Specification

Version: 1.1.0  
Date: 2026-10-15  
Status: Draft implementation specification; no Monday configuration changes authorized or performed  
Recommended repository path: `docs/document-intake-spec.md`

## 1. Purpose and scope

Implement Monday's Document Intake Agent: identify document type and entity, extract structured facts, propose a filename and location, link evidence, and flag conflicts. Start with a read/extract/propose workflow for property and transaction documents. Preserve originals and never automatically overwrite conflicting material facts.

The first pilot is 5110 W Madison. Expand to other properties only after pilot review. Dropbox is an optional pilot intake source; Google Drive remains an existing source collection. This specification does not authorize storage migration, original-file moves/deletions, Monday writes, or changes to Monday authority and controls.

Spine 1 maintains data and evidence; Spine 2 consumes approved facts for document production. Packet generation, financial modeling, payments, accounting entries, external submissions, and autonomous transaction decisions are outside this intake build.

### Authoritative Monday sources

- [Document Intake Agent](https://rubyinvestments02s-team.monday.com/boards/18433907364/pulses/13201827223)
- [Shared Agent Runtime & Security](https://rubyinvestments02s-team.monday.com/boards/18433907364/pulses/13201843133)
- [C1 · Risk / Evidence Verification Agent](https://rubyinvestments02s-team.monday.com/boards/18433907364/pulses/13202162226)
- [C3 · Reconciliation / Stale-Data Control Agent](https://rubyinvestments02s-team.monday.com/boards/18433907364/pulses/13202165767)
- [Human Approval & Authority Matrix](https://rubyinvestments02s-team.monday.com/boards/18433907364/pulses/13201842849)

Monday labels and column IDs below are existing board metadata. Proposed document categories and technical keys are implementation recommendations, not existing Monday columns or approved policy changes.

## 2. Versioning and repository layout

Use semantic versioning. Each change records version, date, author, summary, and commit/PR link when one exists.

| Version | Date | Summary |
| --- | --- | --- |
| 1.0.0 | 2026-10-15 | Supplied generic intake specification. |
| 1.1.0 | 2026-10-15 | Align scope and accountability labels with LUCID Monday requirements; replace generic document schemas; distinguish extraction from verification; remove unsupported thresholds and retention defaults. |

Recommended layout:

```text
docs/document-intake-spec.md
schemas/document-intake.schema.json
workflows/document-intake.json
tests/fixtures/synthetic/
tests/expected/
```

Keep code, prompts, schemas, sanitized workflow exports, and synthetic test fixtures in GitHub. Store original documents and live property data in their designated document storage and database. Never commit credentials, live bank records, or confidential document fixtures.

Suggested commit message: `spec(doc-intake): align required fields with LUCID Monday scope`

## 3. Implementation roles and order

| Component | Responsibility |
| --- | --- |
| GitHub | Version the specification, schemas, prompts, workflow exports, and tests. |
| Dropbox or existing upload source | Supply pilot document bytes and source metadata; preserve originals. |
| n8n | Retrieve documents, track revisions, extract text, classify, propose matches/facts, flag exceptions, and log processing. |
| Central database | Separate source documents, extraction runs, proposed facts, approved facts, and review history. |
| Lovable | Display the intake register, source evidence, proposed facts, conflicts, and permitted review actions. |
| Monday | Preserve existing assignments, approvals, exceptions, deadlines, and controlling task records. |

Inspect the existing unpublished site first. Define the schema and access rules, mock up the Lovable review screens, verify one source connection, then test one complete intake flow. Add background scans after the manual flow passes acceptance. Do not treat a chat-only connector as a published-app runtime integration.

## 4. Document categories

The following taxonomy is proposed for LUCID's property and transaction scope. Confirm it during the taxonomy work required by Monday; these are not current Monday category labels.

| Proposed category | Examples |
| --- | --- |
| Property / Cityscape Report | Address, parcels, PINs, property characteristics, source-reported findings. |
| Property Tax Record | Tax bills, balances, payment history, tax-sale records. |
| Title / Deed / Court Record | Deeds, title commitments, recorded instruments, court orders. |
| Lease / Rent Roll | Leases, amendments, unit schedules, lease expirations. |
| Operating Statement / Offering Memorandum | Historical income/expenses, broker materials, reported NOI. |
| Financing / Loan Document | Term sheets, loan statements, financing requests, lender conditions. |
| Purchase / LOI / Transaction Document | LOIs, contracts, amendments, transaction reviews. |
| Inspection / Work Write-Up / Construction | Walkthroughs, scopes, estimates, bids, permits, schedules. |
| Insurance | Quotes, policies, certificates, coverage dates. |
| Funding / Grant Report | Opportunities, eligibility, award ranges, application deadlines. |
| Invoice / Supporting Financial Record | Vendor invoices or statements relevant to a property or transaction. |
| Other / Needs Classification | Unknown or unsupported documents; hold for triage. |

Generic identity-document, resume, and medical-record processing is excluded from this pilot.

## 5. Identification and matching rules

1. Capture source metadata and uploader hints without treating them as verified facts.
2. Extract text, using an OCR service when needed. Unreadable documents remain visible as exceptions.
3. Compare document contents, filename, address, PINs, and entity names with the master property/entity register.
4. Preserve multiple PINs and properties where a document covers more than one parcel or transaction.
5. Propose matches with supporting evidence. Filename or uploader metadata alone must not resolve a contradictory PIN/address/entity.
6. Route uncertain, missing, or conflicting matches to review. Do not select the first plausible match automatically.

No confidence weights, automatic-acceptance thresholds, or freshness limits are established by this specification. Calibrate proposed thresholds with reviewed pilot samples before enabling automated acceptance. Model confidence is not independent verification.

## 6. Required fields and labels

### 6.1 Existing Monday agent configuration labels

These fields describe the implementation task/agent charter; they are not required extraction fields on every PDF.

| Exact Monday label | Existing column ID | Document Intake scope |
| --- | --- | --- |
| Name | `name` | Document Intake Agent |
| Status | `color_mm7t51dw` | Existing labels: Working on it, Done, Stuck, Planned, In Progress. Retrieved intake status: Planned. |
| Owner | `multiple_person_mm7t47sf` | Accountable implementation owner; retrieved value: Ameera. |
| Agent / Layer | `dropdown_mm7trsb0` | Retrieved value: Document Agent. |
| Purpose | `long_text_mm7tn75d` | Eliminate manual naming, classification, extraction, routing, and evidence-register updates. |
| Inputs | `long_text_mm7tv6nk` | Google Drive/upload locations; Master Property Files; document taxonomy; property/entity identifiers. |
| Actions | `long_text_mm7txtsd` | Identify type/entity; extract facts; propose filename/location; link evidence; flag conflicts. |
| Approval Gate | `long_text_mm7trq60` | Never automatically overwrite conflicting material facts; original-file deletions/moves require approval until a verified workflow exists. |
| Acceptance Test | `long_text_mm7tphfx` | At least 90% correct classification and property/entity matching; conflicts surfaced; originals preserved. |
| Next Action | `long_text_mm7t9f55` | Define taxonomy and matching rules; build read/extract/propose before move/update actions. |
| Target Date | `date_mm7t5gsv` | Existing implementation target; not an extracted document deadline. |
| Build Budget | `numeric_mm7t473v` | Existing planning budget; not a vendor quote or spending authorization. |
| Weekly Hours Target | `numeric_mm7tngba` | Intended recurring hours saved. |
| Risk | `color_mm7tm4ed` | Existing implementation risk; retrieved value: Medium. |
| Evidence / Notes | `long_text_mm7txkn0` | Implementation evidence and decisions. |

### 6.2 Existing Monday accountability fields

Required for a controlling task's review submission, not for every ingested document. Blank values stay visibly incomplete; never fabricate an owner, deadline, approval, or reviewer.

| Exact Monday label | Existing column ID | Required use |
| --- | --- | --- |
| Last Update | `acct_last` | Date of latest substantive findings/evidence reconciliation. |
| Important Information Learned | `acct_learned` | Material findings personally checked by the owner and their implications. |
| Decision / Outcome | `acct_outcome` | What was achieved or decided, including whether the assignment was satisfied. |
| Next Action | `long_text_mm7t9f55` | Concrete next implementation action. |
| Blocker / Dependency | `acct_blocker` | Specific unresolved dependency, or None after reconciliation. |
| Source / Contact | `acct_source` | Supporting source/contact and relevant communication date. |
| Evidence Link | `acct_evidence` | Direct link to reviewed supporting evidence. |
| Personal Review Notes | `acct_review_notes` | What the owner checked; reviewer return/acceptance conditions. |
| Greg Decision Needed | `acct_greg` | Exact labels: No, Yes, Not Yet Determined. |
| Submission Status | `acct_submission` | Exact labels: Not Submitted, Ready for Review, Resubmission Needed. |
| Review Status | `acct_review` | Exact labels: Not Submitted, Pending Review, Accepted, Changes Needed. |
| Verified By | `acct_verified_by` | Human who personally reviewed and accepted the evidence. |
| Date Verified | `acct_verified_date` | Date entered after human evidence review. |

Owner and Target Date remain separate task-level fields. A document's author, issue date, or extracted deadline must not populate these automatically.

### 6.3 Proposed technical document envelope

These keys are application/database fields. They are not new Monday columns.

| Key | Requirement | Meaning |
| --- | --- | --- |
| `document_id` | Required | Stable internal document ID. |
| `source_system` | Required | Actual source, such as Dropbox, Drive, or manual upload. |
| `source_file_id` | Required | Stable provider file ID or assigned upload ID. |
| `source_revision` | Required | Provider revision/version or content hash. |
| `original_filename` | Required | Original name preserved. |
| `source_locator` | Required | Access-controlled original-file reference. |
| `received_at` | Required | Intake timestamp in ISO 8601. |
| `document_category` | Required outcome | Proposed supported category, or Other / Needs Classification. |
| `property_ids`, `deal_ids`, `entity_ids` | Required keys; arrays may be empty | Proposed master-record matches; empty when unresolved. |
| `property_addresses`, `pins` | Required keys; arrays may be empty | Identifiers observed in the source; preserve parcel multiplicity. |
| `scenario`, `reporting_period` | Conditional | Separate transaction scenario and period where relevant; null when unknown. |
| `document_date` | Conditional | Date supported by the document, separate from upload time. |
| `proposed_filename`, `proposed_location` | Optional proposals | No automatic original-file mutation. |
| `processing_status` | Required | Proposed technical enum: queued, processing, processed, failed. |
| `review_required`, `review_reasons` | Required | Whether review is needed and why. |
| `fields`, `exceptions`, `audit` | Required | Extracted facts, visible exceptions, and processing provenance. |

Technical processing states must not be mapped directly to Monday Done, Accepted, or any verification label.

### 6.4 Evidence requirements for each extracted fact

Each fact contains `field_name`, `value`, `source_document_id`, `source_revision`, `page_reference`, `source_excerpt`, `extracted_at`, and `verification_state`. Include currency, units, and applicable period where relevant. Preserve source wording alongside normalized values. Unknown values are null with a missing-data reason; do not use zero as a substitute.

Proposed verification states: `unverified`, `needs_review`, `human_verified`, `rejected`. A confidence score is optional and remains separate from these states. Human editing does not set model confidence to 1.0. Human verification requires an attributable reviewer, timestamp, and evidence assessment.

If page-level evidence cannot be recovered, retain the document reference and flag evidence location as incomplete. Do not invent a page number or label the unsupported fact verified.

### 6.5 Conditional extraction fields by document type

Require each applicable field to have a value or an explicit missing/unreadable/not-applicable reason. These are proposed extraction schemas; they do not imply that every source contains every field.

| Category | Applicable extraction fields |
| --- | --- |
| Property / Cityscape Report | Address; PINs; source report date; reported ownership/entity; property characteristics; source-specific findings. |
| Property Tax Record | PIN; tax year; installment; tax amount; balance; payment date/status; due date; source-reported sale/redemption information where present. |
| Title / Deed / Court Record | Property/PINs; parties; instrument or case number; recording/order date; document type; quoted relevant provisions; recorded interests/exceptions where stated. Do not infer a legal conclusion. |
| Lease / Rent Roll | Property/unit; tenant; lease start/end; base rent; payment frequency; concessions; reimbursements; occupancy; rent-roll as-of date. |
| Operating Statement / Offering Memorandum | Property; reporting period; actual/projected designation; income; vacancy; expenses; source-reported NOI; provenance of each figure. |
| Financing / Loan Document | Borrower/lender; document date; proposed/executed status; principal; rate; term; amortization; payment; maturity; lien position; conditions; fees where stated. |
| Purchase / LOI / Transaction Document | Property; parties; purchase price; document date; proposed/accepted/executed status supported by evidence; consideration, financing, diligence, and closing deadlines; deposit; seller financing; borrower cash equity where stated. |
| Inspection / Work Write-Up / Construction | Property; inspection/estimate date; scope item; condition; contractor/author; cost; schedule; permit references where present. |
| Insurance | Property/entity; insurer; quote/policy/COI identifier; effective/expiration dates; premium; coverage limits; deductibles; exclusions where stated. |
| Funding / Grant Report | Program/funder; eligible entity; geography; eligible uses; award range; match requirement; deadline; source date/link. |
| Invoice / Supporting Financial Record | Property/entity match; vendor; invoice/statement number; date; due date; amount; currency; relevant period; masked account reference where needed. |

Keep purchase price, income, expenses, debt, and borrower equity as distinct fields. Preserve separate scenarios and periods. Do not recalculate or approve financial conclusions as part of extraction.

## 7. Review and authority rules

- Show the original source and page evidence beside proposed values and existing approved values.
- Review ambiguous matches, unreadable text, missing applicable fields, contradictory facts, stale evidence, and unsupported claims.
- Preserve unresolved contradictions until evidence and an authorized reviewer resolve them.
- Store reviewer identity, timestamp, previous/proposed/accepted values, reason, and source references.
- Keep review decisions in staging until the controlling-record update is permitted by the existing authority framework.
- Monday's Ready for Review submission requires completed findings, outcome, next action, source, evidence, and review notes. Accepted requires human review, Verified By, and Date Verified; Changes Needed returns the task to Resubmission Needed.
- Do not use model outputs to manufacture human approvals or mark work complete.
- Do not change Monday ownership, permissions, approval/verification rules, authority levels, routing controls, or related automation configurations without Gregory's specific prior approval under the existing authority record.

No new two-hour/24-hour SLA, automatic approval rule, retraining consent, or review authority is created here.

## 8. Acceptance criteria

### Existing Monday minimum

- Reviewed test set correctly classifies documents and matches property/entity at least 90% of the time. Report classification and matching separately.
- Conflicts are surfaced rather than overwritten.
- Originals are preserved.
- Runtime test completes end to end; credentials are vaulted; a failed job produces a visible alert; restart/recovery is tested; audit history is retained.
- Material claims trace to evidence; unresolved contradictions remain visible.

### Proposed pilot engineering checks

- Rerunning the same file revision creates no duplicate document/fact records.
- A new revision retains the earlier source/fact history.
- Scans, ambiguous properties, multi-PIN documents, missing fields, and conflicting amounts produce visible results or exceptions.
- Extraction completion never grants human verification.
- Unauthorized users cannot read documents or approve facts.
- Processing latency, field accuracy, review time, and cost are measured before production targets are set.

The generic 10-second SLA, 96% classification target, 100-document weekly minimum, and arbitrary confidence thresholds from v1.0.0 are removed. Do not lower confidence thresholds in response to worsening precision; hold affected automation for review and recalibration.

## 9. API contract

Proposed implementation contract; confirm endpoint details against the existing backend before coding.

Input: uploaded bytes or an authorized storage reference, plus source ID, revision, original filename, received timestamp, and optional uploader hints. Never fetch arbitrary supplied URLs without validating the permitted source and access.

Example output (illustrative; no live property facts):

```json
{
  "document_id": "sample-document-001",
  "source_system": "manual_upload",
  "source_file_id": "sample-upload-001",
  "source_revision": "sample-revision-001",
  "original_filename": "sample-property-tax-report.pdf",
  "source_locator": "internal://documents/sample-document-001",
  "received_at": "2026-10-15T16:00:00Z",
  "document_category": "Property Tax Record",
  "property_ids": [],
  "deal_ids": [],
  "entity_ids": [],
  "property_addresses": [],
  "pins": [],
  "scenario": null,
  "reporting_period": null,
  "document_date": null,
  "processing_status": "processed",
  "review_required": true,
  "review_reasons": ["Property/PIN match unresolved"],
  "fields": [],
  "exceptions": [{"code": "MATCH_UNRESOLVED", "message": "Human triage required"}],
  "audit": {"processor_version": "1.1.0", "spec_version": "1.1.0"}
}
```

HTTP semantics: `202 Accepted` for an asynchronous job accepted for processing; `200 OK` for a successful result/status fetch; appropriate `4xx` for invalid/unauthorized requests; `5xx` for server failures. A successful HTTP response does not imply factual verification.

## 10. Tests and CI

Use synthetic or approved redacted fixtures representing the pilot categories. Test matching ambiguity, multiple PINs, OCR failure, absent values, conflicting amounts, duplicate retries, revision handling, review attribution, access restrictions, and recovery. Validate output against the schema and parse JSON examples. Gate releases on the reviewed pilot acceptance results rather than model confidence alone.

Do not run live document ingestion or production writes automatically from an unreviewed pull request.

## 11. Data protection and audit

Keep credentials in the appropriate runtime credential store. Limit document and database access to authorized identities. Mask sensitive account identifiers in logs. Record processor/model version when applicable, source revision, timestamps, review decisions, and errors. Prevent extracted document instructions from overriding workflow rules or invoking tools.

Retention periods, deletion schedules, freshness thresholds, and review cadence require existing policy or a separately documented decision. No default one-year deletion rule is introduced. Preserve originals and history throughout the pilot; changes to the applicable controls require the existing authorization process.

## 12. Deferred work

- Portfolio-wide ingestion and cross-system synchronization.
- Original-file renaming, moving, and deletion.
- Write-enabled Monday integration.
- Automated approved-record replacement.
- Bank/ledger reconciliation, accounting postings, and money movement.
- Packet generation, financial-model regeneration, and external publishing.

Each deferred capability requires its own specification, acceptance evidence, and any authorization required by the existing controls.
