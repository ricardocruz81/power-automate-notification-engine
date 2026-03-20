# Shared Email Templates

Reusable HTML email templates for all notification flows. Copy the HTML body into the **Send an email** action in Power Automate. Replace `[PLACEHOLDER]` values with dynamic content expressions.

---

## Template 1 — Generic Item Alert

**Use for:** Status changes, assignments, updates

```html
<div style="font-family: Segoe UI, Arial, sans-serif; max-width: 600px;">
  <div style="background-color: #0078d4; padding: 16px 24px;">
    <h2 style="color: white; margin: 0;">Action Required</h2>
  </div>
  <div style="padding: 24px; background-color: #ffffff; border: 1px solid #e0e0e0;">
    <p>Hi @{triggerBody()?['AssignedTo']?['DisplayName']},</p>
    <p>An item assigned to you has been updated and may require your attention.</p>
    <table style="border-collapse: collapse; width: 100%; margin: 16px 0;">
      <tr style="background-color: #f5f5f5;">
        <td style="padding: 8px 16px; font-weight: bold; width: 140px;">Item</td>
        <td style="padding: 8px 16px;">@{triggerBody()?['Title']}</td>
      </tr>
      <tr>
        <td style="padding: 8px 16px; font-weight: bold;">Status</td>
        <td style="padding: 8px 16px;">@{triggerBody()?['Status']?['Value']}</td>
      </tr>
      <tr style="background-color: #f5f5f5;">
        <td style="padding: 8px 16px; font-weight: bold;">Due Date</td>
        <td style="padding: 8px 16px;">@{formatDateTime(triggerBody()?['DueDate'], 'dd MMMM yyyy')}</td>
      </tr>
      <tr>
        <td style="padding: 8px 16px; font-weight: bold;">Modified By</td>
        <td style="padding: 8px 16px;">@{triggerBody()?['Editor']?['DisplayName']}</td>
      </tr>
    </table>
    <p style="margin-top: 24px;">
      <a href="@{triggerBody()?['{Link}']}"
         style="background-color: #0078d4; color: white; padding: 10px 20px; text-decoration: none; border-radius: 4px;">
        View Item
      </a>
    </p>
  </div>
  <div style="padding: 12px 24px; background-color: #f5f5f5; font-size: 12px; color: #666;">
    This is an automated notification. Please do not reply to this email.
  </div>
</div>
```

---

## Template 2 — Overdue Alert

**Use for:** Items past their due date

```html
<div style="font-family: Segoe UI, Arial, sans-serif; max-width: 600px;">
  <div style="background-color: #c62828; padding: 16px 24px;">
    <h2 style="color: white; margin: 0;">⚠️ Overdue Item</h2>
  </div>
  <div style="padding: 24px; background-color: #ffffff; border: 1px solid #e0e0e0;">
    <p>Hi @{items('Apply_ToEach_OverdueItem')?['AssignedTo']?['DisplayName']},</p>
    <p>The following item is <strong>@{outputs('Calculate_DaysOverdue')} days overdue</strong> and requires immediate attention.</p>
    <table style="border-collapse: collapse; width: 100%; margin: 16px 0;">
      <tr style="background-color: #ffebee;">
        <td style="padding: 8px 16px; font-weight: bold; width: 140px;">Item</td>
        <td style="padding: 8px 16px;">@{items('Apply_ToEach_OverdueItem')?['Title']}</td>
      </tr>
      <tr>
        <td style="padding: 8px 16px; font-weight: bold;">Due Date</td>
        <td style="padding: 8px 16px; color: #c62828;">@{formatDateTime(items('Apply_ToEach_OverdueItem')?['DueDate'], 'dd MMMM yyyy')}</td>
      </tr>
      <tr style="background-color: #ffebee;">
        <td style="padding: 8px 16px; font-weight: bold;">Days Overdue</td>
        <td style="padding: 8px 16px; color: #c62828; font-weight: bold;">@{outputs('Calculate_DaysOverdue')} days</td>
      </tr>
      <tr>
        <td style="padding: 8px 16px; font-weight: bold;">Current Status</td>
        <td style="padding: 8px 16px;">@{items('Apply_ToEach_OverdueItem')?['Status']?['Value']}</td>
      </tr>
    </table>
    <p>Please update the status or contact your manager if you need assistance.</p>
    <p style="margin-top: 24px;">
      <a href="@{items('Apply_ToEach_OverdueItem')?['{Link}']}"
         style="background-color: #c62828; color: white; padding: 10px 20px; text-decoration: none; border-radius: 4px;">
        View &amp; Update Item
      </a>
    </p>
  </div>
  <div style="padding: 12px 24px; background-color: #f5f5f5; font-size: 12px; color: #666;">
    This is an automated notification. Please do not reply to this email.
  </div>
</div>
```

---

## Template 3 — Approval Request

**Use for:** Sending to approvers

```html
<div style="font-family: Segoe UI, Arial, sans-serif; max-width: 600px;">
  <div style="background-color: #f57c00; padding: 16px 24px;">
    <h2 style="color: white; margin: 0;">🔔 Approval Required</h2>
  </div>
  <div style="padding: 24px; background-color: #ffffff; border: 1px solid #e0e0e0;">
    <p>Hi @{triggerBody()?['ApproverName']?['DisplayName']},</p>
    <p>The following item is awaiting your approval.</p>
    <table style="border-collapse: collapse; width: 100%; margin: 16px 0;">
      <tr style="background-color: #fff8e1;">
        <td style="padding: 8px 16px; font-weight: bold; width: 140px;">Item</td>
        <td style="padding: 8px 16px;">@{triggerBody()?['Title']}</td>
      </tr>
      <tr>
        <td style="padding: 8px 16px; font-weight: bold;">Submitted By</td>
        <td style="padding: 8px 16px;">@{triggerBody()?['SubmittedBy']?['DisplayName']}</td>
      </tr>
      <tr style="background-color: #fff8e1;">
        <td style="padding: 8px 16px; font-weight: bold;">Submitted Date</td>
        <td style="padding: 8px 16px;">@{formatDateTime(triggerBody()?['SubmittedDate'], 'dd MMMM yyyy')}</td>
      </tr>
    </table>
    <p style="margin-top: 24px; display: flex; gap: 12px;">
      <a href="@{triggerBody()?['{Link}']}"
         style="background-color: #2e7d32; color: white; padding: 10px 20px; text-decoration: none; border-radius: 4px; margin-right: 12px;">
        ✅ View &amp; Approve
      </a>
    </p>
  </div>
  <div style="padding: 12px 24px; background-color: #f5f5f5; font-size: 12px; color: #666;">
    This is an automated notification. Please do not reply to this email.
  </div>
</div>
```

---

## Template 4 — Success / Completion

**Use for:** Confirming completion or approval to submitter

```html
<div style="font-family: Segoe UI, Arial, sans-serif; max-width: 600px;">
  <div style="background-color: #2e7d32; padding: 16px 24px;">
    <h2 style="color: white; margin: 0;">✅ Completed</h2>
  </div>
  <div style="padding: 24px; background-color: #ffffff; border: 1px solid #e0e0e0;">
    <p>Hi @{triggerBody()?['SubmittedBy']?['DisplayName']},</p>
    <p>Your submission has been <strong>approved</strong>.</p>
    <table style="border-collapse: collapse; width: 100%; margin: 16px 0;">
      <tr style="background-color: #e8f5e9;">
        <td style="padding: 8px 16px; font-weight: bold; width: 140px;">Item</td>
        <td style="padding: 8px 16px;">@{triggerBody()?['Title']}</td>
      </tr>
      <tr>
        <td style="padding: 8px 16px; font-weight: bold;">Approved By</td>
        <td style="padding: 8px 16px;">@{triggerBody()?['ApproverName']?['DisplayName']}</td>
      </tr>
      <tr style="background-color: #e8f5e9;">
        <td style="padding: 8px 16px; font-weight: bold;">Date</td>
        <td style="padding: 8px 16px;">@{formatDateTime(utcNow(), 'dd MMMM yyyy')}</td>
      </tr>
    </table>
    <p style="margin-top: 24px;">
      <a href="@{triggerBody()?['{Link}']}"
         style="background-color: #2e7d32; color: white; padding: 10px 20px; text-decoration: none; border-radius: 4px;">
        View Item
      </a>
    </p>
  </div>
  <div style="padding: 12px 24px; background-color: #f5f5f5; font-size: 12px; color: #666;">
    This is an automated notification. Please do not reply to this email.
  </div>
</div>
```
