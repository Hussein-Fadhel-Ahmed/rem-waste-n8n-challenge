# 🚀 REM Waste – Lead Scoring & Notification Bot (n8n Workflow)

## 📌 Overview

This workflow qualifies incoming leads from a form, evaluates them using budget and interest level, stores the result in Google Sheets, and sends Slack alerts for high-value leads. Built fully with **free-tier tools**, the workflow simulates a real-time lead management pipeline and follows the assessment brief from REM Waste.

> 🌐 **Webhook Endpoint**: [Submit via webhook](https://husseinfadhelahmed.app.n8n.cloud/webhook/lead-webhook) &#x20;
> 🧾 **Form (Tally.so)**: [Lead Submission Form](https://tally.so/r/wdoMEo) &#x20;
> 📊 **Google Sheet**: [View Submitted Leads](https://docs.google.com/spreadsheets/d/1-HI1DydzqmIJVL6kUxY2hJI_WqaFjYLqatjpqGYGcBE/edit?usp=sharing)

> 📸 *Tally Form Preview:* ![Tally Form](./assets/tally_form_preview.png)
> 📸 *Google Sheet Snapshot:* ![Google Sheet View](./assets/google_sheet_preview.png)

---

## 🧩 Use Case Overview

> 🖼️ *Use Case Diagram:* ![Use Case Diagram](./assets/use_case_diagram.png)

The diagram illustrates how the sales team interacts with the automation process:

* They receive Slack alerts based on lead scoring.
* The workflow itself handles validation, scoring, storage, and follow-up autonomously.

---

## ✅ Assessment Requirements (Checklist)

| Task                                       | Implemented |
| ------------------------------------------ | ----------- |
| Webhook trigger for lead input             | ✅ Yes       |
| Validation of required fields              | ✅ Yes       |
| Scoring logic for lead qualification       | ✅ Yes       |
| Store lead + score in Google Sheets        | ✅ Yes       |
| Slack notification for hot leads           | ✅ Yes       |
| Tally.so simulation form                   | ✅ Yes       |
| Delay + Follow-up notification             | ✅ Yes       |
| Documentation                              | ✅ Yes       |
| Extra features (Gemini AI, welcome emails) | ✅ Yes       |

---

## 🧠 Lead Scoring Logic

### 🔎 Logic Summary:

The lead score is determined by the combination of `budget` and `interest_level`.

| Budget (£) | Interest Level | Score |
| ---------- | -------------- | ----- |
| > 5000     | High           | HOT   |
| 1000–5000  | High or Medium | WARM  |
| Else       | Any            | COLD  |

### 💻 JavaScript Code Snippet

```javascript
let score = "Cold";
if (budget > 5000 && interestLevel === "High") {
  score = "Hot";
} else if (
  (budget >= 1000 && budget <= 5000 && (interestLevel === "High" || interestLevel === "Medium")) ||
  (budget > 5000 && interestLevel === "Medium")
) {
  score = "Warm";
}
```

This logic is implemented in an `n8n Code` node, after dynamically extracting field values and mapping Tally.so option IDs to their labels.

---

## 🔄 Workflow Description (n8n)

> 📸 *Screenshot of Workflow:* ![n8n Workflow Overview](./assets/n8n_workflow_screenshot.png)

This image should show all connected nodes including webhook trigger, validation, scoring, Sheets, Slack, and email.

### 📥 Input Trigger

* Triggered by `POST` from Tally.so form or manual Postman request.
* Fields: full\_name, email, phone, company\_size, budget, interest\_level (ID).

### ✅ Validation & Scoring

* `If Node`: Checks if required fields exist.
* `Email Regex`: Validates email format.
* `Code Node`: Maps dropdown ID to text + calculates score.

### 📝 Data Storage

* `Google Sheets Append`: Saves all lead data + score.

> 📸 *Google Sheet Entry Example:* ![Google Sheets Entry](./assets/google_sheet_entry.png)

### 🚨 Slack Notifications

* `Slack Node`: Sends rich notification if HOT lead.
* Includes AI-based summary from Gemini.

> 📸 *Slack Notification Example:* ![Slack Notification](./assets/slack_hot_lead_message.png)

### ⏳ Bonus: Follow-up Reminder

* `Wait Node`: Delays 2 mins.
* `Slack Node`: Sends follow-up message.

### ✉️ Welcome Email

* `Gmail Node`: Sends personalized HTML welcome email.

> 📸 *Email Preview:* ![Welcome Email](./assets/welcome_email_preview.png)

---

## 🤖 Gemini AI Integration

| Use Case                  | Output                                                                                 |
| ------------------------- | -------------------------------------------------------------------------------------- |
| **1. Internal Summary**   | Slack message with: headline, why important, potential ROI, suggested actions.         |
| **2. Personalized Pitch** | Gemini generates warm email paragraphs tailored to company size, interest, and budget. |

**Benefits:**

* Transforms lead alerts from raw data into **actionable insights**.
* Saves sales team time and ensures high-value leads are approached with strategic clarity.

---

## 📬 Manual Testing with Postman

To simulate form submission manually:

* **POST URL**: `https://husseinfadhelahmed.app.n8n.cloud/webhook/lead-webhook`
* **Headers**: `Content-Type: application/json`
* **Body**:

```json
{
  "eventId": "20bba5a5-c89a-410e-bf35-235a0233125d",
  "eventType": "FORM_RESPONSE",
  "createdAt": "2025-07-04T04:56:38.462Z",
  "data": {
    "responseId": "Ek1q8oA",
    "submissionId": "Ek1q8oA",
    "respondentId": "GG86WZ",
    "formId": "wdoMEo",
    "formName": "REM Waste – Lead Qualification Form",
    "createdAt": "2025-07-04T04:56:38.000Z",
    "fields": [
      { "label": "Full Name", "value": "Hussein Fadhel" },
      { "label": "Phone Number", "value": "+9647830117210" },
      { "label": "E-mail Address", "value": "hussein.alali80163@gmail.com" },
      { "label": "Company size", "value": "20" },
      {
        "label": "Interest Level",
        "value": ["631abc3c-4a0f-4c5d-9307-68d0a55fc01d"],
        "options": [
          { "id": "bce1b282-46bf-4376-b546-e10738f7c18f", "text": "High" },
          { "id": "631abc3c-4a0f-4c5d-9307-68d0a55fc01d", "text": "Medium" },
          { "id": "3c7b7bc6-2e9f-4f14-9b66-ea3fdae2c287", "text": "Low" }
        ]
      },
      { "label": "Estimated Budget (in £)", "value": 52222 }
    ]
  }
}
```

---

## 🔍 Assumptions

* Tally.so form returns values as arrays of fields.
* No CRM integration was required or implemented.
* Google Sheets is used for easy access and team visibility.
* Slack is assumed available on free tier.

---

## ⚠️ Limitations & Future Improvements

| Limitation            | Suggestion                                     |
| --------------------- | ---------------------------------------------- |
| No CRM sync           | Integrate HubSpot or Zoho                      |
| Slack-only alerting   | Add email/SMS fallback using Twilio or Mailjet |
| No duplicate checking | Add deduplication via email check              |

---

## 🔚 Conclusion

This workflow reflects:

* **Solid automation logic**
* **Clear validation and branching**
* **Practical scoring model**
* **AI-powered enhancement**
* **Well-structured notification system**

All within the expected **3–4 hour limit** using **free-tier tools** only.

---

Good luck, Hussein!

> Assessment: [Submit via form](https://forms.gle/Qwp8JgZ7xqyzuCKLA) &#x20;
> Contact: [hr@remwaste.com](mailto:hr@remwaste.com) | [www.remwaste.com](https://www.remwaste.com)
