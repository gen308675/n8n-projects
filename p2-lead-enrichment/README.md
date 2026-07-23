Project 2: Lead Enrichment & Automated Routing System

An automated, event-driven lead processing pipeline built on **n8n**[cite: 2]. This system ingests incoming webhooks[cite: 2], checks for existing duplicates in **Supabase**[cite: 2], enriches corporate metadata via **Abstract API**[cite: 2], dynamically routes notifications across **Slack** channels[cite: 2], and reports execution failures using a dedicated **Global Error Handler**[cite: 1, 2].


Architecture & Workflow Logic


               [ Webhook Ingestion ]
                         │
           [ Supabase: Duplicate Check ]
                         │
              ┌──────────┴──────────┐
          (Existing)             (New Lead)
              │                     │
      [ #unassigned-leads ]  [ Supabase: Insert Lead ]
                                    │
                         [ Abstract API Enrichment ]
                                    │
                         ┌──────────┴──────────┐
                      (Failed)              (Enriched)
                         │                     │
                [ #unassigned-leads ]   [ Employee Threshold Check ]
                                               │
                                     ┌─────────┴─────────┐
                                (>= 500)              (< 500)
                                   │                     │
                         [ #enterprise-leads ]     [ #smb-leads ]

Workflows Included

1. Primary Workflow: `Lead Enrichment Router` (`lead-enrichment.json`)

Webhook Trigger:Accepts incoming `POST` JSON payloads.


Deduplication: Queries the `leads` table in Supabase by email address. Duplicate records short-circuit to Slack.

Database Persistence: Inserts new valid leads directly into Supabase.


Enrichment: Extracts domain names from lead email addresses and fetches company employee counts using Abstract API.


Conditional Routing:
Enterprise leads (employees => 500)`#enterprise-leads`

MB leads** (employees < 500)`#smb-leads`

Unenriched/Fallback leads `#unassigned-leads`

2. Monitoring Workflow: `Global Error Handler` (`global-error-handler.json`)



Error Trigger: Automatically intercepts execution crashes or unhandled node failures from attached workflows.


Alerting: Formats execution details (Workflow Name, Execution ID, Error Message, and Direct Canvas Link) and posts alerts to `#devops-alerts`.


Step-by-Step Setup & Deployment Guide

Step 1: Environment & Database Setup

1. Log into Supabase and create a `leads` table with columns: `first_name`, `last_name`, `email`, and `company_name`.


2. Set up a Slack App with Bot token permissions to post in `#enterprise-leads`, `#smb-leads`, `#unassigned-leads`, and `#devops-alerts`.


3. Obtain an API Key from Abstract API (Company Enrichment Service).



Step 2: Import Workflows into n8n

1. Open your n8n instance canvas.


2. Go to Workflows > Import from Files and upload `global-error-handler.v1.json`.


3. Save and activate the `[GLOBAL] Error Handler` workflow. Note its Workflow ID.


4. Repeat the import process for `lead-enrichment-router.main.v1.json`.



Step 3: Configure Credentials & Settings

1. Attach your Slack API, Supabase API, and Abstract API Key credentials to the corresponding nodes.


2. Open the **Settings** menu for the `Lead Enrichment Router` workflow:


* Set Error Workflow to `[GLOBAL] Error Handler`.


3. Toggle the workflow to **Active**.


Testing Production Webhooks

Trigger the production workflow using `curl`:

bash
curl -X POST https://<your-n8n-instance-url>/webhook/6fc8e0ca-0939-4707-9d45-142a190896c6 \
  -H "Content-Type: application/json" \
  -d '{
        "first_name": "Satya",
        "last_name": "Nadella",
        "email": "satya@microsoft.com",
        "company_name": "Microsoft"
      }'
