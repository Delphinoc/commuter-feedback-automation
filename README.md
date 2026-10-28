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
                                               ├─ Excel Online (Business): add row to table
                                               └─ Office 365 Outlook: send acknowledgement
                                                        (only if an email was given)

Excel workbook in OneDrive for Business ──> Power BI Desktop (local .pbix report)
```

Design choices:

- **Excel Online in OneDrive, not SharePoint.** One workbook with one table is enough for a demo, and it needs no site permissions.
- **One table, not one sheet per mode.** Routing is shown by a `RoutedTeam` column and a per-mode email, so Power BI reads a single tidy table.
- **Standard connectors only.** Microsoft Forms, Excel Online (Business) and Office 365 Outlook are all standard, so no premium Power Automate licence is needed.
- **Power BI Desktop only.** The report is saved as a local `.pbix` file with screenshots. Publishing to the Power BI service would need a Pro licence and is out of scope.

## Feedback fields

| # | Form question | Type | Required |
|---|---|---|---|
| 1 | Mode of transport | Choice: Bus, Train, Taxi | Yes |
| 2 | Service, route or station | Short text (e.g. "Bus 190", "Jurong East") | No |
| 3 | Date of trip | Date | Yes |
| 4 | Feedback type | Choice: Compliment, Complaint, Suggestion | Yes |
| 5 | Overall rating | Rating 1 to 5 | Yes |
| 6 | Comments | Long text | Yes |
| 7 | Email for acknowledgement | Short text | No |

Columns the flow adds to the Excel table: `ResponseId`, `SubmittedAt`, `RoutedTeam`, `AckSent`.

## Prerequisites

- A Microsoft 365 **work or school** account. The Forms trigger and Excel Online (Business) connector do not work with personal Microsoft accounts.
- Microsoft Forms, Power Automate, OneDrive for Business and Outlook enabled for that account, and no tenant policy (DLP) blocking the three connectors.
- Power BI Desktop (free) on a Windows 10/11 PC.
- Test data only. No real commuter personal data.

## Implementation status

| Phase | Scope | Status |
|---|---|---|
| 1 | Feasibility, architecture, project setup | Done, awaiting review |
| 2 | Create form and Excel table (manual, by the account owner) | Not started |
| 3 | Build Power Automate flow and test with sample responses | Not started |
| 4 | Power BI summary report and screenshots | Not started |
| 5 | Final documentation for portfolio | Not started |

Nothing has been built or connected yet. No Microsoft account access has been verified.
