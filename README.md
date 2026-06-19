#  Customer Service AI Agents Platform

An intelligent, multi-channel platform designed to revolutionize customer service by bridging the gap between automated chatbots and human support. This platform features autonomous AI agents capable of natural conversation, real-time sentiment analysis, and seamless handoff to human supervisors. 

**This project was created as a Graduation Project (Proof of Concept - POC).**

---

## 🎥 Video Demo

[![Watch the demo](https://img.shields.io/badge/YouTube-Demo%20Video-red?style=for-the-badge&logo=youtube)](https://youtu.be/ObzqaqGmt68?si=TmkbW2t77SSSydRg)


---

## ✨ Features

- **Multi-Channel Support:** Engage with customers seamlessly across Telegram, WhatsApp (Twilio/Meta), Instagram, and WebRTC Voice.
- **Intelligent Autonomous Agents:** Resolves 70-80% of routine inquiries instantly using LLM-powered reasoning (Gemini, Groq) with context-aware responses.
- **Voice & Chat Capabilities:** Agents can engage in live bidirectional voice calls (via LiveKit) or multi-platform text chats.
- **Real-Time Sentiment Analysis:** Uses an ultralow-latency model to analyze sentiment and satisfaction scores instantly during interactions.
- **Supervisor Dashboard & "Human-in-the-Loop":** 
  - **Monitor:** Supervisors can view live interactions and track customer sentiment in real time.
  - **Whisper Mode:** Supervisors can inject invisible instructions to guide the AI mid-conversation.
  - **Takeover:** Seamless human takeover when an issue escalates beyond the AI’s capabilities.
- **Secure Authentication & RBAC:** Complete Role-Based Access Control (Admin/Supervisor) and Row-Level Security (RLS) integrated with Supabase.

---

## 💬 Chat Agents

Our platform features versatile chat agents designed to operate across multiple popular messaging platforms simultaneously.

- **Omnichannel Integration:** Capable of natively handling messages from **Telegram**, **WhatsApp** (via Twilio and Meta Graph API), and **Instagram Direct Messages**.
- **Context-Aware Responses:** Powered by advanced LLMs, maintaining the context of previous messages stored in the database for continuous, natural conversations.
- **Dynamic Webhooks:** Automatically registers webhooks on external channels, allowing real-time message receiving and dispatching directly from the single backend.
- **Supervisor Oversight:** Supervisors can monitor ongoing chats, view a real-time sentiment score (updated continuously based on the text), and take over manually if a customer becomes frustrated.

### 📊 Chat Pipeline Architecture

```text
┌─────────────────────────────────────────────────────────────┐
│  EXTERNAL CHANNELS                                          │
├──────────────┬──────────────────┬───────────────────────────┤
│  Telegram    │  WhatsApp        │  Instagram                │
│  (Native)    │  (Twilio/Meta)   │  (Meta Graph API)         │
└──────┬───────┴────────┬─────────┴──────────┬────────────────┘
       │                │                    │
       └────────────────┼────────────────────┘
                        ↓
         ┌──────────────────────────────────┐
         │  FastAPI Backend                 │
         │  POST /v1/{channel}/{agent_id}   │
         └──────┬───────────────────────────┘
                ↓
    ┌───────────────────────────┐
    │  ChatAgent LLM Processing │
    │  (Context + Prompt)       │
    └───────────┬───────────────┘
                ↓
    ┌───────────────────────────┐
    │  Response Generated       │
    │  Message Saved to DB      │
    └───────────┬───────────────┘
                ↓
       Response Sent Back
       via Original Channel
```

## 🎙️ Voice Agents

To simulate human-like telephone support, our voice agents use a state-of-the-art WebRTC pipeline.

- **Sub-second Latency:** Integrated with **LiveKit**, providing an ultra-low latency audio stream capable of handling interruptions and natural conversational pauses.
- **Advanced Processing Pipeline:**
  - **Hearing (STT):** Uses **Whisper v3 Large** powered by Groq's LPUs for instantaneous Speech-to-Text translation.
  - **Reasoning:** Driven by **Gemini 2.5 Flash-Lite** or **Gemini Live on Vertex AI** to formulate responses quickly.
  - **Speaking (TTS):** Responses are converted back to audio using high-quality TTS engines like Gemini Flash TTS or ElevenLabs.
- **Live Injection (Whisper Mode):** Supervisors listening in via the dashboard can "whisper" direct instructions (e.g., "Offer a 10% discount") to the AI agent, which the AI seamlessly incorporates into its ongoing conversation.
- **Mock Dialer PWA:** Allows users to initiate voice calls to the agents through a browser-based dialer, acting exactly like a standard phone call.

### 🎙️ Voice Pipeline Architecture

```text
 ┌──────────────┐      ┌─────────────────┐      ┌───────────────┐
 │ Customer     │      │ LiveKit Server  │      │ AI Voice      │
 │ (Mock PWA)   │ ───> │ (WebRTC Stream) │ ───> │ Pipeline      │
 └──────────────┘      └─────────────────┘      └──────┬────────┘
                                                       │
         ┌─────────────────────────────────────────────┘
         │
         ↓
 ┌────────────────┐    ┌─────────────────┐    ┌─────────────────┐
 │ Hearing (STT)  │    │ Reasoning (LLM) │    │ Speaking (TTS)  │
 │ Whisper v3 via │───>│ Gemini 2.5      │───>│ Gemini TTS /    │
 │ Groq LPUs      │    │ Flash-Lite      │    │ ElevenLabs      │
 └────────────────┘    └─────────────────┘    └────────┬────────┘
                                                       │
         ┌─────────────────────────────────────────────┘
         │
         ↓
 ┌─────────────────┐   ┌─────────────────┐    ┌───────────────┐
 │ LiveKit Server  │   │ Customer        │    │ Supervisor DB │
 │ (WebRTC Stream) │──>│ (Hears AI)      │    │ (Sentiment    │
 └─────────────────┘   └─────────────────┘    │  Analysis)    │
                                              └───────────────┘
```

---

## 🏗️ System Architecture

Our platform utilizes a single-backend architecture connecting multiple frontend clients with real-time AI and database layers:

1. **Client Layer:**
   - **Admin/Supervisor Dashboards (React):** Web applications for system governance, team management, metrics streaming via SSE, and live monitoring.
   - **Customer Touchpoint (PWA):** A mobile-first Progressive Web App simulating a phone dialer with WebRTC voice.
2. **Application Layer (Backend):**
   - **FastAPI:** Central hub handling API requests, business logic, webhook integrations, and multi-channel routing.
   - **LiveKit Server:** A dedicated WebRTC engine for sub-second latency audio/video streaming.
3. **Intelligence Layer:**
   - **LLM/AI Services:** Gemini 2.5 Flash-Lite (Reasoning), Gemini 2.5 Flash TTS (Speech generation), Whisper v3 Large via Groq (Speech-to-Text).
   - **Database (Supabase):** PostgreSQL with real-time sync capabilities, handling auth, user profiles, agent configurations, and logs.

---

## 💻 Tech Stack

- **Backend:** FastAPI, Python, LiveKit WebRTC, Server-Sent Events (SSE)
- **Frontend:** React, TypeScript, Vite, Tailwind CSS (PWA)
- **Database & Auth:** Supabase (PostgreSQL, Realtime, JWT Authentication)
- **AI & ML:** Gemini 2.5 Flash-Lite, Whisper v3 Large (Groq), ElevenLabs (Optional)
- **Integrations:** Telegram Bot API, Meta Graph API (WhatsApp/Instagram), Twilio

---

## 📁 Project Structure

```text
├── backend/          # FastAPI backend application, routers, AI agent logic, & webhooks
├── frontend/         # React application (Admin & Supervisor dashboards)
├── db/               # PostgreSQL schema and migration scripts
├── docs/             # Project documentation and API specs
├── landing_page/     # Marketing/Landing page for the platform
├── run_all.py        # Local development orchestrator script
├── QUICK_REFERENCE.md# Multi-channel agent configuration guide
├── SETUP_GUIDE.md    # Detailed guide on environment setup
└── seminar_documentation.md # High-level graduation project documentation
```

---

## 🚀 Quick Start Guide

### Prerequisites
- Python 3.10+
- Node.js 18+
- Supabase Account
- API Keys for the AI Models (Gemini, Groq) and integrations (LiveKit)

### 1. Backend Setup

```bash
cd backend
# Create and activate virtual environment
py -m venv venv
venv\Scripts\Activate.ps1  # Windows

# Install dependencies
pip install -r requirements.txt

# Configure environment variables
copy .env.example .env
# Important: Update .env with your Supabase, LiveKit, and API credentials
```

### 2. Frontend Setup

```bash
cd frontend

# Install dependencies
npm install

# Configure environment variables
cp .env.example .env.local
# Important: Update with your backend URL and Supabase Anon keys
```

### 3. Database Initialization
Run the SQL scripts located in the `db/` folder inside your Supabase SQL Editor in numerical order (from `001` to the latest) to set up the schemas, tables, and Row-Level Security policies.

### 4. Running the Platform Locally
We've included an orchestrator script that automatically starts the backend, frontend, and an Ngrok tunnel for testing webhooks locally.

```bash
# From the project root directory
python run_all.py
```

---

##  Deployment

The platform is built with modern deployment practices in mind. 

### Backend (Docker & Cloudflare Tunnels)
For home servers or production environments, you can use the included `docker-compose.yml`.

```bash
cd backend
# 1. Update .env with production keys (LiveKit, Supabase, Groq/Gemini)
# 2. Add your GCP JSON credentials to secret-google/
docker compose up -d --build
```
*Note: We recommend placing the backend behind a reverse proxy (like Nginx Proxy Manager) and securing it via a Cloudflare Tunnel.*

### Frontend (Vercel)
The React/Vite dashboard is optimized for Vercel.

1. Import the `frontend` folder into your Vercel dashboard.
2. Ensure the framework preset is set to **Vite**.
3. Add the required Environment Variables:
   - `VITE_API_BASE_URL` (Point to your production backend URL)
   - `VITE_SUPABASE_URL`
   - `VITE_SUPABASE_ANON_KEY`
4. Deploy! *Remember to add the Vercel production URL to your Supabase Auth Redirect URLs.*

---

## 📖 Further Reading

For deep-dives into specific components, please check out the following documents:
- [Seminar Documentation](./seminar_documentation.md) - For the complete Executive Summary, operational workflows, and KPIs.
- [Setup Guide](./SETUP_GUIDE.md) - For team workflows, secrets management, and dependency management.
- [Quick Reference](./QUICK_REFERENCE.md) - Detailed guide to configuring the Multi-Channel Agent System (Telegram, WhatsApp, Instagram).

---

## 📄 License
This project is for educational and proof-of-concept purposes.
