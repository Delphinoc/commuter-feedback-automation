# Form and Excel Specification

The exact spec for the Microsoft Form, the Excel table and the routing rules. Names and spellings matter: the Power Automate flow matches on them.

## 1. Microsoft Form

**Title:** `Commuter Feedback (Demo)`

**Description:** `Demonstration form for a portfolio project. Feedback is not sent to any real transport operator. Please do not enter personal details other than an optional email address.`

| # | Question title (exact) | Forms type | Required | Settings |
|---|---|---|---|---|
| 1 | Mode of transport | Choice | Yes | Options, in this order and spelling: `Bus`, `Train`, `Taxi`. Single answer. "Add 'Other' option" off. Shuffle off. |
| 2 | Service, route or station | Text (short) | No | Long answer off. Subtitle: `e.g. Bus 95, Clementi MRT, taxi from Changi Airport` |
| 3 | Date of trip | Date | Yes | None (Forms cannot block future dates) |
| 4 | Feedback type | Choice | Yes | Options: `Compliment`, `Complaint`, `Suggestion`. Single answer. "Other" off. Shuffle off. |
| 5 | Overall rating | Rating | Yes | Levels: 5. Symbol: Star. Leave the 1/5 labels blank, or set them to `Very poor` / `Excellent` |
| 6 | Comments | Text | Yes | Long answer **on** |
| 7 | Email for acknowledgement (optional) | Text (short) | No | Long answer off. Subtitle: `Leave blank if you do not want a reply.` |

**Form settings** (... menu > Settings):

- Who can fill out this form: **Only people in my organization** while testing. This is always allowed and stops spam. "Anyone can respond" is more realistic, but some tenants block it, and the flow works the same either way.
- Record name: **off**. The flow takes the email only from question 7.
- One response per person: **off**, so the 10 test cases can be submitted from one account.
- Accept responses: **on**.
- Email notification of each response: **off**. Power Automate handles notifications.

## 2. Excel workbook and table

- **File:** `CommuterFeedback.xlsx` in OneDrive for Business, folder `CommuterFeedback/`
- **Sheet:** `Feedback`
- **Table name:** `FeedbackLog`. The flow selects the table by this name.

| # | Column (exact header) | Data type | Excel number format | Filled from | Example |
|---|---|---|---|---|---|
| 1 | ResponseId | Whole number | General | Forms trigger: Response Id | `7` |
| 2 | SubmittedAt | Date/time (UTC) | `yyyy-mm-dd hh:mm` | Forms: Submission time | `2026-10-28 10:55` |
| 3 | Mode | Text | Text | Q1 | `Train` |
| 4 | RoutedTeam | Text | Text | Flow routing (section 3) | `Rail Operations` |
| 5 | ServiceRouteStation | Text, may be blank | Text | Q2 | `Clementi MRT` |
| 6 | TripDate | Date | Short Date | Q3 | `2026-10-27` |
| 7 | FeedbackType | Text | Text | Q4 | `Complaint` |
| 8 | Rating | Whole number 1 to 5 | Number, 0 decimals | Q5 | `2` |
| 9 | Comments | Text | Text | Q6 | `Train was delayed 15 min` |
| 10 | EmailProvided | Text: `Yes` / `No` | Text | Flow: is Q7 non-blank? | `Yes` |

Design notes:

- **No raw email address column.** The flow reads the address straight from the form response to send the acknowledgement, so the workbook doesn't need to store it. `EmailProvided` records only whether an address was given. That is a fact about the input, not about delivery, so it doesn't claim the email arrived. That's why there's no `AckSent` column.
- **Header names have no spaces or symbols.** That way they appear cleanly as Power Automate dynamic content and table columns.
- **`SubmittedAt` is stored in UTC**, as Forms provides it. The implemented flow keeps UTC.

## 3. Routing

| Mode (Q1 answer) | RoutedTeam |
|---|---|
| Bus | Bus Operations |
| Train | Rail Operations |
| Taxi | Taxi Services |

The team names are demonstration labels only. The flow writes them to the `RoutedTeam` column and nothing is sent to any team or operator. The Switch has a Default case that writes `Unrouted`, so a renamed option shows up in the data instead of failing silently.

## 4. Schema check against the flow

- Every column maps directly to one trigger or "Get response details" field, or to one simple expression.
- The data lives in a formatted table, which "Add a row into a table" requires.
- Routing needs one Switch on `Mode`, and the three options are fixed choices, so spelling can't drift.
- The acknowledgement needs one Condition on `EmailProvided`, the same value written to the table.
- It's one flat table with one row per response and typed date and number columns, so it can be filtered, pivoted or charted in Excel without reshaping.
