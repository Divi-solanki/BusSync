# BusSync — AI-Powered Campus Transport Management System

> A dynamic bus route management system that enables real-time updates to college bus numbers, routes, and stops — eliminating manual dependency on drivers and supporting **SDG 4**, **SDG 9**, and **SDG 11** through smarter, accessible campus transport.

**IBM SkillsBuild AI Strategy & Business Intelligence Internship | AICTE | CSRBOX**  
**Submitted by:** Divyanshi Solanki | `divisolanki54321@gmail.com`
[Download PPT](https://docs.google.com/presentation/d/1x8JOc7gqusaZaD_AJvtIcVhYU78h62xf/edit?usp=sharing&ouid=112249638618746460537&rtpof=true&sd=true)
---

## 📌 Table of Contents

- [Problem Statement](#-problem-statement)
- [Solution](#-solution)
- [AI Agent Architecture](#-ai-agent-architecture)
- [SDG Alignment](#-sdg-alignment)
- [Tech Stack](#-tech-stack)
- [Repository Structure](#-repository-structure)
- [Setup Instructions](#-setup-instructions)
- [n8n Flow Configuration](#-n8n-flow-configuration)
- [AI Prompts Used](#-ai-prompts-used)
- [Business Model](#-business-model)
- [Project Documents](#-project-documents)

---

## ❗ Problem Statement

Every morning, college bus routes change — merges, stop modifications, driver unavailability — yet students and staff have **no reliable way to access this updated information**. They depend on asking drivers or conductors directly, leading to:

- Students waiting at wrong stops
- Missed buses and disrupted academic schedules
- No mechanism for sudden breakdown alerts
- Heavy communication load on the transport in-charge

---

## ✅ Solution

**BusSync** is a two-part system:

1. **Admin Panel** — Transport in-charge updates bus numbers, routes, and stops each morning via Google Sheets
2. **AI Agent** — Automatically detects changes and notifies students via WhatsApp, and answers student queries via a chatbot



---

## 🤖 AI Agent Architecture

BusSync uses **two automated flows** built on [n8n](https://n8n.io):

### Flow 1 — Scheduled Route Change Alerts
```
Schedule Trigger (every morning)
    → Fetch Route Data (Google Sheets)
        → AI Agent: Check for Route Changes (OpenAI)
            → Parse AI Response
                → IF changes detected:
                    → Format Alert Message
                        → Send WhatsApp Alert (Twilio)
```

### Flow 2 — Student Query Chatbot
```
WhatsApp Message Received (Webhook)
    → Extract Message Data
        → AI Agent: Bus Info Chatbot (OpenAI + Chat Memory)
            → Route Data Tool (Google Sheets)
                → Format Reply
                    → Send WhatsApp Reply (Twilio)
```

---

## 🌍 SDG Alignment

| SDG | Goal | How BusSync Contributes |
|-----|------|------------------------|
| **SDG 4** | Quality Education | Ensures uninterrupted access to campus for learners by eliminating transport confusion |
| **SDG 9** | Industry, Innovation & Infrastructure | Digitizes and automates institutional transport management using AI |
| **SDG 11** | Sustainable Cities & Communities | Enables reliable, real-time, accessible transport for all campus commuters |

---

## 🛠 Tech Stack

| Tool | Purpose | Cost |
|------|---------|------|
| [n8n](https://n8n.io) | AI agent builder & workflow automation | Free (cloud) |
| [Google Sheets](https://sheets.google.com) | Route database (updated daily by admin) | Free |
| [OpenAI API](https://platform.openai.com) | LLM powering the AI agent and chatbot | Free credits |
| [Twilio](https://twilio.com) | WhatsApp message sending & receiving | Free trial |
| IBM Watsonx | AI strategy layer (internship framework) | Free (SkillsBuild) |

---

## 📁 Repository Structure

```
BusSync/
├──docs
│   ├──BusSync_Concept_Note.docx       # Full project concept note
│   ├── BusSync_Presentation.pptx       # IBM SkillsBuild submission PPT
│   └── BusSync_Lean_Canvas.pdf         # Lean Canvas
│
├── n8n/
│   ├── flow1_route_alerts.json         # n8n export: scheduled alert agent
│   └── flow2_student_chatbot.json      # n8n export: student query chatbot
│
├── sheets/
│   └── route_data_template.csv         # Google Sheets template for route data
│
├── prompts/
│   └── ai_prompts.md                   # All AI agent system prompts
│
└── README.md
```

---

## ⚙️ Setup Instructions

### Prerequisites
- [n8n account](https://app.n8n.cloud/register) (free cloud)
- [Twilio account](https://www.twilio.com/try-twilio) (free trial)
- [OpenAI account](https://platform.openai.com) (free credits)
- Google account (for Sheets)

---

### Step 1 — Set Up Google Sheets (Route Database)

1. Create a new Google Sheet named `BusSync Routes`
2. Add the following columns in Row 1:

| Bus No | Route Name | Stops | Departure Time | Status | Last Updated |
|--------|-----------|-------|----------------|--------|--------------|
| 101 | College - City Center | Stop A, Stop B, Stop C | 8:00 AM | Active | 2025-12-08 |

3. Fill in your college's bus data
4. Note the **Sheet ID** from the URL: `https://docs.google.com/spreadsheets/d/YOUR_SHEET_ID/`

---

### Step 2 — Set Up Twilio WhatsApp Sandbox

1. Go to [Twilio Console](https://console.twilio.com)
2. Navigate to **Messaging → Try it out → Send a WhatsApp message**
3. Follow sandbox activation instructions (send a join code from your WhatsApp)
4. Note your:
   - **Account SID**
   - **Auth Token**
   - **Twilio WhatsApp number** (format: `whatsapp:+14155238886`)

---

### Step 3 — Set Up n8n

1. Sign up at [n8n.io](https://app.n8n.cloud/register)
2. Go to **Credentials** and add:
   - `Google Sheets OAuth2` — connect your Google account
   - `OpenAI API` — paste your OpenAI API key
   - `Twilio API` — paste your Account SID and Auth Token
3. Import flows:
   - Click **Import from file**
   - Upload `n8n/flow1_route_alerts.json`
   - Upload `n8n/flow2_student_chatbot.json`
4. In each flow, update the Google Sheets node with your **Sheet ID**
5. Update the Twilio nodes with your **WhatsApp number**

---

### Step 4 — Activate Flows

1. Open **Flow 1** → click **Activate** toggle (top right)
   - It will now check for route changes every morning automatically
2. Open **Flow 2** → copy the **Webhook URL** from the first node
   - Go to Twilio Console → WhatsApp Sandbox settings
   - Paste the webhook URL in **"When a message comes in"**
3. Test by sending a WhatsApp message like:
   > *"What is the route for bus 101?"*

---

## 🧠 AI Prompts Used

See [`prompts/ai_prompts.md`](prompts/ai_prompts.md) for all prompts. Quick reference:

**Chatbot System Prompt (Flow 2):**
```
You are BusSync, an intelligent campus transport assistant.
You have access to today's bus route data provided as context.
Answer student queries about bus numbers, routes, stops, and timings.
Never make up bus information — only use the data provided.
Reply in simple English, 2-3 sentences max.
```

**Change Detection Prompt (Flow 1):**
```
Compare yesterday's and today's route data.
Identify changed bus numbers, merged routes, added/removed stops, or cancellations.
Return as bullet points. If no changes, return exactly: NO_CHANGES
```

---

## 💼 Business Model

| Component | Details |
|-----------|---------|
| **Primary Revenue** | College-funded internal system |
| **Secondary Revenue** | Premium analytics dashboard for admin |
| **Long-term** | SaaS licensing to other AICTE-affiliated colleges |
| **Cost Structure** | Server/hosting, SMS gateway, maintenance |

---

## 📄 Project Documents

| Document | Description |
|----------|-------------|
| [`Concept Note`](docs/BusSync_Concept_Note.docx) | Full project concept note for IBM SkillsBuild submission |
| [`Presentation`](docs/BusSync_Presentation.pptx) | 13-slide PPT covering problem, AI agent, BMC, and SDGs |
| [`Lean Canvas`](docs/BusSync_Lean_Canvas.pdf) | Lean Canvas mapping problems, solutions, metrics, and channels |

---

## 👩‍💻 Author

**Divyanshi Solanki**  
Enrollment ID: `0827CS231087`  
IBM SkillsBuild AI Strategy & Business Intelligence Internship  
AICTE | CSRBOX | 2025

---

*Built as part of the IBM SkillsBuild AI Strategy & Business Intelligence Internship Program.*
