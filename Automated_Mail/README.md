# Bulk Job Emailer

A lightweight Python script to send personalized job-application emails with an attached resume to a curated list of (remote-friendly) startups. Built using `smtplib` and `python-dotenv` for configuration.

> **Warning & Ethics**: This tool sends real emails. Use it responsibly — do not spam. Always respect recipients' opt-out requests and the terms of service of your email provider. For large-scale outreach use a proper transactional email provider (SendGrid, Mailgun, Amazon SES) and follow anti-spam laws (CAN-SPAM, GDPR where applicable).

---

## Features

- Read SMTP credentials from a `.env` file.
- Sends a personalized plain-text email with an attached PDF resume.
- Small delay between messages to reduce throttling risk.
- Simple template substitution to personalize company name.

---

## Repository structure (recommended)

```
bulk-job-emailer/
├── README.md
├── send_emails.py            # main script (the file you provided)
├── requirements.txt         # deps (see below)
├── .env.example             # example .env file
└── resume/                  # store your resume here (or use absolute path)
    └── Harsh_Resume.pdf
```

---

## Prerequisites

- Python 3.8+
- A Gmail account (or any SMTP-enabled email account)
- If using Gmail with 2FA enabled, generate an **App Password** and use that (recommended). Normal account password may not work because Google blocks less secure apps.

---

## Installation

Create a virtual environment and install dependencies:

```bash
python -m venv venv
source venv/bin/activate      # macOS / Linux
venv\Scripts\activate       # Windows
pip install -r requirements.txt
```

**requirements.txt** (example):

```
python-dotenv>=0.19.0
```

---

## Configuration (.env)

Create a `.env` file (never commit this to GitHub!). You can copy the provided `.env.example` and fill in values.

```
MAIL_USERNAME=your.email@gmail.com
MAIL_PASSWORD=your_app_password_here
MAIL_SERVER=smtp.gmail.com
MAIL_PORT=587
MAIL_USE_TLS=True
MAIL_USE_SSL=False
```

**Important note about MAIL_USE_SSL**: In the shipped script `MAIL_USE_SSL` is computed with `os.getenv("MAIL_USE_SSL") == "False"` which is likely a bug. You probably want:

```py
MAIL_USE_SSL = os.getenv("MAIL_USE_SSL") == "True"
```

---

## Usage

1. Put your resume file on disk and update `resume_path` in the script or pass it as an argument (recommended edit).
2. Update `email_company_pairs` list in the script or load contacts from a CSV for easier editing.
3. Customize `subject` and `body_template` as you prefer. If you plan to send HTML emails, change `MIMEText(body, 'plain')` to `MIMEText(html_body, 'html')` and test thoroughly.

Run the script:

```bash
python send_emails.py
```

You will be prompted for email and password if they are not set in `.env`.

---

## Example: `.env.example`

```
MAIL_USERNAME=harsh.shinde.job@gmail.com
MAIL_PASSWORD=app_password_here
MAIL_SERVER=smtp.gmail.com
MAIL_PORT=587
MAIL_USE_TLS=True
MAIL_USE_SSL=False
```

---

## Customization ideas / improvements

- **CSV input**: Load recipients from a CSV (`email,company_name,first_name`) to scale.
- **Rate limiting**: Increase delay or randomize the sleep time to avoid detection (e.g., `time.sleep(random.uniform(5,12))`).
- **Retry & logging**: Store failures to a log file and retry failed sends.
- **Use OAuth2 or transactional SMTP**: For better reliability and to avoid Gmail being flagged.
- **HTML templates**: Use `email.mime.text.MIMEText(..., 'html')` for richer formatting and `jinja2` for templating.
- **Throttling / batching**: Send a small number per hour/day to avoid provider limits.
- **Unsubscribe handling**: Track opt-outs by replying `opt-out` in the template and removing addresses from future runs.

---

## Troubleshooting

- `smtplib.SMTPAuthenticationError`: Most commonly due to wrong password, app password not used with 2FA, or Gmail blocking sign-in. Check Google account security settings.
- `smtplib.SMTPRecipientsRefused`: Invalid email address or domain blocking attachments.
- Connection / TLS errors: Verify `MAIL_PORT`, `MAIL_USE_TLS`, `MAIL_USE_SSL`, and server name.

---

## Security & best practices

- **Never commit `.env`** to version control. Use `.gitignore` to exclude it.
- Use **App Passwords** or OAuth2 for SMTP authentication.
- Respect recipient privacy and legal restrictions. Avoid sending unsolicited commercial email at scale.
- Use a dedicated email account for outreach rather than your personal mailbox.

---

## License & Author

MIT License — feel free to reuse and modify.

Author: **Harsh Shinde** ([harsh.shinde.job@gmail.com](mailto:harsh.shinde.job@gmail.com))

---



