# Topic 15: Production-Ready Business Automation Capstone
## Document AI, Multi-System Orchestration & Resilient Rollback Architecture

---

## 1. Document Control
| Field | Value |
| :--- | :--- |
| **Document Title** | Topic 15: Production-Ready Business Automation Capstone Specification |
| **Course Module** | Hands-On Level 2: Advanced Enterprise Architectures |
| **Version** | 1.0 (Enterprise Release) |
| **Status** | Approved & Production-Ready |
| **Last Updated** | 2026-10-31 |
| **Prepared By** | Lead Automation Architect & Solutions Engineering Lead |
| **Target Audience** | Enterprise Automation Engineers, Solutions Architects, Microservices & DevOps Teams |

---

## 2. Executive Summary & Architectural Vision
Manual employee onboarding across modern enterprises is notoriously fragmented, error-prone, and expensive. HR coordinators spend hours extracting candidate information from PDF contracts, re-entering details across disparate HRIS platforms (e.g., BambooHR, Workday), provisioning Identity Provider (IdP) accounts (Okta, Azure AD), dispatching hardware logistics tickets (Jira Service Management, ServiceNow), and notifying internal team channels.

When any intermediate step in this chain fails—such as an IT service desk outage or an identity provisioning conflict—organizations suffer from **orphaned accounts**, security vulnerabilities, stranded hardware orders, and payroll discrepancies.

This Capstone project establishes an **enterprise-grade, production-hardened orchestration suite** in n8n featuring:
1. **Intelligent Document Processing (IDP):** Ingests binary PDF contracts and extracts structured metadata using native n8n file extraction and pure JavaScript regex parsing with zero external OCR billing dependencies.
2. **Deterministic Data Transformation & Guardrails:** Normalizes and validates candidate identity, role eligibility, compensation tiers, and security clearance levels.
3. **Multi-SaaS Microservice Orchestration:** Coordinates sequential provisioning across HRIS databases and corporate Identity Providers.
4. **Dynamic Role-Based Hardware Allocation:** Evaluates departmental requirements to dispatch granular, itemized IT logistics tickets using resilient batch looping.
5. **Team Communication & Master Audit Telemetry:** Emits automated Slack welcome announcements and synthesizes an immutable end-to-end onboarding audit receipt.
6. **Saga Pattern Compensating Rollback:** Catches downstream failures via automated `Error Trigger` hooks and dispatches targeted compensating transactions (e.g., deprovisioning staged Okta accounts, soft-deleting HRIS draft entries) to prevent orphaned cloud resources.

---

## 3. Business Requirements Document (BRD)

### 3.1 Business Rationale & Objectives
| ID | Requirement | Business Rationale | Impact | Priority |
| :--- | :--- | :--- | :--- | :--- |
| **BRD-001** | Zero-Cost Contract Document Ingestion | Ingest signed PDF agreements without reliance on costly third-party OCR APIs. | Eliminates SaaS OCR overhead; maintains sub-second extraction speed. | High |
| **BRD-002** | Strict Data Validation Guardrails | Prevent corrupted, unverified, or incomplete candidate contracts from entering downstream systems. | Protects corporate database integrity and prevents duplicate provisioning. | Critical |
| **BRD-003** | Automated Cross-System Provisioning | Synchronize employee creation across HRIS and Identity Provider infrastructure. | Reduces onboarding cycle time from 72 hours to under 45 seconds. | High |
| **BRD-004** | Role-Based Hardware Fulfillment | Dynamically allocate engineering vs. standard hardware bundles based on departmental role. | Automates warehouse logistics; prevents equipment misallocation. | High |
| **BRD-005** | Granular Requisition Ticket Generation | Batch individual hardware components into distinct asset tracking tickets. | Ensures complete traceability in Jira / ServiceNow. | Medium |
| **BRD-006** | Departmental Broadcast Notifications | Automatically announce verified new hires in corporate Slack channels. | Fosters team cohesion and accelerates team readiness on Day 1. | Medium |
| **BRD-007** | Consolidated Immutable Audit Ledger | Generate a comprehensive execution receipt detailing all provisioned systems and identifiers. | Guarantees SOC2, ISO27001, and HR compliance adherence. | High |
| **BRD-008** | Automated Compensating Rollback (Saga) | Automatically undo upstream provisioning steps if downstream services fail. | Eliminates orphaned SaaS licenses and prevents ghost identity accounts. | Critical |

---

## 4. Product Requirements Document (PRD)

### 4.1 Feature Capabilities & User Stories
- **PRD-001 (Candidate Contract Intake):** As an HR Operations Specialist, I want to upload or transmit signed PDF employment agreements via a secure webhook endpoint so that onboarding initiates instantaneously.
- **PRD-002 (Native PDF Text Parsing):** As an Automation Architect, I want n8n to natively extract text streams from PDF binaries without external cloud dependencies so that candidate records are extracted reliably.
- **PRD-003 (Regex Entity Extraction & Verification):** As a Data Steward, I want the system to parse candidate full name, corporate email, role title, department, salary, and security clearance, verifying that all mandatory parameters are present before advancing.
- **PRD-004 (Exception Rejection Routing):** As an HR Auditor, I want invalid contracts to be routed to a rejection logger with actionable error codes so that HR teams can rectify errors immediately.
- **PRD-005 (HRIS Employee Staging):** As a People Operations Manager, I want the system to register the candidate in our central HRIS database with automated retry logic in case of transient network timeouts.
- **PRD-006 (IdP Account Provisioning):** As an IT Security Administrator, I want corporate identity credentials and MFA enrollments provisioned automatically with security clearance role mappings.
- **PRD-007 (Role-Tailored IT Asset Allocation):** As an IT Logistics Manager, I want Engineering candidates to receive developer equipment (MacBook Pro M3 Max, 4K displays, YubiKey) and non-engineering staff to receive standard business bundles.
- **PRD-008 (Batch Dispatch Loop):** As a Warehouse Dispatcher, I want equipment bundles split into single-item loop executions so that distinct tracking barcodes and Jira tickets are created.
- **PRD-009 (Team Welcome Broadcast):** As a Team Lead, I want a welcome message posted to `#announcements` introducing the new hire with department and title context.
- **PRD-010 (Resilient Saga Compensating Rollback):** As a Systems Reliability Engineer, I want the pipeline to catch unexpected downstream errors, identify the failed node, and trigger surgical compensating transactions (e.g. Okta revocation, HRIS soft deletion) to maintain system consistency.

---

## 5. Software Requirements Specification (SRS) & Canvas Blueprint

### 5.1 Canvas Architectural Topology
```text
[ N15-01: Manual_Trigger ] ────────┐
                                   v
[ N15-02: Webhook_CandidateIntake ] ──> [ N15-03: Code_SimulateSignedContractPDF ]
                                                       |
                                                       v
                                        [ N15-04: Extract_PDF_Contract ]
                                                       |
                                                       v
                                  [ N15-05: Transform_ParseContractText ]
                                                       |
                                                       v
                                         [ N15-06: IF_IsValidAndUnique ]
                                           /                       \
                             (True Branch)/                         \ (False Branch)
                                         v                           v
                       [ N15-07: HTTP_CreateHRISRecord ]    [ N15-08: Log_OnboardingRejected ]
                                         |
                                         v
                   [ N15-09: HTTP_ProvisionIdentityAccount ]
                                         |
                                         v
                 [ N15-10: Transform_DetermineHardwareBundle ]
                                         |
                                         v
                   ┌───> [ N15-11: Item_Lists_BatchHardware ]
                   │                     |
                   │ (Loop Handle)       v (Done Handle)
     [ N15-12: HTTP_CreateAssetTicket ]  [ N15-13: HTTP_PostSlackWelcome ]
                   ▲                     │
                   └─── (Loop Back)      v
                               [ N15-14: Code_MasterOnboardingAudit ]

======================= STANDALONE RESILIENCE SAGA =======================
[ N15-15: Error_Trigger ] ──> [ N15-16: Code_ExecuteSagaRollback ]
```

---

## 6. Detailed Node Configuration Blueprint

| Node Name | Node Type | Version | Operational Function & Key Configuration |
| :--- | :--- | :--- | :--- |
| **Manual_Trigger** | `manualTrigger` | 1 | Canvas testing trigger for immediate execution with zero external requirements. |
| **Webhook_CandidateIntake** | `webhook` | 1 | Production webhook endpoint listening on `POST /v1/onboarding/candidate`. |
| **Code_SimulateSignedContractPDF** | `code` | 2 | Generates binary PDF contract for Dr. Alexander Vance using base64 byte encoding. |
| **Extract_PDF_Contract** | `extractFromFile` | 1 | Native PDF extractor parsing binary property `data` into text stream. |
| **Transform_ParseContractText** | `code` | 2 | Pure JS regex engine extracting candidate, email, role, department, salary, clearance. |
| **IF_IsValidAndUnique** | `if` | 1 | Conditional guardrail evaluating `$json.isValidContract === true`. |
| **Log_OnboardingRejected** | `code` | 2 | Exception logger recording rejected contracts with status code & failure reason. |
| **HTTP_CreateHRISRecord** | `httpRequest` | 4.1 | POST to mock HRIS API with employee payload; configured with 3 retries on failure. |
| **HTTP_ProvisionIdentityAccount** | `httpRequest` | 4.1 | POST to mock IdP API provisioning enterprise username, role, and clearance. |
| **Transform_DetermineHardwareBundle** | `code` | 2 | Logic evaluating department to assemble Engineering (M3 Max, 4K, YubiKey) vs Standard bundles. |
| **Item_Lists_BatchHardware** | `splitInBatches` | 3 | Iterates through hardware units one item at a time (`batchSize: 1`). |
| **HTTP_CreateAssetTicket** | `httpRequest` | 4.1 | Dispatches individual IT Jira requisition ticket per hardware item; loops back to batch node. |
| **HTTP_PostSlackWelcome** | `httpRequest` | 4.1 | Broadcasts rich welcome message to `#announcements` upon batch loop completion. |
| **Code_MasterOnboardingAudit** | `code` | 2 | Synthesizes master enterprise onboarding receipt summarizing all provisioned systems. |
| **Error_Trigger** | `errorTrigger` | 1 | Workflow error listener capturing unhandled exceptions across any pipeline node. |
| **Code_ExecuteSagaRollback** | `code` | 2 | Evaluates failed node and executes targeted compensating rollback actions. |

---

## 7. Testing & Quality Assurance Verification Plan

| Scenario ID | Test Condition | Input Data Profile | Expected Execution Route | Verification Checkpoint |
| :--- | :--- | :--- | :--- | :--- |
| **TC-15-01** | Valid Executive Contract Intake | Complete signed PDF with Dr. Alexander Vance details. | Full pipeline: Manual Trigger -> HRIS -> IdP -> Hardware Batch -> Slack -> Audit. | Master audit receipt with status `ONBOARDING_COMPLETED_SUCCESSFULLY`. |
| **TC-15-02** | Malformed / Corrupted Document | PDF missing email, candidate name, or role attributes. | Route to `Log_OnboardingRejected` via `IF_IsValidAndUnique` False branch. | Rejection log with `REJECTED_INVALID_DOCUMENT` status; zero downstream SaaS calls. |
| **TC-15-03** | Engineering Hardware Bundle | Candidate with `department: "Engineering"`. | Allocates 3 items: MacBook Pro M3 Max, Dual 4K Displays, YubiKey 5C. | Batch loop iterates 3 times, dispatching 3 separate Jira tickets. |
| **TC-15-04** | Standard Hardware Bundle | Candidate with non-engineering department (e.g. "Operations"). | Allocates single standard laptop bundle (ThinkPad T14s 32GB). | Batch loop executes once, dispatching 1 single Jira ticket. |
| **TC-15-05** | Downstream SaaS Failure & Saga Rollback | Simulated network timeout or 500 error at `HTTP_CreateAssetTicket`. | Triggers `Error_Trigger` -> `Code_ExecuteSagaRollback`. | Rollback log executes HRIS record soft-delete and Okta deprovisioning. |
| **TC-15-06** | Canvas Execution PinData Coverage | Direct click of "Execute workflow" button in n8n canvas. | All nodes execute using pre-pinned realistic test fixtures. | 100% green checkmarks across all canvas nodes with zero external blockers. |

---

## 8. Production Deployment & Security Hardening
1. **Sandboxed Code Execution:** All transformations execute using pure ECMAScript 6 without disallowed external Node.js libraries.
2. **Credential & Secret Protection:** In production, mock URLs are substituted with n8n Credentials using OAuth2 for Okta, API Token for BambooHR, and Bearer Token for Jira.
3. **High Availability Queue Mode:** Configured for Redis-backed n8n worker nodes to process concurrent candidate intake bursts without memory degradation.
4. **Data Retention & Privacy:** Binary PDF contracts are processed in memory and released upon pipeline completion to adhere to GDPR/CCPA data minimization standards.
