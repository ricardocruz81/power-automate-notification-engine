# Power Automate Notification Engine

A reusable, generic notification system built in Power Automate that sends alerts when SharePoint items change, statuses become overdue, or approvals are pending. Designed as a modular set of flow patterns that can be adapted across any SharePoint-based system.

---

## Architecture

```
┌──────────────────────────────────────────────┐
│              Notification Engine              │
│                                              │
│  ┌────────────────┐  ┌────────────────────┐  │
│  │  Trigger Flows │  │  Scheduled Flows   │  │
│  │                │  │                    │  │
│  │ • Item changed │  │ • Daily overdue    │  │
│  │ • Item created │  │ • Weekly summary   │  │
│  │ • Status set   │  │ • Expiry warnings  │  │
│  └───────┬────────┘  └────────┬───────────┘  │
│          └──────────┬─────────┘              │
│                     ▼                        │
│  ┌──────────────────────────────────────┐    │
│  │   Central Notification Dispatcher    │    │
│  │   (Condition routing by alert type)  │    │
│  └──────────────────┬───────────────────┘    │
│                     │                        │
│        ┌────────────┼─────────────┐          │
│        ▼            ▼             ▼          │
│   Email          Teams       SharePoint      │
│   Notification   Message     Log Entry       │
└──────────────────────────────────────────────┘
```

---

## Project Structure

```
9-power-automate-notification-engine
├── README.md
├── flows
│   ├── 01-item-changed-alert.json       # Trigger: item modified
│   ├── 02-overdue-status-check.json     # Schedule: daily overdue scan
│   ├── 03-approval-pending-reminder.json # Schedule: chases approvals
│   ├── 04-expiry-warning.json           # Schedule: expiry warnings
│   └── shared-email-templates.md       # Reusable email content
└── docs
    └── how-to-reuse.md                  # Guide to adapt flows
```

---

## Flow Patterns

### Pattern 1: Item Changed Alert
**Trigger:** When SharePoint item is modified  
**Use case:** Notify stakeholders when a record's status changes

```
Trigger: SharePoint — Item Modified
  │
  ├── Condition: Status field changed?
  │     (Compare old vs new value)
  │
  ├── TRUE → Send email to assigned person
  │          Log change to Audit list
  │
  └── FALSE → Do nothing (terminate)
```

### Pattern 2: Daily Overdue Scan
**Trigger:** Scheduled — every weekday at 08:00  
**Use case:** Catch items past their due date with no completion

```
Trigger: Recurrence — Daily 08:00
  │
  ├── Get Items: Filter DueDate < Today AND Status != 'Complete'
  │
  ├── Apply to Each item:
  │     ├── Calculate days overdue
  │     ├── Send email to item owner
  │     └── Update 'OverdueFlag' column to Yes
  │
  └── Send summary to manager if count > 0
```

### Pattern 3: Approval Pending Reminder
**Trigger:** Scheduled — every Monday and Thursday  
**Use case:** Chase approvers who haven't responded

```
Trigger: Recurrence — Mon/Thu 09:00
  │
  ├── Get Items: Status = 'Pending Approval'
  │              AND SubmittedDate < (Today - 3 days)
  │
  └── Apply to Each:
        ├── Send reminder to Approver
        └── CC: Submitter and manager if > 7 days
```

### Pattern 4: Expiry Warning
**Trigger:** Scheduled — weekly  
**Use case:** Warn owners before documents/contracts expire

```
Trigger: Recurrence — Weekly Monday
  │
  ├── Get Items: ExpiryDate within next 30 days
  │              AND Status != 'Archived'
  │
  └── Apply to Each:
        ├── Calculate days until expiry
        ├── Choose template: 30 / 14 / 7 days
        └── Send warning email with renewal link
```

---

## Reusable Email Templates

### Overdue Item Alert
```
Subject: ⚠️ Action Required – [Item Title] is Overdue

[Item Title] assigned to you is now [X] days overdue.

Due Date:     [DueDate]
Current Status: [Status]
Item Link:    [URL]

Please update the status or contact your manager.
```

### Approval Reminder
```
Subject: 🔔 Reminder – Approval Pending: [Item Title]

This is a reminder that the following item is awaiting your approval:

Item:         [Title]
Submitted By: [Submitter]
Submitted:    [Date] ([X] days ago)

[Approve] [Reject]
```

---

## How to Reuse These Flows

Each flow is designed to work with **any SharePoint list** by changing three variables:

| Variable | Description |
|----------|-------------|
| `SiteAddress` | Your SharePoint site URL |
| `ListName` | The list or library name |
| `NotifyColumn` | Column holding the email address |

See `docs/how-to-reuse.md` for full instructions.

---

## Skills Demonstrated
- Power Automate scheduled and event-driven flows
- SharePoint list filtering with OData queries
- Conditional logic and Apply to Each loops
- Dynamic email content with expressions
- Reusable flow design patterns
- Business process automation
