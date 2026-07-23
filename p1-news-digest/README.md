# ☕ Project 1: Automated AI News Digest Agent

An automated, serverless intelligence workflow built on **n8n** that aggregates engineering and tech news from multi-source RSS feeds, summarizes the top stories using **Google Gemini AI**, and formats them into a clean HTML email digest delivered every morning.

---

## 📸 Workflow Architecture

```text
[ Schedule Trigger (7:00 AM) ]
              │
   [ RSS Feed Collector ] ──> Aggregates TechCrunch, The Verge, & Hacker News
              │
      [ RSS Read Node ]   ──> Fetches & parses article content
              │
    [ JS Data Sanitizer ] ──> Filters & structures top articles to optimize tokens
              │
  [ AI Agent (Gemini 2.5) ] ──> Evaluates impact & generates structured HTML summary
              │
     [ Gmail Delivery ]   ──> Sends formatted daily email newsletter