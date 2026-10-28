# Manual Setup Guide: Form and Workbook

About 20 minutes. You do this in your browser, signed in with your work or school Microsoft 365 account. Exact names and settings are in [form-and-excel-spec.md](form-and-excel-spec.md). Keep it open alongside this guide.

## Part A: Excel workbook (do this first)

1. Go to onedrive.com (OneDrive for Business) and choose **+ Add new > Folder**. Name it `CommuterFeedback`.
2. Open the folder and choose **+ Add new > Excel workbook**. Rename the file `CommuterFeedback.xlsx`. You can rename it by clicking the file name at the top of Excel.
3. Rename the sheet tab `Sheet1` to `Feedback`. To do this, right-click the tab and choose **Rename**.
4. Type the 10 headers into cells **A1 to J1**, exactly as written and in this order:
   `ResponseId | SubmittedAt | Mode | RoutedTeam | ServiceRouteStation | TripDate | FeedbackType | Rating | Comments | EmailProvided`
5. Select **A1:J2** and choose **Insert > Table**. Tick **My table has headers** and select **OK**.
6. Click inside the table, open the **Table Design** tab, and change **Table Name** to `FeedbackLog`. Press Enter.
7. Set the column formats. Select a column's cells below the header, then choose **Home > Number Format**:
   - SubmittedAt (B): **More number formats > Custom** `yyyy-mm-dd hh:mm`
   - TripDate (F): **Short Date**
   - Rating (H): **Number**, 0 decimal places
8. Leave the one empty table row as it is. **Don't type any data** into the table.
9. Close the tab. Excel Online saves automatically.

## Part B: Microsoft Form

1. Go to forms.office.com and choose **New Form**.
2. Set the title and description from the spec, section 1.
3. Add the 7 questions in order. For each one:
   - Choose **+ Add new** and pick the type: Choice, Text, Date or Rating.
   - Type the question title exactly as written.
   - Set the options and settings from the spec table. For Rating, set **Levels 5** and **Symbol Star**.
   - Turn the **Required** toggle on for questions 1, 3, 4, 5 and 6 only.
   - For questions 2 and 7, open **... > Subtitle** to add the hint text.
4. Open **... (top right) > Settings** and apply the form settings from the spec.

## Part C: Verify without submitting

1. In Forms, choose **Preview**. Check that:
   - the questions appear in the right order with the right spelling
   - an empty submission is blocked on questions 1, 3, 4, 5 and 6, but not on 2 or 7
   - each choice question has exactly 3 options and no "Other"
2. Close the preview **without submitting**. Don't submit any responses yet. A response submitted before the flow exists will never be logged.
3. In Excel, click inside the table and check that **Table Design > Table Name** shows `FeedbackLog` and that the headers match exactly.
4. Reply in the project thread with:
   - "Form and workbook created and verified", or what didn't match
   - whether "Only people in my organization" or "Anyone can respond" was used
   - anything in your tenant that looked different from this guide

Don't share the form link publicly. The 10 test cases in `test-data/test-cases.csv` are submitted in Phase 3, after the flow is built.
