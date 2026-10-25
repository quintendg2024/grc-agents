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
sample_user_access.csv              # 12 fictional accounts with planted control failures
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

## Next

- Policy-mapping agent: compare a written policy against NIST CSF / SOC 2 controls and report gaps.
