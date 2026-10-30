# Power Automate Flow Build Guide

You build **one automated cloud flow**: it logs each form response to `FeedbackLog` and sends an acknowledgement email when an address is given. It takes about 30 to 45 minutes. Do everything at make.powerautomate.com, signed in with the same work or school account that owns the form and the workbook.

**Final shape of the flow**

```
When a new response is submitted                  (Microsoft Forms trigger)
Get response details                              (Microsoft Forms)
Initialize variable: RoutedTeam                   (String, empty)
Switch on "Mode of transport"
   Bus     → Set variable RoutedTeam = Bus Operations
   Train   → Set variable RoutedTeam = Rail Operations
   Taxi    → Set variable RoutedTeam = Taxi Services
   Default → Set variable RoutedTeam = Unrouted
Compose: Email provided                           ("Yes" or "No")
Add a row into a table                            (Excel Online (Business), runs once)
Condition: Email provided = Yes
   True  → Send an email (V2)                     (Office 365 Outlook)
   False → (nothing)
```

The Excel action comes **before** the email step. A response is always logged, even if the acknowledgement fails.

> **About IDs.** Forms gives each question an internal ID (for example `r1a2b3...`), and the ID is different in every tenant. This guide never asks you to type one. Always pick answers from the **Dynamic content** list by their question title. In expressions, insert them from the **Dynamic content** tab of the expression editor, and the designer writes the correct reference for you.

---

## Part A: Connector permission check (before building)

1. Go to make.powerautomate.com > **Connections**. It may be under **Data** or **More** in the left menu.
2. Select **+ New connection** and create, one at a time:
   - **Microsoft Forms**
   - **Excel Online (Business)**
   - **Office 365 Outlook**
3. Each one should sign in with your work account and then appear in the list with status **Connected**.
4. While you're there, confirm that none of the three shows a **Premium** diamond label.

**Stop, and don't build any further, if any of these happens:**
- a connection can't be created, or shows an error
- any of the three is labelled Premium, or you're asked to start a trial
- later, saving the flow shows a message about a **data loss prevention (DLP) policy**

All three are standard connectors. Microsoft 365 work and school accounts normally include them, but your organisation's policy decides. Don't start any trial or paid plan.

---

## Part B: Build the flow

The designer buttons are written generically ("+ > Add an action"). The new and classic designers look slightly different, but the action names are the same.

**Tip:** rename each action right after you add it, before you use its outputs anywhere. To rename, select the action and edit its title. Renaming later can break expressions you've already written.

### B1. Create the flow and the trigger

1. Choose **+ Create > Automated cloud flow**.
2. Flow name: `Commuter Feedback - Log and Acknowledge`
3. Trigger: search for "Forms" and choose **When a new response is submitted** (Microsoft Forms). Don't choose the one marked [DEPRECATED]. Select **Create**.
4. In the trigger, set **Form Id** to `Commuter Feedback (Demo)` from the dropdown.

### B2. Get response details

1. Choose **+ > Add an action** and pick **Get response details** (Microsoft Forms).
2. **Form Id**: choose `Commuter Feedback (Demo)`.
3. **Response Id**: from Dynamic content, pick **Response Id** under *When a new response is submitted*. The full label may read "List of response notifications Response Id".
   - If the designer wraps the action in **Apply to each**, that's fine. Keep building inside it. Every later step then goes inside the loop.

### B3. Initialize the RoutedTeam variable

This must be at the top level, not inside the Switch.

1. Choose **+ > Add an action** and pick **Initialize variable** (under Variables).
2. Name: `RoutedTeam`. Type: **String**. Value: leave it empty.

### B4. Switch on Mode of transport

1. Choose **+ > Add an action** and pick **Switch** (under Control).
2. **On**: from Dynamic content (*Get response details*), pick **Mode of transport**.
3. Case 1: set **Equals** to `Bus`. Inside it, add **Set variable**, with Name `RoutedTeam` and Value `Bus Operations`.
4. Choose **+ Add case** for Case 2: **Equals** `Train`. Add **Set variable** with `RoutedTeam` = `Rail Operations`.
5. Case 3: **Equals** `Taxi`. Add **Set variable** with `RoutedTeam` = `Taxi Services`.
6. **Default**: add **Set variable** with `RoutedTeam` = `Unrouted`.

Case values are case-sensitive, so they must match the form options exactly: `Bus`, `Train`, `Taxi`. No quotes, no spaces.

### B5. Compose "Email provided"

Below the Switch (not inside any case):

1. Add **Compose** (under Data Operation) and rename it to `Email provided`.
2. Click into **Inputs** and open the **expression editor** (the *fx* icon, or the Expression tab).
3. Type `if(empty(trim(coalesce(`
4. Switch to the **Dynamic content** tab inside the editor and select **Email for acknowledgement (optional)**. This inserts the correct reference for your form.
5. Then type `,''))),'No','Yes')` and select **Add** or **OK**.

The finished expression has this shape. The part in angle brackets is what the designer inserted:

```
if(empty(trim(coalesce(<Email for acknowledgement (optional)>,''))),'No','Yes')
```

`coalesce(...,'')` turns an unanswered question into an empty string, `trim` ignores spaces only, and `empty` checks for blank. The result is always `Yes` or `No`. Both the Excel column and the email condition use this one output, so they can never disagree.

### B6. Add a row into a table

1. Add **Add a row into a table** (Excel Online (Business)). Don't use the [DEPRECATED] version.
2. Location: **OneDrive for Business**. Document Library: **OneDrive**. File: browse to `/CommuterFeedback/CommuterFeedback.xlsx`. Table: `FeedbackLog`.
3. The 10 column fields appear. Map them as follows:

| Excel column | Value to insert |
|---|---|
| ResponseId | Dynamic content: **Response Id** (from the trigger) |
| SubmittedAt | Expression: type `formatDateTime(`, insert the dynamic content **Submission time** (*Get response details*), then type `,'yyyy-MM-dd HH:mm')` |
| Mode | Dynamic content: **Mode of transport** |
| RoutedTeam | Dynamic content: variable **RoutedTeam** |
| ServiceRouteStation | Dynamic content: **Service, route or station** |
| TripDate | Dynamic content: **Date of trip** |
| FeedbackType | Dynamic content: **Feedback type** |
| Rating | Dynamic content: **Overall rating** |
| Comments | Dynamic content: **Comments** |
| EmailProvided | Dynamic content: **Outputs** of `Email provided` |

4. Leave **DateTime Format** empty for now. The test plan checks whether dates arrive correctly.

Submission time from Forms is in UTC, so `SubmittedAt` stays in UTC, as the spec says.

### B7. Condition and acknowledgement email

1. Below the Excel action, add **Condition** (under Control).
2. Left value: the **Outputs** of `Email provided`. Operator: **is equal to**. Right value: `Yes`.
3. In **True**, add **Send an email (V2)** (Office 365 Outlook):
   - **To**: expression `trim(`, then insert **Email for acknowledgement (optional)**, then `)`
   - **Subject**: `We received your commuter feedback (demo)`
   - **Body** (you can insert the dynamic content where shown):

     > Hello,
     >
     > Thank you for using the Commuter Feedback demo form. Your response has been recorded.
     >
     > Mode of transport: *[Mode of transport]*
     > Feedback type: *[Feedback type]*
     > Date of trip: *[Date of trip]*
     >
     > Please note that this is a demonstration project. Your feedback is not forwarded to any transport operator, and no further action or reply will follow.
     >
     > This is an automated message. Please do not reply.

4. Leave **False** empty. When no email is given, nothing is sent and the run still succeeds. A missing address can never cause a send failure, because the send action is only reached when `Email provided` is `Yes`.

### B8. Save

1. Select **Save**. Read any warning banner. A DLP or licence warning is a stop-and-report point (Part A).
2. Use **Flow checker** (the stethoscope icon) and fix any errors it lists.
3. A saved automated flow is **On**. Don't submit any form responses yet. Testing follows [flow-test-plan.md](flow-test-plan.md).

---

## Part C: If an acknowledgement send fails

Kept simple on purpose, with no retries or extra error handling:

- The row is already in Excel, because logging happens before sending. Only the email is missing.
- The run shows as **Failed** in run history, with the failed step in red. That's the record that the email wasn't sent.
- The usual cause is an address with a typo. Forms can't check email format. The fix is to check the address and, if needed, email the person by hand.
- **Don't use "Resubmit" on a failed run.** It re-runs the whole flow and adds a **duplicate Excel row**. If you resubmit anyway, delete the duplicate row afterwards.

## Part D: Run history and troubleshooting

**Open run history:** go to **My flows**, open `Commuter Feedback - Log and Acknowledge`, and look at **28-day run history**. Select a run to see each step, with a green tick or a red cross. Select a step to see its **inputs** and **outputs**. This is how you check what Forms actually sent, for example the date format.

| Symptom | Likely cause | Fix |
|---|---|---|
| No run appears after submitting | The flow is off, the wrong form was picked in the trigger, or the trigger is slow | Check that the flow is **On** and that the trigger shows the right form. Wait a few minutes and refresh |
| RoutedTeam is `Unrouted` | The case value doesn't match the option text | Compare the Switch values with the form options. They're case-sensitive, and watch for trailing spaces |
| Excel step fails: file or table not found | The workbook was moved or renamed, or the table was renamed | Re-pick File and Table in the action |
| Excel step fails: locked or conflict | The workbook was open and being edited at the same time | Close the workbook while testing, and open it only to check results |
| The row appears late | The Excel connector can take up to about 30 seconds to show changes | Refresh the workbook after a short wait |
| Send email fails | Bad address, or the Outlook connection has expired | Check the address. Open **Connections** and fix the Outlook connection if it shows an error |
| "Connection not authorised" or "Fix connection" banner | A connection's sign-in has expired | Re-sign the connection under **Connections** |
| A DLP policy message on save | A tenant policy is blocking a connector | Stop. This is an organisation setting, so ask your IT admin rather than working around it |
