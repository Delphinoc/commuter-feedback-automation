# Commuter Feedback Automation

A small portfolio project showing practical workflow automation and data analytics with standard Microsoft tools.

## Objective

Collect commuter feedback through Microsoft Forms, log every response to an Excel table, route each submission by transport mode (Bus, Train or Taxi), send the commuter an acknowledgement email, and summarise the feedback in a Power BI report.

## Proposed architecture

```
Microsoft Forms ──(trigger: new response)──> Power Automate cloud flow
                                               │
                                               ├─ Get response details
                                               ├─ Switch on Mode: Bus / Train / Taxi
                                               │     └─ set RoutedTeam (and per-mode email text)
                                               ├─ Excel Online (Business): add row to FeedbackLog
                                               └─ Office 365 Outlook: send acknowledgement
                                                        (only if an email was given)

Excel workbook in OneDrive for Business ──> Power BI Desktop (local .pbix report)
```

Design choices:

- **Excel Online in OneDrive, not SharePoint.** One workbook with one table is enough for a demo, and it needs no site permissions.
- **One table, not one sheet per mode.** Routing is shown by a `RoutedTeam` column and a per-mode email, so Power BI reads a single tidy table.
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
| 2 | Form and workbook spec, setup guide, test cases | Docs done, awaiting review. The user hasn't created the form or workbook yet |
| 3 | Build Power Automate flow and test with sample responses | Not started |
| 4 | Power BI summary report and screenshots | Not started |
| 5 | Final documentation for portfolio | Not started |

Nothing has been built or connected yet. The user has confirmed access to Forms, Power Automate, OneDrive for Business, Outlook and Power BI Desktop, but connector permissions are still unverified.
