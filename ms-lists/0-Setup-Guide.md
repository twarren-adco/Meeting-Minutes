# Weinam Ck Car Park: moving the checklist to Microsoft Lists

This pack rebuilds the Weinam start-up checklist and contact list in Microsoft Lists, so anyone with an ADCO Microsoft 365 login can view and edit it without a Claude account. Allow about an hour for the full setup.

## What's in the pack

| File | What it is |
|---|---|
| `1-Weinam-Tasks.csv` | All 70 tasks as at 29 Oct 2026, in checklist order |
| `2-Weinam-Contacts.csv` | All 20 contacts, with project details below |
| `3-Row-colours-view-format.json` | Row colours: green complete, red urgent/overdue, yellow due within 7 days |
| `4-Status-column-format.json` | Coloured status pills, showing the completion date on completed tasks |
| `5-Key-task-column-format.json` | ★ for key tasks |

Project details for the list description: **Weinam Creek Carpark, 12 Meissner St, Redland Bay QLD 4165. Client: EDQ.**

**Pick one home for the lists.** The project team's Teams channel is the easiest (Teams › channel › **+** › Lists). A SharePoint project site works too. Create both lists in the same place.

**Before you go live:** stop editing the Claude page once the lists are set up, so the team only has one version of the truth.

---

## Step 1: Create the Tasks list

1. Open **Microsoft Lists** (or the Lists tab in Teams), then **New list › From CSV**, and upload `1-Weinam-Tasks.csv`.
2. On the preview screen, set each column type:

   | CSV column | Type | Rename it to (after import) |
   |---|---|---|
   | Title | Single line of text | **Task** |
   | Ref | Single line of text | Ref |
   | Group | Choice | Group |
   | Section | Choice | Section |
   | ActionDetail | Multiple lines of text | **Action / Detail** |
   | Champion | Choice | Champion |
   | Support | Choice | **Support / Input** |
   | StartBy | Date | **Start By** |
   | CompleteBy | Date | **Complete By** |
   | Status | Choice | Status |
   | CompletedOn | Date | **Completed On** |
   | LiveNotes | Multiple lines of text | **Live Notes** |
   | KeyTask | Yes/No | **Key Task** |
   | SortOrder | Number | Sort order |

3. Name the list **Weinam Start-Up Checklist** and create it.
4. Rename the columns as shown above: click the column header › **Column settings › Edit**. Only change the display name. Keep the CSV header names as they are until the import has finished, because the formatting code in this pack relies on those internal names (`CompleteBy`, `CompletedOn`, `Status`).
5. Edit the **Status** column's choices to be exactly: `Not Started`, `In Progress`, `Urgent`, `Complete`, `N/A this project`. Set the default to `Not Started`. Six tasks currently have no status, and they'll show as "Not set" until someone picks one.

## Step 2: Add Champion people (needed for emails)

Emails need a real person, not initials. Add a **Person** column called **Champion Person** (and optionally **Support Person**). Then fill it in:

1. Switch to **Edit in grid view**.
2. Filter Champion to one set of initials, type the person's name in the first row, and copy it down the rest. Repeat for each set of initials.

| Initials | Person (from the contact list) | Tasks |
|---|---|---|
| EW | Emily Wyatt | 23 |
| TW | Tom Warren | 17 |
| TCH | Tyler Cuthbert-Hough | 8 |
| NS | Nathan Stevenson | 6 |
| BE | Ben Evans | 1 |
| SW | *not in the contact list, add when known* | 5 |
| HSE | *HSE Manager, not in the contact list* | 4 |
| Estimator | *not in the contact list* | 3 |
| WM | *not in the contact list* | 3 |

Once every row has a Champion Person you can hide or delete the old initials column.

## Step 3: Colour the rows and the status column

1. **Row colours:** open the view menu (top right, e.g. "All Items") › **Format current view › Advanced mode**. Paste the contents of `3-Row-colours-view-format.json` and click **Save**.
   - Complete = green; Urgent, or past its Complete By date = red; Complete By within the next 7 days = yellow. N/A tasks aren't coloured.
   - Colours are worked out from today's date each time the page loads.
2. **Status pills:** Status column header › **Column settings › Format this column › Advanced mode**. Paste `4-Status-column-format.json`.
3. **Key task star:** the same steps on the Key Task column, with `5-Key-task-column-format.json`.

Repeat step 1 on each view you create in step 4. Formatting is saved per view.

## Step 4: Create the views

| View | How to set it up | Replaces on the Claude page |
|---|---|---|
| **Checklist** (make it the default) | List view; **Group by** Group; **Sort by** Sort order; 100 items per page | Checklist tab |
| **Open tasks** | Same as Checklist, plus **Filter**: Status is not equal to Complete | "Hide completed" button |
| **My tasks** | Filter: Champion Person is equal to `[Me]` | Person filter |
| **Key tasks** | Filter: Key Task is equal to Yes; sort by Complete By | Key Dates & Tasks panel |
| **Timeline** | **Calendar** view; Start = Start By, End = Complete By | Timeline tab (drag to reschedule works here too) |
| **Board** | **Board** view, grouped by Status | Quick status changes by dragging cards |

**Dashboard:** Lists has no dashboard view. The simplest substitute is a SharePoint page with the **Open tasks** and **Key tasks** views as list web parts, plus a **Quick chart** web part counting tasks by Status.

## Step 5: Stamp the Completed On date automatically

This needs Power Automate, because Lists rules can't set a value.

1. In the list: **Integrate › Power Automate › Create a flow**, then choose **Automated cloud flow**.
2. Trigger: **SharePoint – When an item is created or modified**, pointed at this site and list.
3. Add a **Condition**: `Status Value` is equal to `Complete` **and** `Completed On` is equal to (empty).
   - If yes: **Update item** with Completed On = `utcNow()`.
4. Add a second **Condition** (in the "If no" branch): `Status Value` is not equal to `Complete` **and** `Completed On` is not equal to (empty).
   - If yes: **Update item** with Completed On = `null`.

The flow's own update triggers it once more, but neither condition is true the second time, so it stops there.

## Step 6: Email people when they're assigned a task

**Option A: no Power Automate (quickest)**
In the list: **Automate › Create a rule › Someone is assigned** › pick **Champion Person** › **send email to** Champion Person. Do the same for Support Person if you added it. The emails are plain, with the task name and a link to it.

**Option B: a fuller email through Power Automate**
1. Trigger: **When an item is created or modified** on this list.
2. **Get changes for an item or a file (properties only)**. Set *Since* to the trigger window start token.
3. **Condition**: `Has Column Changed: Champion Person` is true **and** `Champion Person Email` is not empty.
4. If yes: **Send an email (V2)**:
   - **To:** Champion Person Email
   - **Subject:** `You've been assigned: [Task] – Weinam Ck Car Park`
   - **Body:** Ref, Task, Start By, Complete By, Status, Action / Detail, and the item's **Link to item**.

Versioning must be on for "Get changes" to work. It's on by default for new lists.

## Step 7: Create the Contacts list

1. **New list › From CSV**, then upload `2-Weinam-Contacts.csv`.
2. Column types: Title (text, rename to **Contact**), Category (**Choice**), Role, Company, Initials, Mobile (all single line of text), Email (single line of text), SortOrder (Number).
3. Category choices: `Project Team`, `Client`, `Consultant Team`. To add a new category later, edit the Category column's choices. This replaces the "+ Add category" button.
4. Default view: **Group by** Category, **Sort by** SortOrder.

Contacts with no name were imported as "TBC – *role*" (Landscape, Services – Fire). Replace these when you have a name.

## Step 8: Share

- **Teams channel:** everyone in the channel can view and edit automatically.
- **SharePoint site:** add people as site Members to let them edit.
- **People outside ADCO** (consultants, EDQ): they need Microsoft 365 guest access, which ADCO IT controls.

**Privacy:** the contact list holds names, mobile numbers and emails. Keep it inside ADCO's Microsoft 365 environment, and check with IT before giving external guests access to it.

## What doesn't carry over

| On the Claude page | In Lists |
|---|---|
| Ctrl+Z undo | Use **Version history** on an item to restore it |
| Drag a bar's end to change only one date | Edit the date directly, or drag in Calendar view |
| Excel export | Built in: **Export › Export to Excel** |
| Paste a block from Excel | Works in **Edit in grid view** |
