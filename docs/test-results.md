# Test Results

**Test date:** 2026-10. **Environment:** the user's Microsoft 365 work or school tenant. **Data:** the 10 synthetic cases in [`../test-data/test-cases.csv`](../test-data/test-cases.csv).

## Who verified what

The tests were **run and checked by hand by the project owner** in their own Microsoft 365 account. Claude Code, which prepared the documentation, had no access to the Microsoft account, so it did not run or observe any of these tests. The results below are as the owner reported them. The owner later supplied screenshots of the table and run history (see [screenshots](screenshots/)). Claude checked those screenshots against the results, which is a visual check of evidence the owner provided, not an independent test.

## Results

| # | Check | Result |
|---|---|---|
| 1 | Power Automate runs | **10 / 10 succeeded** |
| 2 | Rows logged in `FeedbackLog` | **10**, one per submission |
| 3 | Duplicate or blank rows | **None** |
| 4 | Routing by Switch | Bus **4** → Bus Operations, Train **3** → Rail Operations, Taxi **3** → Taxi Services. None `Unrouted` |
| 5 | Acknowledgements sent and received | **9**, all to the owner's own address |
| 6 | Acknowledgements skipped (no email given) | **1**. The Condition took the False branch and no send was attempted |
| 7 | Data types | `SubmittedAt` stored as a real date/time, `TripDate` as a real date, `Rating` as a number |
| 7a | `EmailProvided` column | `Yes` ×9, `No` ×1 (TC02), as seen in the table screenshot |
| 8 | Real operators contacted | **None**. Routing labels are written to the table only |

## Difference from the planned test cases

The test plan expected **6** acknowledgements and **4** skips. In the fixture file, TC02, TC04, TC06 and TC09 have no email address.

During the run, the owner typed their own email address for **TC03 to TC10**, including TC04, TC06 and TC09, which were planned without one. TC01 also included an address, as planned. That left **TC02** as the only submission without an email. So the actual split was **9 sent and 1 skipped**. The table screenshot confirms this: `EmailProvided` is `No` for TC02 and `Yes` for the other nine.

The fixture file is unchanged, and still records the original plan. The results above describe what was actually submitted.

**What this means for coverage:** both paths of the Condition were exercised. The "email present" path ran 9 times and the "no email" path ran once. The skip path was confirmed on one case rather than four.

## Not covered by these tests

- A mistyped or invalid email address. That would produce a failed run, but the row would still be logged, as described in the build guide.
- A submission that reaches the `Default` (`Unrouted`) branch. On the live form, this would happen if a commuter chose the "Other" option on *Mode of transport*, or if an option were renamed.
- Many submissions arriving at the same moment.

## Screenshot cross-check (by Claude)

- **Table:** 10 data rows (rows 2 to 11) with no blank rows, ResponseID 1 to 10, routing correct for every mode, and TC03 and TC10 with ServiceRouteStation blank as planned.
- **Run history:** 10 runs, all Succeeded. Their local start times are 8 hours ahead of the UTC `SubmittedAt` values in the table, and they line up one to one. That fits a UTC+8 time zone.
