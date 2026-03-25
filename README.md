# 🚀 Zapier Automated Hiring Pipeline V2 | TechNova Startups
### Built with Zapier · Google Forms · Gmail · Slack · Google Sheets

![Status](https://img.shields.io/badge/Status-Live%20%E2%9C%85-brightgreen?style=for-the-badge)
![Zapier](https://img.shields.io/badge/Zapier-FF4A00?style=for-the-badge&logo=zapier&logoColor=white)
![Google Forms](https://img.shields.io/badge/Google%20Forms-7A1FA2?style=for-the-badge&logo=googleforms&logoColor=white)
![Gmail](https://img.shields.io/badge/Gmail-EA4335?style=for-the-badge&logo=gmail&logoColor=white)
![Slack](https://img.shields.io/badge/Slack-4A154B?style=for-the-badge&logo=slack&logoColor=white)
![Google Sheets](https://img.shields.io/badge/Google%20Sheets-34A853?style=for-the-badge&logo=googlesheets&logoColor=white)

---

## 📌 Project Overview

A fully automated, intelligent hiring pipeline built on **Zapier** for **TechNova Startups** — screening, routing, and notifying the right people about the right candidates, automatically.

When a candidate submits an application, the system:
- ✅ **Extracts and validates** their skills, experience and location
- ✅ **Filters out unqualified** applicants at a zero-waste gate
- ✅ **Delays 30 minutes** to give HR a review buffer
- ✅ **Emails the Sales Manager** with a branded candidate card
- ✅ **Queues a digest entry** for the 6 PM daily batch summary
- ✅ **Routes by experience** — Senior → Slack alert · Junior → Google Sheets
- ✅ **Posts a daily summary** to `#hiring-daily-summary` at 6 PM automatically

> *"Zero manual screening hours. Every qualified applicant auto-notified in 30 seconds. 100% automated routing — zero missed candidates."*

---

## 🆕 What's New in V2

| | V1 | V2 |
|---|---|---|
| Steps | 5 steps | 12 steps ✅ |
| Delay before action | ❌ No | ✅ 30-min buffer |
| Email to Sales Manager | ❌ No | ✅ Branded HTML email |
| Daily digest summary | ❌ No | ✅ 6 PM Slack batch |
| Paths routing | ✅ Yes | ✅ Yes (now Step 8) |

**3 new steps added in V2:** Step 5 (Delay) → Step 6 (Email Sales Manager) → Step 7 (Append to Digest)

---

## 🏗️ Full System Architecture — V2 (12 Steps)

```
Candidate submits Google Form
            │
            ▼
  Step 1 — Google Forms Trigger (New Form Response)
            │
  Step 2 — Formatter: Split Full Name → First + Last
            │
  Step 3 — Formatter: Extract Experience Number from text
            │
  Step 4 — Filter Gate ← STOP if unqualified
            │  ✅ Python in skills
            │  ✅ Experience > 0 years
            │  ✅ Location supported
            │
  Step 5 — Delay: 30 Minutes ⏱️ (NEW in V2)
            │
  Step 6 — Gmail: Email Sales Manager (NEW in V2)
            │
  Step 7 — Digest: Append Entry to Daily Queue (NEW in V2)
            │
  Step 8 — Paths: Route by Experience Level
            │
            ├──► Senior Track (4+ years)
            │         └── Slack → #tech-interviews
            │
            └──► Junior Track (1–3 years)
                      └── Google Sheets → Junior Applicants tab

  ─────────────────────────────────────────────
  SEPARATE ZAP — Daily 6 PM Digest
  Schedule Trigger → Release Digest → Slack #hiring-daily-summary
```

---

## 🔧 Step-by-Step Breakdown

### Steps 1–3 — Form Intake & Data Formatting

**Step 1 — Google Forms Trigger**
Fires instantly on every new application submission. Collects: Full Name, Email, Years of Experience, Key Skills, Location, LinkedIn URL.

**Step 2 — Formatter: Split Name**
`Formatter → Text → Split Text`
Splits Full Name into First Name + Last Name for personalised email greetings and Slack messages.

**Step 3 — Formatter: Extract Experience**
`Formatter → Text → Extract Number`
Pulls the numeric value from free-text experience answers (e.g. "5 years" → `5`) for use in the Filter and Paths conditions.

---

### Step 4 — 🚫 Filter Gate (Zero-Waste Screener)

Only candidates who pass **all 3 conditions** proceed. Everyone else is stopped silently — no email, no Slack, no digest entry.

| Condition | Rule |
|---|---|
| **Python** | Key Skills must CONTAIN `Python` |
| **Experience** | Extracted number must be GREATER THAN `0` |
| **Location** | Location does NOT CONTAIN `Other (not supported)` |

> *Protects the Sales Manager from low-quality alerts and keeps task history clean.*

---

### Step 5 — ⏱️ Delay 30 Minutes *(NEW in V2)*

`Delay by Zapier → Delay For → 30 Minutes`

Pauses the Zap for exactly 30 minutes after the filter passes. Gives HR a short review window and spaces out notifications to prevent alert fatigue. Only qualified applicants reach this step.

---

### Step 6 — 📧 Email Sales Manager *(NEW in V2)*

`Gmail by Zapier → Send Outbound Email`

Sends a branded HTML candidate card email automatically after the 30-minute delay.

| Field | Value |
|---|---|
| **To** | salesmanager@technova.com |
| **Subject** | `New Qualified Applicant: {{Full Name}} – {{Experience}} years` |
| **Body** | Branded HTML template with candidate profile + filter pass confirmation |

---

### Step 7 — 📋 Append to Daily Digest *(NEW in V2)*

`Digest by Zapier → Append Entry and Schedule Digest`

Silently queues one summary line per qualified applicant throughout the day. The digest holds all entries until released at 6 PM.

**Entry format:**
```
- {{First Name}} {{Last Name}} ({{Email}}) – {{Exp}} yrs – Skills: {{Skills}} – Location: {{Location}}
```

**Digest name:** `Daily Qualified Job Apps`

---

### Step 8 — 🛣️ Paths: Route by Experience

**PATH A — Senior Track (4+ years)**

| Field | Value |
|---|---|
| Condition | Extracted experience > 3 |
| Action | Slack → `#tech-interviews` |
| Sent by | TechNova Hiring Bot |
| Message includes | Name · Email · Experience · Skills · Location · LinkedIn |
| Result | Tech lead sees notification immediately |

**PATH B — Junior Track (1–3 years)**

| Field | Value |
|---|---|
| Condition | Extracted experience < 4 AND > 0 |
| Action | Google Sheets → Junior Applicants tab |
| Columns | Name · Email · Experience · Skills · Location |
| Status | Auto-set to `Pending Review` |
| Result | HR batch reviews sheet daily |

---

## ⏰ Separate Zap — Daily 6 PM Digest

A completely independent Zap fires every evening at 6:00 PM:

| Step | Action |
|---|---|
| 1 | Schedule by Zapier — triggers every day at 6:00 PM |
| 2 | Digest: Release — pulls all queued entries from `Daily Qualified Job Apps` |
| 3 | Slack: Send Message → `#hiring-daily-summary` |

**Slack message includes:**
- 📊 Header: `Daily Qualified Job Applications Summary — {{date}}`
- 👤 One formatted line per qualified applicant
- 🔢 Total count: `X applicants passed screening today`
- 📋 Track summary: Senior alerted · Junior logged to Sheets
- 📣 `@Salesmanager` tagged for immediate visibility

---

## ✅ Live Test Results — All 6 V2 Outcomes Confirmed

| Outcome | Result |
|---|---|
| ✅ Filter gate stopped unqualified applicant | Confirmed |
| ✅ 30-minute delay ran correctly | Confirmed |
| ✅ HTML email delivered to Sales Manager | Confirmed |
| ✅ Slack `#tech-interviews` notified (Senior) | Confirmed |
| ✅ Digest entry appended in 1 second | Confirmed |
| ✅ 6 PM daily summary posted to `#hiring-daily-summary` | Confirmed |

---

## 💼 Business Impact & ROI

| Metric | Before V2 | After V2 |
|---|---|---|
| Manual screening hours | Several per day | **0** |
| Time to notify Sales Manager | Manual email per application | **30 seconds** (post-delay) |
| Daily hiring visibility | Unknown until checked | **6 PM Slack digest** |
| Senior/Junior routing | Manual check required | **100% auto-routed** |
| Alert fatigue | High — immediate noise | **Eliminated** by 30-min delay |
| Missed candidates | Possible | **Zero** |

---

## 🛠️ Tech Stack

| Tool | Role |
|---|---|
| **Google Forms** | Application intake — Name, Email, Experience, Skills, Location, LinkedIn |
| **Zapier Formatter** | Splits name · Extracts experience number from free text |
| **Zapier Filter** | Zero-waste qualification gate |
| **Zapier Delay** | 30-minute buffer before email fires |
| **Gmail by Zapier** | Branded HTML candidate email to Sales Manager |
| **Zapier Digest** | Queues applicant lines all day for 6 PM batch release |
| **Zapier Paths** | Routes Senior → Slack · Junior → Google Sheets |
| **Slack** | TechNova Hiring Bot — `#tech-interviews` + `#hiring-daily-summary` |
| **Google Sheets** | Junior Applicants tab — HR batch review dashboard |

---

## 📁 Repository Contents

```
zapier-hiring-pipeline/
│
├── screenshots/
│   ├── full_zap_architecture.jpg
│   ├── slack_senior_notification.jpg
│   ├── slack_daily_digest.jpg
│   ├── gmail_sales_manager_email.jpg
│   ├── filter_gate.jpg
│   ├── delay_step.jpg
│   ├── digest_step.jpg
│   └── google_sheets_junior_tab.jpg
│
├── docs/
│   └── TechNova_Zapier_Pipeline_V2_Presentation.pdf
│
└── README.md
```

---

## 🚀 Possible V3 Upgrades

| Feature | Description |
|---|---|
| 🤖 AI Scoring | Add ChatGPT/Claude API step to auto-score candidates |
| 📅 Auto-Schedule Interviews | Calendly integration for instant interview booking |
| ❌ Rejection Emails | Auto-send polite rejection to unqualified candidates |
| 🔗 LinkedIn Auto-Sync | Pull job posting data directly from LinkedIn |
| 📊 ATS Integration | Connect to Greenhouse or Lever for full hiring pipeline |
| 📈 Weekly Leadership Report | Auto-generate weekly hiring summary for management |

---

## 👩🏾‍💻 About the Builder

**Gloria Njorteah** — Strategic Business Analyst & AI Automation Specialist

This pipeline was built and upgraded to V2 as part of a Zapier automation module — demonstrating advanced use of Filter gates, Delay steps, Digest batching, Paths routing and multi-tool integrations for a real HR use case.

- 🌍 [LinkedIn](https://www.linkedin.com/in/gloria-njorteah)
- 💼 [Upwork](https://www.upwork.com/freelancers/gloinnovate)
- 🐙 [GitHub](https://github.com/Glorious75)

> *Available to build hiring automation, HR pipelines and workflow systems for your business. Let's talk.*

---

*Built with ⚡ using Zapier | TechNova Startups · HR & Hiring Automation | March 2026*
