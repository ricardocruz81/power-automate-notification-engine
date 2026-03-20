# How to Reuse These Flows on Any SharePoint List

Every flow in this project is designed to work with **any SharePoint list** by changing just a few variables. No code changes needed.

---

## Quick Start — 3 Steps to Adapt Any Flow

### Step 1 — Import the Flow

1. Go to **power.microsoft.com → My Flows → Import → Import Package (Legacy)**
2. Upload the `.json` file from the `flows/` folder
3. Map the connections (SharePoint + Office 365 Outlook) to your account
4. Click **Import**

---

### Step 2 — Update the Variables

Each flow has a **variables block** at the top. Update these after importing:

Open the flow → Edit → find the **"Initialize variable"** actions at the start.

| Variable | What to Change it To |
|----------|----------------------|
| `SiteAddress` | Your SharePoint site URL, e.g. `https://yourorg.sharepoint.com/sites/mysite` |
| `ListName` | The exact name of your SharePoint list or library |
| `NotifyColumn` | The column that holds the person to notify (e.g. `AssignedTo`, `DocumentOwner`) |
| `ManagerEmail` | Your manager's email address for escalations and summaries |

---

### Step 3 — Match the Column Names

Each flow uses SharePoint column **internal names** in filter queries and expressions. Check your column internal names match. 

To find internal names in SharePoint:
1. Go to **List Settings → Columns → click a column name**
2. Look at the URL — the `Field=` parameter shows the internal name

**Common mismatches to check:**

| Flow Expects | Your Column Might Be Called |
|---|---|
| `Status` | `ApprovalStatus`, `TaskStatus` |
| `DueDate` | `TaskDueDate`, `ReviewDate`, `DeadlineDate` |
| `AssignedTo` | `Owner`, `ResponsibleOfficer`, `DocumentOwner` |
| `SubmittedDate` | `Created`, `DateSubmitted` |
| `ExpiryDate` | `ReviewDate`, `ContractEndDate`, `ValidUntil` |

Update any expressions in the flow that reference these column names.

---

## Flow-by-Flow Adaptation Guide

### 01 — Item Changed Alert
**Minimum changes:** SiteAddress, ListName  
**Optional:** Change which column triggers the condition (default: `Status`)

### 02 — Daily Overdue Scan
**Minimum changes:** SiteAddress, ListName, ManagerEmail  
**Column to check:** `DueDate` — rename in the filter query if different  
**Status to exclude:** Default is `Complete` and `Cancelled` — update the filter if your statuses differ

### 03 — Approval Pending Reminder
**Minimum changes:** SiteAddress, ListName, ManagerEmail  
**Column for pending status:** Default checks `ApprovalStatus eq 'Pending'` — update if your column/value differs  
**Chase threshold:** Default 3 days, escalate at 7 days — update the variables

### 04 — Expiry Warning
**Minimum changes:** SiteAddress, ListName, NotifyColumn  
**Date column:** Default is `ExpiryDate` — update the filter query if yours is named differently  
**Excluded status:** Default excludes `Archived` and `Cancelled`

---

## Adding a New List Without Re-importing

If you want the same flow to cover **multiple lists**, you can parameterise the trigger using a parent flow:

1. Create a new flow: **Manually trigger a flow** → pass in SiteAddress and ListName as inputs
2. Call each child notification flow using the **Run a Child Flow** action
3. Pass the variables through

This lets you run the same overdue scan against 5 different lists without duplicating the flow.

---

## Troubleshooting

| Problem | Likely Cause | Fix |
|---------|-------------|-----|
| Flow not triggering | Wrong SiteAddress or ListName | Double-check exact spelling, case-sensitive |
| Empty results in GetItems | Filter query column name wrong | Check internal names in List Settings |
| Email not received | Wrong column for recipient email | Confirm the column holds an email address, not a person object — use `?['Email']` to extract |
| `null` in email body | Dynamic content expression wrong | Wrap in `if(empty(...), 'N/A', ...)` to handle nulls |
| Flow failing on Apply to Each | Too many items (>100) | Add pagination: set Top = 500 and enable pagination in GetItems settings |
