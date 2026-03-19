# 📄 Docs Automation — n8n Workflow

Automated document generation pipeline triggered by Google Sheets. When a new row with status `ready` appears, the workflow creates a personalized folder, fills in contract templates, converts them to PDF, and sends them via email — all without any manual work.

---

## 🔄 How It Works

```
Google Sheets (trigger)
    └── If status == "ready"
            └── Create folder in Google Drive (named after client)
                    ├── Copy template_contract → fill with client data → download as PDF
                    └── Copy template_contract_2 → fill with client data → download as PDF
                            └── Merge both PDFs
                                    ├── Send via Gmail (with attachments)
                                    ├── Upload PDF 1 to Drive folder
                                    └── Upload PDF 2 to Drive folder
```

---

## ⚙️ Nodes Used

| Node | Purpose |
|---|---|
| Google Sheets Trigger | Polls sheet every minute for new rows |
| If | Filters rows where `status == "ready"` |
| Google Drive — Create Folder | Creates a folder named after the client |
| Google Drive — Copy File (x2) | Copies contract templates into the new folder |
| Google Docs — Update Document (x2) | Replaces placeholders with client data |
| Google Drive — Download File (x2) | Exports filled docs as PDF |
| Merge | Combines both PDF outputs into one item |
| Gmail — Send Message | Sends email with both PDFs attached |
| Google Drive — Upload File (x2) | Saves final PDFs back to the client folder |

---

## 📋 Template Placeholders

The workflow replaces the following placeholders in both document templates:

| Placeholder | Source field |
|---|---|
| `{{full_name}}` | `full_name` column in Google Sheets |
| `{{company}}` | `company` column |
| `{{phone}}` | `phone` column (contract 1 only) |
| `{{email}}` | `email` column |

---

## 🚀 Setup

### 1. Google Sheets
Create a spreadsheet with the following columns:
```
full_name | company | phone | email | status
```
Set `status` to `ready` when a row should be processed.

### 2. Google Docs Templates
Create two document templates in Google Drive using the placeholders above (`{{full_name}}`, `{{company}}`, etc.).

### 3. Import Workflow
- Download `docs_automation.json`
- In n8n go to **Workflows → Import from file**
- Connect your credentials:
  - Google Sheets Trigger OAuth
  - Google Docs OAuth
  - Google Drive OAuth
  - Gmail OAuth

### 4. Update IDs
Replace the following in the workflow nodes:
- `YOUR_SPREADSHEET_ID` — your Google Sheet ID
- `YOUR_FILE_ID` — your template document IDs
- `YOUR_EMAIL@gmail.com` — recipient email address

---

## 🛠️ Built With

- [n8n](https://n8n.io/) — workflow automation
- Google Sheets, Google Docs, Google Drive, Gmail

---

## 📁 Files

| File | Description |
|---|---|
| `docs_automation.json` | n8n workflow (credentials removed) |
| `README.md` | This file |
