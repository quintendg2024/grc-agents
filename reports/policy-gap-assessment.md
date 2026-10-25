# Policy Gap Assessment: NIST CSF 2.0

**Policy assessed:** Prairie Mutual Insurance Information Security Policy (v2.1, effective 2026-01-01)
**Framework:** NIST CSF 2.0 (22 categories)
**Result:** 5 Covered, 9 Partial, 8 Gap

## Coverage table

| ID | Category | Rating | Policy evidence (section) | Notes |
|---|---|---|---|---|
| GV.OC | Organizational Context | Partial | 2.2 | The only reference is that the policy is reviewed after changes to the "regulatory environment". The policy does not identify the mission, stakeholders, or specific legal and regulatory obligations. It does not mention insurance or privacy regulation. |
| GV.RM | Risk Management Strategy | Partial | 4.2 | Risks rated High need a treatment plan within 60 days. There is no risk appetite or tolerance, no rating criteria, and no treatment rule for Medium or Low risks. |
| GV.RR | Roles, Responsibilities, and Authorities | Covered | 2.1, 3.1-3.4 | The CISO, IT, managers and all personnel have named responsibilities. |
| GV.PO | Policy | Covered | 2.2, 2.3, 2.4, 10.1 | The policy is reviewed annually, published, acknowledged on hire, backed by disciplinary action, and has an exception process. |
| GV.OV | Oversight | Partial | 3.1, 2.2 | The CISO reports to the executive team, with no stated frequency or content. The annual review covers the policy, not risk results. There is no mechanism for adjusting strategy. |
| GV.SC | Cybersecurity Supply Chain Risk Management | Gap | Not addressed. | Scope in 1 covers contractors, but there is nothing on vendors or third-party providers. |
| ID.AM | Asset Management | Gap | Not addressed. | There is no inventory requirement. 4.1 refers to "critical systems", but nothing defines or classifies them. |
| ID.RA | Risk Assessment | Covered | 4.1, 4.2 | A formal annual assessment covers threats, vulnerabilities, likelihood and impact. It applies to critical systems only. There is no threat intelligence element. |
| ID.IM | Improvement | Gap | Not addressed. | There are no lessons-learned, testing or post-incident improvement requirements. |
| PR.AA | Identity Mgmt, Authentication, and Access Control | Partial | 5.1-5.5 | Manager-approved, role-based access (5.1) and removal on departure (5.4) are stated. Weaknesses: 8-character passwords (5.2); MFA only for VPN (5.3), not for admin, email or cloud access; admin rights "as appropriate" (5.5); no periodic access reviews; no timeframe for removing access. |
| PR.AT | Awareness and Training | Gap | Not addressed. | 2.3 requires only a policy acknowledgment on hire. There is no security training or phishing awareness. |
| PR.DS | Data Security | Covered | 6.1, 6.2, 6.3 (also 8.1) | AES-256 at rest, TLS 1.2 or higher in transit, personal devices and cloud storage prohibited, and retention and destruction rules. There is no data classification scheme or integrity protection. Backup testing is rated under RC.RP. |
| PR.PS | Platform Security | Covered | 7.1, 7.2, 7.3 | Secure baselines, patch deadlines of 14 and 30 days, and handling of end-of-life software. There is no vulnerability scanning requirement and no coverage of secure development. |
| PR.IR | Technology Infrastructure Resilience | Gap | Not addressed. | There is nothing on network protection, segmentation or capacity. 8.2 mentions a secondary backup location but does not address infrastructure resilience. |
| DE.CM | Continuous Monitoring | Gap | Not addressed. | There is no logging or monitoring requirement. |
| DE.AE | Adverse Event Analysis | Gap | Not addressed. | There is no event analysis, correlation or incident declaration criteria. |
| RS.MA | Incident Management | Partial | 9.1, 9.2, 3.4 | Employees must report incidents immediately. There is no incident response plan, no severity triage, no escalation path, and no defined roles. |
| RS.AN | Incident Analysis | Partial | 9.2 | IT "will investigate". There is no scope or root-cause requirement and no evidence preservation. |
| RS.CO | Incident Response Reporting and Communication | Partial | 9.1, 3.1 | Internal reporting only. There is no regulator, policyholder or law enforcement notification requirement or timeline. This is a significant gap for an insurer. |
| RS.MI | Incident Mitigation | Partial | 9.2 | "Take appropriate action" is vague. There is no containment or eradication requirement. |
| RC.RP | Incident Recovery Plan Execution | Partial | 8.1, 8.2 | Nightly backups of critical systems, stored at a secondary location. There is no backup testing, no restore or recovery time objectives, no recovery plan, and no stated offline or immutable protection. |
| RC.CO | Incident Recovery Communication | Gap | Not addressed. | No recovery communication requirement. |

## Top 5 priority gaps

1. **DE.CM and DE.AE (Gap).** Add a requirement for centralized logging and monitoring of critical systems, with defined log retention, alert review, and criteria for declaring an incident. Without detection, the incident response and recovery processes are never triggered.
2. **RS.CO and RS.MA (Partial).** Require a documented incident response plan with severity levels, escalation, and roles. Add notification timelines for regulators and affected policyholders. An insurer holds sensitive policyholder data and faces breach-notification obligations.
3. **PR.AA (Partial).** Require MFA for all remote, privileged and cloud or email access. Raise the minimum password length to 14 or more characters, or adopt NIST 800-63B guidance. Define admin privilege criteria. Add periodic access reviews and a deadline for removing leavers' access, such as 24 hours. Weak authentication is a leading cause of breaches.
4. **RC.RP (Partial).** Require periodic restore testing (at least annually), recovery time and recovery point objectives, protected or offline backups, and a recovery plan. Backups that have never been tested may not restore when needed, for example after ransomware.
5. **GV.SC (Gap).** Add third-party risk requirements covering due diligence, contract security clauses, and ongoing monitoring of vendors with access to policyholder data. Insurers rely heavily on vendors.

Also worth noting: PR.AT (no training) and ID.AM (no asset inventory) are Gaps just outside the top five. ID.AM is foundational, because controls such as patching and backups cannot be scoped without an inventory.

## Assessment limitations

- Only the policy text was judged. Whether controls such as nightly backups, patching, or encryption are actually implemented or followed is unknown.
- Standards, procedures, and plans that the policy might reference were not provided. These include the records retention schedule, the secure configuration baseline, and any incident response plan. No credit was given for them, since the policy does not state their contents.
- Applicable regulations, such as state insurance data security laws, were not assessed, so the severity of the regulatory notification gap is inferred from the nature of the business.
- The ratings reflect judgment on vague wording, such as "as appropriate", and a different reviewer might draw the Partial and Covered boundary differently. Examples are GV.OC and PR.DS.

## Files reviewed

- `sample_infosec_policy.md`
- `nist_csf_2_categories.md`
