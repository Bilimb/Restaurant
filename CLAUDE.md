# Restaurant Agents

Before designing or implementing anything in this repo, read `GROUNDING.md` — it defines the customer profile, Toast/QuickBooks/bank integration rules, the recipe-costing and invoice-processing engines, the shared data model, and the non-negotiable agent rules (Excel as UI, 10-minute rule, read-only first, human approval for QuickBooks writes).

Individual agent specs live as `agentN_<name>.md` files (e.g. `agent3_inventory_burn_calculator.md`).

## On New Artifact Creation

Whenever a new discussion artifact is created (a new `GROUNDING.md`, `agentN_*.md` file, or any other doc capturing decisions from a brainstorming session), always push it to GitHub. First complete the `Before Every Push` sync check below, then commit and push so the new artifact is never left only in the working tree.

## Before Every Push

Before pushing any changes to GitHub, review all existing artifacts in the repo (`GROUNDING.md`, `agentN_*.md` files, and any other docs) and ensure they reflect every decision made in the current brainstorming session. If a decision changes scope, numbering, naming, dependencies, or design of an existing agent or system, update that artifact first. Never push new work while existing docs are out of sync with what was agreed.
