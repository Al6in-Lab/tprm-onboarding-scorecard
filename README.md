# Lab 2: Vendor Onboarding Questionnaire & Scorecard

> **Fictional vendors, synthetic answers.** All vendor names and responses in the scorecard are invented to demonstrate the scoring model.

**Live scorecard:** https://docs.google.com/spreadsheets/d/15uug97JSRsrsFdZ4NxTT-0jM_xziLoRAaeWB-qnut6E/edit?usp=sharing

---

## 1. Purpose

A quantitative onboarding questionnaire with **tiering, weighted domain scoring, critical gates and a decision workflow**. A strong total score cannot hide a critical control failure: gate failures override the score and route the vendor to a defined path.

## 2. Design rationale

1. **Inherent risk tiering first.** Questionnaire depth should scale with risk. A stationery supplier and a payroll processor should not receive the same questionnaire.
2. **Weighted domain scoring.** Domains carry different weights reflecting the organization's risk priorities.
3. **Critical gates.** Specific answers trigger an override regardless of total score.
4. **Evidence validation.** Self-attestation is not proof. The sheet has an *External validation* column where the analyst records outside-in findings (for example from a ratings platform such as UpGuard, Panorays, Black Kite or RiskRecon, or from manual checks such as DMARC/HSTS lookups; see Lab 3). This mirrors how hybrid platforms such as Panorays and UpGuard pair questionnaires with external ratings.
5. **Residual risk and risk acceptance.** A gated vendor does not have to be permanently blocked. The business can proceed only through a documented risk acceptance signed by the business owner and the risk function, with an expiry date. Real programs run on this basis because businesses will push back on hard stops.

## 3. Step 1: Inherent risk tiering

Score each factor 1 (low) to 3 (high), then sum.

| Factor | 1 | 2 | 3 |
|---|---|---|---|
| Data sensitivity | Public / none | Internal | PII, financial, health, regulated |
| System access | None | Limited / read-only | Privileged or production integration |
| Business criticality | Easily replaced | Important | Outage halts core operations |
| Volume of records | < 1,000 | 1,000 to 100,000 | > 100,000 |

| Total | Tier | Questionnaire depth |
|---|---|---|
| 4-6 | Tier 3 (low) | Short form: 15 questions |
| 7-9 | Tier 2 (medium) | Standard: 40 questions |
| 10-12 | Tier 1 (high) | Full: 80+ questions, evidence required, annual reassessment |

## 4. Step 2: Weighted domain scoring

Each answer scores **0** (no control), **50** (partial or in progress) or **100** (implemented and evidenced). Domain score = average of its answers. Total = sum of (domain score x weight).

| Domain | Weight | Example controls assessed | Reference |
|---|---|---|---|
| Access control and authentication | 25% | MFA, least privilege, access reviews | NIST AC, IA; ISO A.5.15, A.8.5 |
| Data protection and encryption | 20% | Encryption at rest and in transit, key management | NIST SC-8, SC-28; ISO A.8.24 |
| Incident response and notification | 20% | IR plan, tested, notification SLA | NIST IR; ISO A.5.24 to A.5.26 |
| Business continuity and DR | 15% | RTO/RPO, backups, DR test | NIST CP; ISO A.5.29, A.5.30 |
| Subprocessor / fourth-party management | 10% | Subprocessor list, flow-down clauses | NIST SR; ISO A.5.19 to A.5.21 |
| Governance and compliance | 10% | Policies, certifications (SOC 2, ISO 27001), training | NIST PM, AT; ISO clauses 5 to 7 |

Question content is aligned to the themes of common industry questionnaires (SIG Lite, CAIQ).

## 5. Step 3: Decision thresholds

| Total score | Result |
|---|---|
| 80-100 | **Approve** |
| 65-79 | **Conditional approve**: remediation plan with deadlines |
| 40-64 | **Remediate**: onboarding paused until gaps are closed |
| 0-39 | **Reject** |

## 6. Critical gates (override the score)

If any of these fail on a **Tier 1 or Tier 2** vendor, the status becomes **HOLD**, regardless of total score:

| Gate | Condition that triggers the hold |
|---|---|
| G1: Privileged MFA | No MFA on privileged or admin access |
| G2: Encryption in transit | Customer data sent unencrypted |
| G3: Breach notification | No contractual commitment to notify, or notice window longer than our requirement (24 to 72h by tier) |
| G4: Evidence | Vendor refuses to provide any independent assurance (certificate, audit report, pen test summary) |

**From HOLD, the paths are:**
1. **Remediate**: the vendor fixes the gap and provides evidence, then re-score.
2. **Risk-accept**: the business owner and risk function sign an acceptance with compensating controls and an expiry date.
3. **Reject**: no acceptable path.

## 7. Sheet structure

| Tab | Contents |
|---|---|
| `README` | Legend and how to use the sheet |
| `1_Tiering` | Inherent risk factors and tier calculation (formula-driven) |
| `2_Questionnaire` | Questions, domain, weight, vendor answer (0/50/100), evidence link, **external validation**, analyst notes |
| `3_Scorecard` | Domain scores, total score, gate status, final decision |
| `4_Vendors` | Vendor register: Tier, Total score, Gate status, Decision, Inherent and Residual risk, Owner, Review date |

**Decision values:** Approve / Conditional approve / Remediate / Risk-accept / Reject

## 8. Worked examples (synthetic)

| Vendor | Tier | Total score | Gates | Decision |
|---|---|---|---|---|
| PayFlow Ltd | 1 | 86 | All pass | **Approve** |
| DocuShare Co | 1 | 84 | **G1 fails (no admin MFA)** | **HOLD**: score alone would have approved; gate forces remediation or risk acceptance |
| QuickPrint | 3 | 58 | N/A (Tier 3) | **Remediate** |

The second row is the point of the model: without gates, DocuShare would have passed.

## 9. Limitations

- Self-attested answers can be wrong or optimistic. The external-validation column and evidence requirement partly address this but do not replace audits.
- Weights and thresholds are illustrative and should be calibrated to a given organization's risk appetite.
- A spreadsheet is suited to a small program. At scale, this logic lives in a TPRM platform with workflow automation, which is where tools like Panorays and UpGuard are used.

## 10. Tooling note

I have not operated commercial TPRM platforms. This lab builds the underlying logic by hand (tiering, weighting, gating, validation and risk acceptance) to show I understand what those platforms automate.
