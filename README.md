# AI-Powered Lead Nurturing & CRM Sync Platform

![n8n](https://img.shields.io/badge/n8n-Workflow%20Automation-FF6D5A?style=for-the-badge&logo=n8n&logoColor=white)
![Supabase](https://img.shields.io/badge/Supabase-PostgreSQL-3ECF8E?style=for-the-badge&logo=supabase&logoColor=white)
![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4o-412991?style=for-the-badge&logo=openai&logoColor=white)
![ClickUp](https://img.shields.io/badge/ClickUp-CRM-7B68EE?style=for-the-badge&logo=clickup&logoColor=white)

This repository contains the backend database schemas, automated workflows, and integration pipelines for the **AI-Powered Real Estate Lead Nurturing & CRM Sync Platform**. 

The system automates lead capturing, multi-channel messaging, booking coordination, deposit collection, and CRM synchronization.

---

## 📂 Repository Structure

```
real-estate-automation/
├── scrapers/
│   ├── spareroom_scraper.py         # Selenium browser automation for portal extraction
│   └── whatsapp_scraper.py          # Playwright browser script for chat logs
├── workflows/
│   ├── 01_spareroom_ai_bot.json     # Portal inbound parsing and AI replying
│   ├── 02_outbound_whatsapp.json    # SMS template converter and lead initiator
│   ├── 03_woztell_ai_brain.json     # Inbound WhatsApp intent routing bot
│   ├── 04_viewing_reminders.json    # Calendly schedule cron and warnings dispatch
│   └── 05_clickup_sync_engine.json  # CRM updater with central retry queue
├── schema/
│   └── supabase_schema.sql          # PostgreSQL table structures
└── README.md                        # Documentation
```

---

## ⚙️ Core Workflows

### 1. Inbound Portal Parsing & AI Replying (`01_spareroom_ai_bot.json`)
* **Trigger**: Webhook from local portal scraper.
* **Process**: Cleans HTML artifacts, extracts contact details, checks current listing inventory, and runs OpenAI GPT-4o to generate a response containing a personalized, shortened WhatsApp chat link.

### 2. WhatsApp Lead Initiation (`02_outbound_whatsapp.json`)
* **Trigger**: 10-minute cron job.
* **Process**: Scans portal databases for phone numbers, checks if a WhatsApp message has been sent, matches room specifications, and dispatches a template message via WATI/Woztell.

### 3. Inbound WhatsApp Intent Routing (`03_woztell_ai_brain.json`)
* **Trigger**: Webhook on WhatsApp message reply.
* **Process**: Categorizes inquiries (Booking, Portfolio, Callback, FAQ), manages conversation state, and generates answers using GPT-4o.

### 4. ClickUp CRM Sync & Retry Helper (`05_clickup_sync_engine.json`)
* **Trigger**: Callout from internal workflows.
* **Process**: Automatically updates ClickUp custom fields (pricing, room type, deposit status) and logs full transcripts. Unsuccessful API calls are logged to a retry queue and executed again every 30 minutes.

---

## 🗄️ Database Schema

Run the SQL schema located in `schema/supabase_schema.sql` inside your Supabase instance:

```sql
-- Leads Table
CREATE TABLE leads (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    phone VARCHAR(20) UNIQUE NOT NULL,
    email VARCHAR(100),
    status VARCHAR(50) DEFAULT 'new',
    advert_number VARCHAR(50),
    viewing_booked BOOLEAN DEFAULT FALSE,
    reservation_response VARCHAR(20) DEFAULT 'PENDING',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Rooms Table
CREATE TABLE rooms (
    id SERIAL PRIMARY KEY,
    advert_number VARCHAR(50) UNIQUE,
    property_address TEXT,
    price_pcm NUMERIC,
    ensuite BOOLEAN,
    available_from DATE
);

-- ClickUp Sync Errors Table
CREATE TABLE clickup_sync_errors (
    id SERIAL PRIMARY KEY,
    task_id VARCHAR(50),
    update_payload JSONB,
    error_message TEXT,
    retry_count INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## 🚀 Setup & Installation

### 1. Import n8n Workflows
1. Open your n8n instance.
2. Create a new workflow and import the `.json` files from the `workflows/` directory.

### 2. Configure Supabase Database
1. Create a new database in Supabase.
2. Execute the schema queries from `schema/supabase_schema.sql` in the SQL editor.

### 3. Environment Configuration
Create a `.env` file in the root directory:
```ini
SUPABASE_URL=https://your-project.supabase.co
SUPABASE_KEY=your-service-role-key
OPENAI_API_KEY=your-openai-api-key
WOZTELL_BEARER_TOKEN=your-token
CLICKUP_API_TOKEN=your-clickup-token
SHORTIO_API_KEY=your-shortio-key
```


---

## ?? Interested in a Similar System?

> Want to build something like this? Let's talk.

Whether you want to:
- ?? **Replicate this exact system** for your own business
- ??? **Build a custom automation** tailored to your workflow
- ?? **Discuss how AI automation** can solve your specific problem

**Feel free to reach out � I'd love to help.**

[![LinkedIn](https://img.shields.io/badge/Connect_on_LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](http://linkedin.com/in/aina-asim-659b67369)
[![WhatsApp](https://img.shields.io/badge/WhatsApp_Me-25D366?style=for-the-badge&logo=whatsapp&logoColor=white)](https://wa.me/923206455471)

?? **WhatsApp:** +92 320 6455471  
?? **LinkedIn:** [Aina Asim](http://linkedin.com/in/aina-asim-659b67369)
