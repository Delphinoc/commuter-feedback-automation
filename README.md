# Commuter Feedback Automation

A small portfolio project showing practical workflow automation and data analytics with standard Microsoft tools.

## Objective

Collect commuter feedback through Microsoft Forms, log every response to an Excel table, route each submission by transport mode (Bus, Train or Taxi), send the commuter an acknowledgement email, and summarise the feedback in a Power BI report.

## Proposed architecture

```
Microsoft Forms ──(trigger: new response)──> Power Automate cloud flow
                                               │
                                               ├─ Get response details
                                               ├─ Switch on Mode: Bus / Train / Taxi / Default
                                               │     └─ set one RoutedTeam variable
                                               ├─ Compose "Email provided" (Yes/No)
                                               ├─ Excel Online (Business): add row to FeedbackLog
                                               └─ Office 365 Outlook: send acknowledgement
                                                        (only if an email was given)

Excel workbook in OneDrive for Business ──> Power BI Desktop (local .pbix report)
```

Design choices:

- **Excel Online in OneDrive, not SharePoint.** One workbook with one table is enough for a demo, and it needs no site permissions.
- **One table, not one sheet per mode.** Routing is shown by a `RoutedTeam` column set from one variable, with a single Excel action after the Switch, so Power BI reads a single tidy table.
- **Standard connectors only.** Microsoft Forms, Excel Online (Business) and Office 365 Outlook are all standard, so no premium Power Automate licence is needed.
- **Power BI Desktop only.** The report is saved as a local `.pbix` file with screenshots. Publishing to the Power BI service would need a Pro licence and is out of scope.

## Feedback fields and data

Seven form questions (Mode, Service/route/station, Date of trip, Feedback type, Rating, Comments, optional Email) feed one Excel table, `FeedbackLog`, with 10 columns. The flow adds three of them itself: `ResponseId`, `SubmittedAt` and `RoutedTeam`, plus an `EmailProvided` Yes/No column. The workbook doesn't store the raw email address, and it doesn't track delivery status. The exact spec is in [docs/form-and-excel-spec.md](docs/form-and-excel-spec.md).

Routing, using demonstration labels only: Bus goes to Bus Operations, Train to Rail Operations, and Taxi to Taxi Services.

## Repository contents

| Path | Purpose |
|---|---|
| `docs/form-and-excel-spec.md` | Exact form questions and settings, Excel table schema, routing rules, and the schema check against the flow and Power BI |
| `docs/setup-guide.md` | Beginner-friendly manual steps to create the form and workbook |
| `docs/flow-build-guide.md` | Connector permission check, step-by-step flow build, failed-send guidance, run history and troubleshooting |
| `docs/flow-test-plan.md` | How to run the 10 test cases and the 10 checks to verify |
| `test-data/test-cases.csv` | 10 synthetic test cases with expected results. Test data only. Every comment is tagged `[TEST TCxx]` |

## Prerequisites

- A Microsoft 365 **work or school** account. The Forms trigger and Excel Online (Business) connector do not work with personal Microsoft accounts.
- Microsoft Forms, Power Automate, OneDrive for Business and Outlook enabled for that account, and no tenant policy (DLP) blocking the three connectors.
- Power BI Desktop (free) on a Windows 10/11 PC.
- Test data only. No real commuter personal data. Test cases live in `test-data/`, and every comment submitted from them starts with `[TEST TCxx]` so the rows can be identified.

## Implementation status

| Phase | Scope | Status |
|---|---|---|
| 1 | Feasibility, architecture, project setup | Done, approved |
| 2 | Form and workbook spec, setup guide, test cases | Done, approved. The user created and verified the form and workbook, with no responses submitted |
| 3 | Build Power Automate flow and test with sample responses | Build guide and test plan done, awaiting review. The flow hasn't been built or tested yet |
| 4 | Power BI summary report and screenshots | Not started |
| 5 | Final documentation for portfolio | Not started |

The form and workbook exist (user-verified). No flow exists yet, and connector permissions are still unverified. Part A of the build guide checks them.
