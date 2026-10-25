# GRC Agents: AI-Assisted Access Review

An AI agent that performs a user access review, the kind of control testing GRC and IT audit teams run quarterly for SOX, SOC 2, and ISO 27001. Built with [Claude Code](https://code.claude.com/docs/en/sub-agents) subagents.

> All data in this repo is fictional sample data. No real users, systems, or company information.

## What it does

The `access-reviewer` agent reads a user access export (CSV) and flags:

| Check | Example |
|---|---|
| Terminated/inactive users with active access | HR says terminated, account still enabled |
| Stale accounts | No login in 90+ days |
| Excessive privilege | Help desk technician with Domain Admin |
| Segregation-of-duties (SoD) conflicts | Same user can Create Vendor and Approve Payment |
| Shared or unowned accounts | `svc_`, `temp` accounts with no named owner |

It produces an auditor-style report: executive summary, findings table with risk rating (High/Medium/Low), evidence, recommended action, and a data-limitations section. The agent is **read-only**; it cannot modify the source data.

## Repo contents

```
.claude/agents/access-reviewer.md   # The agent definition (instructions, tools, output format)
.claude/agents/policy-mapper.md     # Policy-to-framework gap analysis agent
sample_user_access.csv              # 12 fictional accounts with planted control failures
sample_infosec_policy.md            # Fictional information security policy (Prairie Mutual Insurance)
nist_csf_2_categories.md            # NIST CSF 2.0 category checklist used as the reference
reports/policy-gap-assessment.md    # Policy-mapper output for the sample policy
README.md
```

## How to run

1. Install [Claude Code](https://code.claude.com/docs/en/setup) and sign in.
2. Clone this repo and open a terminal in the folder.
3. Run `claude`, then:
   ```
   Use the access-reviewer agent to review sample_user_access.csv
   ```

## Results

The sample file contains 12 accounts with deliberately planted issues. The final version of the agent:

- Reviewed all **12** accounts and reported **11 findings** (5 High, 5 Medium, 1 Low)
- Caught every planted issue, including a terminated user with active access, two SoD conflicts, and an unowned service account with Full Control
- Correctly **did not** flag a disabled account, and noted a user at 87 days was not yet stale
- Explained when it deviated from the risk guidance (rated an ownerless account Medium instead of Low because it held a fraud-sensitive role)

## What I learned: validating the agent

The first version got the analysis right but the **summary math wrong**: it reported 11 accounts reviewed when the file had 12, and double-counted a finding. Every account was actually covered; the error was in the totals.

In audit work, a wrong count is a finding against the reviewer, so I treated it like one:

1. **Found the error** by checking the agent's totals against the source file.
2. **Fixed the root cause** by adding a "Before you finish" self-check to the agent: count the rows, confirm every account appears in the results, and reconcile summary totals to the findings table.
3. **Re-tested** until the totals reconciled on a clean run.

I also noticed that two runs on the same data grouped findings differently (11 vs. 13). That's why AI output in a control process still needs a fixed format and human sign-off.

## Limitations

- Privileged roles are judged from role names and job titles, not a documented role-to-permission matrix.
- One last-login value per account; no activity history, termination dates, or approval records.
- A single-system snapshot; cross-application SoD conflicts are out of scope.

## Policy-mapper agent

The `policy-mapper` agent reads a written security policy and maps it against NIST CSF 2.0, the kind of gap analysis GRC teams run when preparing for an audit or framework adoption.

### What it does

For each of the 22 CSF 2.0 categories it assigns a rating and cites the policy section it relied on:

| Rating | Meaning |
|---|---|
| Covered | The policy states a requirement that addresses the category |
| Partial | The policy touches the category but is vague or missing key elements |
| Gap | The policy does not address the category |

It produces an executive summary, a coverage table (category, rating, policy evidence, notes), a prioritized gap list, and a data-limitations section. Like the access-reviewer, it is **read-only**. It judges the policy text only, and gives no credit for referenced documents it cannot see.

### How to run

```
Use the policy-mapper agent to assess sample_infosec_policy.md against NIST CSF 2.0
```

### Results

On the fictional Prairie Mutual Insurance policy (v2.1), the agent rated **5 Covered, 9 Partial, 8 Gap**. Its top priorities were:

1. No logging, monitoring, or event analysis (DE.CM, DE.AE), so incident reporting depends on someone noticing an attack
2. No incident response plan or notification timelines (RS.MA, RS.CO)
3. Weak authentication: MFA only on VPN and 8-character passwords (PR.AA)
4. Backups with no restore testing or recovery objectives (RC.RP)
5. No third-party or vendor risk requirements (GV.SC)

The full table is in [`reports/policy-gap-assessment.md`](reports/policy-gap-assessment.md).

### Reviewer judgment: RS.CO

I would rate **RS.CO (Incident Response Reporting and Communication) a Gap rather than Partial**. The agent gave Partial credit because the policy requires employees to report incidents internally. But insurers hold policyholder data and have regulatory breach-notification duties, and the policy sets no regulator, policyholder, or law enforcement notification requirement or timeline. Internal reporting does not satisfy the intent of the category for this kind of organization. With that change the tally is **5 Covered, 8 Partial, 9 Gap**.

The agent itself flagged that its regulatory-gap severity was inferred from the nature of the business, not from an assessment of specific laws. That is why the rating call belongs to a human reviewer. It is the same reason the access review needs sign-off.

### Limitations

- Policy text only; it cannot tell whether a control is actually implemented or followed.
- Ratings on vague wording ("as appropriate", "take appropriate action") are judgment calls, and a different reviewer could draw the Partial/Covered line elsewhere.
- Applicable regulations (for example state insurance data security laws) are not assessed.

## Next

- Extend the policy-mapper to other frameworks, such as SOC 2 and ISO 27001.
