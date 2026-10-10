# Proposals and their registry

A proposal is an agreed change to the **way of working** (container, contract, guides, scripts), not to the content of the Design System. It is drafted in Claude Design or with the responsible person, brought to this directory and applied by Claude Code. It does not replace `CHANGELOG.md` and it is not a session report.

## Flow
1. The proposal is saved as `docs/proposals/<name>.md` with one decision per section (`D1`, `D2`…), its reason and its changes.
2. Claude Code applies them in blocks, with validation (`npm run check`, `npm test`) and review by the responsible person.
3. When each decision is finished, its **status** and what was actually done are written in its own section, including corrections to the original premise.
4. The table below collects the status of all of them. Discarded ones are kept with their reason.
5. A proposal that changes the framework ends with a new framework version ([../framework.md](../framework.md)).

## Statuses
- **Applied**: done and validated.
- **Partial**: partly done; what is missing is in the note.
- **Discarded**: the responsible person decided not to apply it.
- **Approved**: decision confirmed by the responsible person, not yet applied.
- **Pending**: undecided, or not applied for lack of a decision.

## Registry
| Proposal | Decision | Status | Version | Note |
| --- | --- | --- | --- | --- |
| | | | | No proposals yet. |
