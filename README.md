# clinical-knowledge-capture
clinical-knowledge-capture
[README.md](https://github.com/user-attachments/files/25528372/README.md)
# Clinical Expert Knowledge Capture Tool

**Efficient tool for capturing clinical expertise to generate AI-ready synthetic healthcare datasets — no patient data required.**

[![GitHub Pages](https://img.shields.io/badge/Live-GitHub%20Pages-brightgreen)](https://yourusername.github.io/clinical-knowledge-capture/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

## The Problem

Healthcare AI research faces a data access paradox:
- **Real patient data** requires ethics approval, anonymisation, and months of governance processes
- **Clinical experts** have limited time and find spreadsheet-based data collection unnatural
- **Random synthetic data** lacks the clinical realism needed for meaningful AI experiments

## The Solution

This tool captures clinical expertise using a structured approach that aligns with how clinicians naturally reason — then exports that knowledge as structured XLSX data ready for synthetic dataset generation.

**Total clinician time: ~20-30 minutes** (compared to hours of spreadsheet population)

### How It Works

1. **Clinical Decision Map** — Four-branch structured knowledge capture (Symptoms → Assessment → Diagnosis → Treatment)
2. **Scenario Cards** — Describe typical patient archetypes with frequency proportions for realistic class distribution
3. **Conditional Rules** — IF-THEN clinical rules that ensure generated datasets have realistic relationships
4. **XLSX Export** — All captured knowledge exported as a structured multi-sheet spreadsheet
5. **Email Notification** — Automatic email to researcher with capture summary

## Quick Start

### Option 1: Use directly (GitHub Pages)

Simply visit: `https://yourusername.github.io/clinical-knowledge-capture/`

No installation, no login, no software required. Works on any device with a browser.

### Option 2: Deploy your own

```bash
# Clone the repository
git clone https://github.com/yourusername/clinical-knowledge-capture.git
cd clinical-knowledge-capture

# That's it! Open index.html in a browser, or deploy to GitHub Pages
```

#### Deploy to GitHub Pages

1. Push to GitHub
2. Go to **Settings → Pages**
3. Set Source to **Deploy from a branch**
4. Select **main** branch, **/ (root)** folder
5. Your tool is live at `https://yourusername.github.io/clinical-knowledge-capture/`

## Email Notification Setup (Optional)

The tool can send email notifications when a clinician submits their knowledge capture. This uses [EmailJS](https://www.emailjs.com/) (free tier: 200 emails/month).

### Step 1: Create EmailJS Account

1. Go to [emailjs.com](https://www.emailjs.com/) and sign up (free)
2. Note your **Public Key** from the dashboard

### Step 2: Add an Email Service

1. In EmailJS dashboard, click **Email Services → Add New Service**
2. Choose your email provider (Gmail, Outlook, etc.)
3. Follow the connection steps
4. Note your **Service ID** (e.g., `service_abc123`)

### Step 3: Create an Email Template

1. Click **Email Templates → Create New Template**
2. Use this template:

**Subject:** `Clinical Knowledge Capture: {{condition}} from {{expert_name}}`

**Body:**
```
New Clinical Knowledge Capture Submission
==========================================

Expert: {{expert_name}}
Specialty: {{specialty}}
Organisation: {{organisation}}
Condition: {{condition}}
Date: {{date}}

The clinician has submitted their expertise capture through the online tool.
The XLSX file should be downloaded from the tool directly.

--- SUMMARY ---
{{summary}}

---
This email was sent automatically by the Clinical Knowledge Capture Tool.
Please ask the clinician to forward the downloaded XLSX file if not received.
```

**To:** `{{to_email}}`
**CC:** `{{cc_email}}`

3. Save and note your **Template ID** (e.g., `template_xyz789`)

### Step 4: Configure in the Tool

1. Open the tool and go to **Step 5: Review & Submit**
2. Enter your EmailJS Public Key, Service ID, and Template ID
3. Click **Save Email Configuration**
4. These values are stored in the browser's localStorage

> **Note:** EmailJS free tier does not support file attachments. The email sends a text summary, and the XLSX is downloaded to the clinician's device. The tool also offers a "Open Email Client" button as a fallback for manual attachment.

### Alternative: Self-hosted Email (Advanced)

If you need direct XLSX attachment, you can add a small backend:

```python
# Example Flask endpoint (deploy on Render, Railway, etc.)
from flask import Flask, request, jsonify
from flask_cors import CORS
import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.base import MIMEBase
from email import encoders
import base64

app = Flask(__name__)
CORS(app)

@app.route('/api/submit', methods=['POST'])
def submit():
    data = request.json
    xlsx_b64 = data['xlsx_base64']
    
    msg = MIMEMultipart()
    msg['To'] = data['researcher_email']
    msg['Subject'] = f"Clinical Knowledge: {data['condition']}"
    
    attachment = MIMEBase('application', 'vnd.openxmlformats-officedocument.spreadsheetml.sheet')
    attachment.set_payload(base64.b64decode(xlsx_b64))
    encoders.encode_base64(attachment)
    attachment.add_header('Content-Disposition', f'attachment; filename="{data["filename"]}"')
    msg.attach(attachment)
    
    # Send via SMTP
    with smtplib.SMTP('smtp.gmail.com', 587) as server:
        server.starttls()
        server.login('your@gmail.com', 'app-password')
        server.send_message(msg)
    
    return jsonify({'status': 'sent'})
```

## Project Structure

```
clinical-knowledge-capture/
├── index.html          # Complete single-page application
├── README.md           # This file
├── LICENSE             # MIT License
└── docs/
    ├── METHODOLOGY.md  # Academic methodology documentation
    └── templates/      # Printable PDF templates for pen-and-paper use
```

## XLSX Output Structure

The exported spreadsheet contains 6 sheets:

| Sheet | Contents |
|-------|----------|
| **Expert Info** | Clinician details, specialty, condition focus |
| **Decision Map** | Four-branch knowledge structure (Symptoms, Assessment, Diagnosis, Treatment) |
| **Scenario Cards** | Patient archetype descriptions with frequency distributions |
| **Conditional Rules** | IF-THEN clinical decision rules |
| **Additional Parameters** | Extra measurable clinical parameters |
| **Dataset Columns** | Auto-generated dataset column specification for synthetic data generation |

## How to Use the Captured Data

Once you have the XLSX from a clinical expert, the workflow is:

1. **Extract parameters** from the Decision Map and Scenario Cards
2. **Define conditional rules** from the Rules sheet
3. **Generate synthetic data** using Python (NumPy/Faker) with the expert's distributions and rules
4. **Validate with the expert** — show them 30-50 sample records for face-validity checking
5. **Use for AI experiments** — the resulting dataset reflects real clinical expertise patterns

See the companion document **Clinical Expert Knowledge Capture Toolkit** (DOCX) for the full methodology.

## Academic Citation

If you use this tool in published research, please cite:

```bibtex
@misc{ramachandran2026knowledge,
  title={Clinical Expert Knowledge Capture Tool for AI-Ready Synthetic Dataset Generation},
  author={Ramachandran, Muthu},
  year={2026},
  howpublished={GitHub: \url{https://github.com/yourusername/clinical-knowledge-capture}},
  note={Forti5 Technologies}
}
```

## Key Terminology for Publications

- **Expert Knowledge Elicitation** — the process of structured capture from clinical experts
- **Knowledge-Driven Synthetic Data** — synthetic datasets generated from expert rules, not purely statistical methods
- **Clinician-in-the-Loop Validation** — expert review of generated outputs
- **Clinical Pathway Modelling** — encoding treatment decision flows as dataset generation rules

## Privacy & Ethics

- **No patient data** is collected, stored, or processed at any point
- **No server-side storage** — all data remains in the browser until exported
- **Expert consent** should be obtained before using captured knowledge
- **Synthetic data disclaimers** should be included in any published work

## Technology

- Pure HTML/CSS/JavaScript — no build tools, no frameworks, no dependencies to install
- [SheetJS](https://sheetjs.com/) for XLSX generation (loaded from CDN)
- [EmailJS](https://www.emailjs.com/) for email notifications (optional, loaded from CDN)
- Hosted on GitHub Pages (static files only, no server required)

## Contributing

Contributions welcome! Please open an issue or PR.

## License

MIT License — see [LICENSE](LICENSE) for details.

---

**Prof Muthu Ramachandran** | Forti5 Technologies | muthu.ramachandran@forti5.tech
