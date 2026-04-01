# ETL Testing Copilot — VS Code Setup Guide

This folder contains a complete set of custom GitHub Copilot agent definitions
for ETL testing workflows, requiring **no Python or additional code**.

---

## Folder Structure

```
your-project/
├── .github/
│   ├── copilot-instructions.md       ← Global workspace instructions
│   └── agents/
│       ├── orchestrator.md           ← Brain: routes tasks to sub-agents
│       ├── metadata-engineer.md      ← Extracts schema & mapping metadata
│       ├── test-designer.md          ← Generates test cases & SQL scripts
│       └── data-generator.md         ← Creates synthetic test datasets
└── README.md                         ← This file
```

---

## Prerequisites

- **VS Code** v1.99 or later
- **GitHub Copilot** extension installed and signed in
- **GitHub Copilot Chat** extension installed

---

## How to Install

1. Copy the `.github/` folder into the **root of your project**.
2. Open the project in VS Code.
3. Open the Copilot Chat panel (`Ctrl+Alt+I` / `Cmd+Alt+I`).
4. The agents will be available automatically — no restart needed.

---

## How to Use Each Agent

### Option A — Use the Orchestrator (Recommended)

In Copilot Chat, type `@orchestrator` and describe your task:

```
@orchestrator I have source table SALES_SRC and target SALES_TGT.
The business rule is: only load records where REGION = 'EMEA'
and ORDER_DATE > '2024-01-01'.
```

The Orchestrator will decide which specialist agent to call.

---

### Option B — Call Specialist Agents Directly

#### Metadata Engineer
```
@metadata-engineer
Source table: ORDERS_SRC
Target table: ORDERS_TGT
Environment: DEV
```

#### Test Designer
```
@test-designer
Business rule: Only active customers (STATUS='A') with orders over $500 should load.
Use the mapping shell from the metadata-engineer output above.
```

#### Data Generator
```
@data-generator
Generate 30 rows for the ORDERS_TGT schema including edge cases and bad data.
Schema: [paste JSON mapping shell here]
```

---

## Agent Capabilities at a Glance

| Agent              | What It Produces                                          |
|--------------------|-----------------------------------------------------------|
| Orchestrator       | Task routing + quality gate validation                    |
| Metadata Engineer  | Column metadata tables + JSON mapping shells              |
| Test Designer      | Test case register + SQL validation scripts (4 categories)|
| Data Generator     | Synthetic dataset (Markdown table + CSV) with full coverage|

---

## ETL Testing Stages Covered

| Stage                  | Agent Responsible           |
|------------------------|-----------------------------|
| Requirements Analysis  | Orchestrator                |
| Source Validation      | Metadata Engineer           |
| Test Design            | Test Designer               |
| Data Extraction        | Metadata Engineer           |
| Transformation Validation | Test Designer            |
| Loading Validation     | Test Designer               |
| Reporting              | Test Designer (register)    |
| Closure                | Orchestrator (quality gate) |

---

## Tips

- Always run **Metadata Engineer first** — other agents depend on its output.
- Paste the JSON mapping shell directly into Test Designer and Data Generator prompts.
- Use the **Data Generator's bad data rows** as inputs to verify your SQL error-handling scripts.
- The `SCENARIO` column in generated datasets maps directly to test case IDs in the register.
