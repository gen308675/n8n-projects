P1: Automated AI News Aggregator & Summarizer

An automated, resilient news aggregation workflow built on self-hosted **n8n** and powered by Google Gemini. The system fetches real-time news feeds, bypasses strict WAF/anti-bot scrapers using custom request headers, generates concise executive summaries via AI, and delivers formatted digests directly via Google OAuth 2.0.

---

Tech Stack & Infrastructure

* Orchestration: n8n (Self-Hosted on Railway)
* LLM Engine: Google Gemini (Flash-lite)
* Database: PostgreSQL (Railway production instance)
* Delivery & Auth: Gmail API via Google Cloud Platform (OAuth 2.0)
* Source Tracking: Custom RSS & HTTP Request nodes with `User-Agent` spoofing

---

Architecture & Data Flow


[ Trigger / Schedule ]
          │
          ▼
[ HTTP Request (Spoofed User-Agent) ] ──► (Bypasses WAF/Cloudflare Anti-Bot)
          │
          ▼
[ XML Parser Node ] ──► (Extracts Structured Article Feed Data)
          │
          ▼
[ Google Gemini Node ] ──► (Generates Executive Bullet Summaries)
          │
          ▼
[ Gmail Node (OAuth 2.0) ] ──► (Delivers Clean HTML Email Digest)
