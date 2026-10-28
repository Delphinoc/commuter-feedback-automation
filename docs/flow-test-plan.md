# Flow Test Plan

This tests the flow with the 10 synthetic cases in [`../test-data/test-cases.csv`](../test-data/test-cases.csv). Start only after the flow is saved and On, and only after Phase 3 review.

## Before you start

- **Close `CommuterFeedback.xlsx`.** Writing from the flow while the workbook is open for editing can cause conflicts.
- Have the form's response link ready: in Forms, choose **Collect responses** and copy the link. Open it in a browser tab where you're signed in.
- Wherever the CSV says `<your own address>`, type **your own** work or school email. Never use anyone else's address.
- Type each comment exactly as written, including the `[TEST TCxx]` tag.

## Step 1: one smoke test (TC01 only)

1. Submit TC01.
2. In run history, wait until the run shows **Succeeded**. That usually takes under a few minutes.
3. Open the workbook and check:
   - **Blank first row.** The table was created with one empty row. If the new data appears below an empty row, right-click the empty row and choose **Delete > Table Rows**. Do this once only, then close the workbook.
   - The values match TC01, and RoutedTeam = `Bus Operations` and EmailProvided = `Yes`.
4. Check that one acknowledgement arrived in your inbox.
5. **Check the data types.** TC01 should now be in row 2. In empty cells **outside** the table, for example L1, L2 and L3, enter:
   - `=ISNUMBER(B2)` to check SubmittedAt
   - `=ISNUMBER(F2)` to check TripDate
   - `=ISNUMBER(H2)` to check Rating

   **TRUE** means a real date/time or number. **FALSE** means it was stored as text. Delete these check cells afterwards.
6. If any check is FALSE, **don't continue**. Note which column it was and what the run's Excel step input shows, then report back. The fix is a small expression change, which I'll confirm before you edit anything.

## Step 2: the remaining 9 cases

Submit TC02 to TC10 in order, waiting about 30 seconds between each. Then wait until run history shows 10 runs, and open the workbook.

## Step 3: verify

| # | Check | Expected | Pass? |
|---|---|---|---|
| 1 | Runs in run history | 10 runs, all **Succeeded** | |
| 2 | Data rows in `FeedbackLog` | Exactly **10**: one per submission, no duplicates, ResponseIds unique | |
| 3 | Blank rows | **None** | |
| 4 | RoutedTeam | Bus rows (TC01-04) `Bus Operations`; Train rows (TC05-07) `Rail Operations`; Taxi rows (TC08-10) `Taxi Services`. No `Unrouted` | |
| 5 | EmailProvided | `Yes` for TC01, 03, 05, 07, 08, 10; `No` for TC02, 04, 06, 09 | |
| 6 | Acknowledgements sent | **6** emails in your inbox (TC01, 03, 05, 07, 08, 10) | |
| 7 | Acknowledgements skipped | For TC02, 04, 06, 09, the run shows the Condition going to **False**, and **Send an email (V2)** is shown as skipped or not run | |
| 8 | Optional field blank | TC03 and TC10 have an empty ServiceRouteStation, and those runs still succeeded | |
| 9 | Data types | `ISNUMBER` is TRUE for SubmittedAt, TripDate and Rating (check one row, as in step 1) | |
| 10 | Email wording | No promise of forwarding or action, and the demo disclaimer is present | |

For check 7, open one of the "No" runs, expand **Condition** and confirm which branch ran.

## Step 4: report back

Reply in the project thread with:
- the Pass/Fail for each of the 10 checks
- for any Fail: the run's failed step name and its error message, copied from run history

Don't screenshot or share real email addresses. Your own address appears only in your inbox.
