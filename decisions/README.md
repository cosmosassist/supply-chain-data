# Graph decisions and changes

Use one packet per decision. The operator reviews the evidence and decides first. An approval authorizes a scoped pull request. PostgreSQL records the change when it actually applies.

## Repository layout

```text
supply-chain-data/                       # open as the Obsidian vault
  renault_chain.xlsx                     # source workbook
  research/
    findings/<finding-id>.json            # original claim and confidence
    verdicts/<finding-id>.json            # independent review
    artifacts/<finding-id>/               # captures and hash manifest
  decisions/
    packets/<packet-id>.md                # brief for the human operator
    records/<packet-id>.json              # recorded human decisions
  changes/<change-id>.json                # created after approval
  views/                                 # generated edge, node, source, and receipt notes
```

Open this repository as the Obsidian vault. Relative links keep packets connected to archived evidence. The artifact manifest holds the original URL, capture time, archive location, and full SHA-256. Keep the captured bytes in the repo or a durable archive. Use a stable archive URI for large or access-controlled files. [Obsidian internal links](https://obsidian.md/help/links)

## What belongs in a packet

| Section | Include |
| --- | --- |
| **Decision requested** | One action or choice and the affected graph ID. Show the proposed graph result in plain language. |
| **Evidence** | The strongest source or short excerpt, a precise locator, and any conflicting evidence that could change the decision. Link to the capture. |
| **Confidence** | The confidence and verification status for the exact claim under review. If it is unscored or pending, say so. Include a limitation only if it affects the call. |
| **Operator response** | `Approve`, `Revise`, `Defer`, or `Reject`. Ask for a reason or condition when it affects the next action. |

Put payloads, row versions, full hashes, tool logs, and adjudication details in the linked records. Keep the packet to one screen when the evidence allows. Open a separate packet for a separate decision, such as funding research on an inferred route.

## From decision to pull request

1. **Prepare the packet.** Version the packet, evidence references, and verdict on a retained decision branch. This keeps operator review available before any graph pull request.
2. **Record the operator’s decision.** Append the operator ID, timestamp, outcome, approved scope or rationale, and the exact packet revision to `decisions/records/<packet-id>.json`. Preserve the record for every outcome. A revised scope goes back to the operator on a new packet revision.
3. **Open the pull request after approval.** The PR links the packet and decision record and carries a `changes/<change-id>.json` patch. The patch includes the decision and finding IDs, expected row versions, ordered node, edge, or evidence operations, and evidence IDs. Check that it matches the human-approved scope. Include the approved records or durable references in the PR so they remain reachable after branch cleanup.
4. **Apply and publish.** After merge, validate the patch and expected row versions and write the graph changes and ledger receipt in one PostgreSQL transaction. Record the approval reference, Git commit, patch digest, applied revision and time, and the exact before-and-after values. Generate the Obsidian receipt from PostgreSQL. A retry of the same change ID and digest returns the existing receipt. [PostgreSQL transactions](https://www.postgresql.org/docs/current/tutorial-transactions.html)

The companion knowledge graph project owns the PostgreSQL writer and applied ledger. The database keeps stable node, edge, source, and capture IDs. Evidence attaches a specific passage to a claim. When a change needs a new node and its first edge, put both in the same approved patch. If an applied decision needs correction, record a new change referencing the original. Back up the database, Git history, and evidence archive together. [PostgreSQL backup and restore](https://www.postgresql.org/docs/current/backup.html)

[Worked operator packet · MIKAS ownership](packets/P-20261127-huayou-mikas-01.draft.md)
