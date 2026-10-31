# Topic 15: Production-Ready Business Automation Capstone
## Document AI, Multi-System Orchestration & Resilient Rollback Architecture

[![n8n Version](https://img.shields.io/badge/n8n-v1.x-orange.svg)](https://n8n.io)
[![Status](https://img.shields.io/badge/Status-Production--Ready-brightgreen.svg)]()
[![License](https://img.shields.io/badge/License-MIT-blue.svg)]()

---

## 📌 Executive Overview
This repository contains the flagship capstone implementation for **Topic 15: Production-Ready Business Automation Capstone (Document AI, Multi-System Orchestration & Resilient Rollback)**.

The solution demonstrates an enterprise-grade, end-to-end employee onboarding pipeline that:
1. Ingests signed executive employment agreements via Webhook and binary PDF attachments.
2. Performs **Intelligent Document Processing (IDP)** utilizing native PDF text extraction and pure JavaScript regex entity parsing—with **zero external OCR billing dependencies**.
3. Validates candidate integrity, salary details, and role specifications through conditional guardrails.
4. Orchestrates cross-system provisioning across **HRIS Core (BambooHR)** and **Identity Provider (Okta/Azure AD)**.
5. Dynamically determines role-tailored hardware packages (MacBook Pro M3 Max, Dual 4K Displays, YubiKey) and batches individual IT logistics tickets (Jira Service Desk).
6. Posts real-time celebration announcements to **Slack** (`#announcements`).
7. Compiles an immutable **Master Onboarding Audit Receipt**.
8. Implements a **Saga Compensating Rollback Engine** triggered by `Error Trigger` to prevent orphaned cloud accounts and ghost licenses upon downstream failure.

---

## 🚀 Key Repository Artifacts
| File | Description |
| :--- | :--- |
| **`Topic 15 - Production Business Automation Capstone (Document AI & Rollback).json`** | Complete importable n8n workflow export with pure JS and pre-pinned test data on every node. |
| **`n8n_topic_15_requirement_document.md`** | Comprehensive requirement specification document covering BRD, PRD, SRS, and test plans. |
| **`n8n_topic_15_requirement_document.pdf`** | Formatted executive PDF requirement document generated via Microsoft Edge headless. |
| **`Topic_15_Implementation_Plan.md`** | Markdown implementation plan detailing step-by-step canvas build instructions. |
| **`Topic_15_Implementation_Plan.pdf`** | Official implementation plan PDF for course reference. |

---

## 🛠️ Canvas Architecture & Flow
```text
[ Manual Trigger / Webhook ] ──> [ Simulate Signed PDF ] ──> [ Extract PDF Text ]
                                                                     │
                                                                     v
                                                          [ Regex Entity Parser ]
                                                                     │
                                                                     v
                                                            [ IF Valid Contract ]
                                                              /              \
                                                (Valid Contract)          (Invalid)
                                                             v                v
                                                    [ Create HRIS ]    [ Log Rejection ]
                                                             │
                                                             v
                                                    [ Provision Okta ]
                                                             │
                                                             v
                                                  [ Determine Hardware ]
                                                             │
                                                             v
                                                ┌──> [ Batch Loop (1) ]
                                                │            │
                                         (Loop) │            v (Done)
                                   [ Jira Ticket ]   [ Slack Announcement ]
                                                             │
                                                             v
                                                    [ Master Audit Log ]

========================= COMPENSATING SAGA =========================
[ Error Trigger ] ──> [ Execute Compensating Rollback Engine ]
```

---

## 🧪 Testing & Verification Instructions
1. **Zero-Blocker Instant Canvas Testing:**
   - Open n8n, click **Import from File / Clipboard**, and import `Topic 15 - Production Business Automation Capstone (Document AI & Rollback).json`.
   - Click **Execute workflow**.
   - Every single node lights up **green** immediately utilizing realistic embedded `pinData`!
2. **External Webhook / Postman Testing:**
   - Transmit candidate PDFs to `POST https://n8n.tayanasolutions.com/webhook-test/v1/onboarding/candidate`.
   - Inspect parsed text, provisioned HRIS IDs, Okta usernames, and batched Jira asset tickets.
3. **Compensating Rollback Testing:**
   - Click **Test step** on `Code_ExecuteSagaRollback` to verify automated deprovisioning logs and rollback execution.

---

## 🎥 YouTube Video Recording Guide (60 Seconds Left-to-Right)
- **0:00 - 0:20 (Why & What):** Introduce the business challenge of fragmented employee onboarding and how this capstone eliminates orphaned accounts through Document AI, multi-system orchestration, and compensating rollback.
- **0:20 - 0:35 (Document AI & Staging):** Show the PDF generation, native PDF extraction, regex entity parsing, and HRIS/Okta account creation.
- **0:35 - 0:45 (Hardware Batching & Slack):** Highlight the dynamic hardware allocation (MacBook Pro + YubiKey) looping through Jira tickets and broadcasting to Slack.
- **0:45 - 0:60 (Audit & Saga Rollback):** Display the final Master Onboarding Audit receipt and showcase the Error Trigger compensating rollback engine that guarantees zero orphaned accounts.
