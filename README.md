# Nickeles Kerkinni

I build production SaaS platforms end-to-end with AI. Database schemas, payment flows, cloud infrastructure, frontend. I architect systems, direct the implementation, and ship.

---

## NextClip

**Performance-based content creator marketplace**
[nextclip.net](https://nextclip.net) | Solo developer (under [github.com/gebro7/NextClip](https://github.com/gebro7/NextClip))

Brands post bounties with view goals and budgets. Creators submit clips, and payouts are calculated proportionally based on verified social media views. Think "bounty board for content creators" with real money moving through it.

**Stack:** Next.js 15 &middot; React 19 &middot; TypeScript &middot; Prisma &middot; PostgreSQL &middot; Supabase &middot; Stripe Connect &middot; AWS (S3, CloudFront, SQS, Lambda, EventBridge, SES, DynamoDB) &middot; Upstash Redis &middot; Vercel

**What I built:**

- **45+ model Prisma database** covering users, bounties, submissions, payments, payouts, agencies, conversations, notifications, affiliates, and audit logs
- **Stripe Connect payment pipeline.** Brands fund bounties via card/ACH, the platform calculates proportional payouts per clip (`clipperViews / totalViews * netBudget`), and creators cash out through Stripe Express accounts. 10% platform fee with tiered reductions
- **OAuth across 4 platforms.** YouTube (Analytics API + channel verification), TikTok, Instagram (Facebook Graph API), Twitter/X. Tokens encrypted with AES-256-GCM, automatic refresh via cron jobs
- **Adaptive view tracking engine** with a 6-tier system (viral, active, slow, dormant, dead, archived) that adjusts polling frequency per submission. Platform-specific API fetchers with rate-limit awareness. Fraud detection via velocity analysis and engagement baselines. Final view sweep triggered by EventBridge into Lambda 1 minute before deadline
- **Real-time messaging** via Supabase Realtime for DMs, workspace chats, typing indicators, and presence. AI content moderation (GPT-4o-mini) with regex fallback
- **Multi-tier approval workflow.** Submissions go through Brand review, then Platform review, then a 7-day appeal window. Full state machine with event hooks and notifications
- **Trust and fraud scoring** using a multi-factor score (0-100) based on account age, social connections, engagement baselines, velocity spikes, and earnings history. Risk levels from SAFE to CRITICAL
- **Agency system** with team management, clipper rosters, brand clients, auto-approval rules for in-house bounties, and persistent team conversations
- **User tier system.** Bronze, Silver, Gold, Diamond based on earnings and average views. Cold-start metrics pulled from YouTube Analytics until 5+ verified clips
- **Admin dashboard** for user management, payout processing, payment reconciliation, security logs, and email campaigns with audience targeting
- **260+ API routes**, 290+ React components, 12 cron jobs, 4 Lambda functions, 20+ rate limiters

**Scale:** 1,500+ commits &middot; 240K+ lines of TypeScript &middot; 5+ months of development &middot; live in production

---

## MDOPS Trading System

**Quantitative research + real-time copy-trading execution for Polymarket prediction markets**

Two-part system: an autonomous research engine that discovers profitable trading patterns, and a real-time executor that copies validated wallet signals with full risk management.

**Stack:** Python &middot; SQLite &middot; pandas &middot; scipy &middot; asyncio &middot; WebSockets &middot; Flask &middot; AWS (EC2, nginx) &middot; Polymarket CLOB API &middot; Gamma API

**What I built:**

- **Autonomous research loop** (`mdops-autoresearch`) that runs experiments against a 6.4M trade corpus across 500+ resolved markets. Each hypothesis is coded, executed, scored against three statistical gates (30+ observations, p < 0.05, expectancy > 2%), and kept or reverted automatically. 11,935+ experiments logged
- **K-means behavioral clustering** on 12-dimensional wallet profiles (trade frequency, win rate, entry zones, size consistency, sell ratio, etc.) to discover trader archetypes. Temporal quintile stability testing ensures patterns survive across time periods, not just in aggregate
- **4 confirmed production archetypes** with validated edge: Resolution Harvester (88.4% score, 97% WR), Diversified Harvester (75.2%, 86% WR), Contrarian Buyer (355% score, 72.6% WR), and Scatter Shot (247%, 65.4% WR). Each with exported wallet lists and executor configs
- **Real-time copy-trading executor** (`mdops-poly-pipeline`) monitoring 50 profiled wallets via WebSocket with 35-120ms detection latency. Evaluates 15+ circuit breakers per signal: hard drawdown limits, max positions, daily/weekly loss caps, consecutive loss triggers, category exposure caps, and per-market concentration limits
- **Capital framework** using quarter-Kelly sizing with 4 edge tiers, resolution time decay (full size at 7 days, 20% at 30 days, skip beyond), price-band multipliers, wallet confidence scores, and conviction detection based on trade size vs. baseline
- **Archetype-aware sell logic.** Flippers mirror sells immediately; all others hold to resolution based on configurable trust scores. Take-profit and stop-loss rules adjust by archetype and days-to-resolution
- **Auto-bench system** that removes wallets with 5+ consecutive losses or negative realized edge, with 24h cooldown before re-activation
- **Live monitoring dashboard** at `dash.metabloom.io` with admin/invite auth tiers, 5-second polling, P&L curves, signal flow visualization, category exposure breakdown, and wallet scoreboard

**Scale:** Running on two AWS boxes (~$20/mo) &middot; currently in dry-run (paper trading) with production-ready execution &middot; actively developed

---

## OpGen AI

**AI-powered blueprint-to-material-quote platform for construction**
[backend](https://github.com/nickeles7/backend-opgen-ai-public) &middot; [frontend](https://github.com/nickeles7/opgen-ai-frontend)

Upload architectural blueprints, AI analyzes them, get an itemized material quote with pricing. Designed to cut quoting time from hours to minutes for contractors and architects.

**Stack:** React 18 &middot; Flask &middot; Python &middot; LangGraph &middot; Grok 2 Vision (xAI) &middot; OpenAI &middot; AWS (S3, Lambda, ECS Fargate, CloudWatch) &middot; Firebase Auth &middot; Mailgun &middot; WeasyPrint

**What I built:**

- **Multi-agent AI pipeline** using LangGraph with 6 specialized agents:
  - `BlueprintAnalyzerAgent` uses Grok 2 Vision to extract materials from blueprint images
  - `ValidatorAgent` ensures structural correctness of extracted data
  - `ApproverAgent` approves or triggers a correction loop (max 3 iterations)
  - `PricerAgent` fetches real-time material pricing via web search APIs
  - `PDFGeneratorAgent` produces professional PDF quotes
  - `WorkflowRouterAgent` orchestrates decisions between agents
- **6-step guided intake wizard** on the frontend. Blueprint upload (drag-and-drop, supports PDF/PNG/JPG/CAD), building classification, material specification, measurements, construction methods, and supporting documents
- **Production deployment on AWS ECS Fargate** running a containerized Python backend with Gunicorn + gevent workers, 2GB memory / 1 vCPU task definition
- **Lambda-triggered email delivery.** S3 event triggers PDF processing, generates pre-signed download URLs, delivers via Mailgun
- **Quote management** with view, edit, and download as PDF. Full history tracking per user in Firestore
- **Admin panel** for invite code generation with expiry dates and user whitelist management
- **Firebase Auth** with email/password and Google OAuth

**Scale:** 140 commits across frontend + backend &middot; Jun 2024 – Apr 2025

---

## MetaBloom

**AI-powered Hearthstone gaming assistant — built three times**
[overlay](https://github.com/nickeles7/metabloom-overlay) &middot; [chat frontend](https://github.com/nickeles7/metabloom-frontend) &middot; [website](https://github.com/nickeles7/metabloom-website)

MetaBloom went through three complete pivots, each built to near-completion before evolving into the next:

1. **Phase 1: AI Match Simulator.** Forked Fireplace (Python Hearthstone engine), built custom AI agents with 5-dimensional archetype profiles (tempo, risk, prioritization, mana usage, action ordering) to generate millions of simulated games. Goal: outpace HSReplay's data collection with synthetic matches to map the meta faster
2. **Phase 2: Standalone AI Chat.** Pivoted to a web-based ChatGPT-style interface specifically for Hearthstone strategy. Deck code generation, theory crafting, card analysis — all through conversational AI with Grok streaming and function calling
3. **Phase 3: Arena Draft Overlay.** Final version. Native Windows overlay that reads Hearthstone's game memory during Arena drafts and displays real-time AI card recommendations on top of the game

**Stack:** Next.js 15 &middot; React 19 &middot; C# / WPF / .NET 4.8 &middot; Firebase &middot; Stripe &middot; Grok API &middot; Claude AI &middot; AWS Lambda &middot; Zustand &middot; Tailwind CSS

**What I built (Phase 3 — shipped version):**

- **Windows desktop overlay** (C# WPF) that reads Hearthstone game memory via UnitySpy, detects Arena draft card options in real-time, and displays AI-powered recommendations as a transparent overlay on top of the game
  - Cloud-based scoring engine via AWS Lambda for card strength analysis with deck context, mana curve, and synergy detection
  - Claude AI integration for natural language card recommendations with 44+ prompt templates
  - Hero winrate display during hero selection
  - Deck tracking and coverage analysis overlays
  - Re-analysis system for deeper AI evaluation on demand
  - Multi-monitor support, DPI-aware asset rendering, draggable UI elements
  - Event-driven architecture with EventBus pattern, MVVM, 34 services
- **Web AI chat interface** (Next.js) with real-time conversational AI using Grok streaming, Hearthstone deck building, function calling for structured tasks, and ambiguity resolution. System prompt caching reduced token usage by 91%
- **Landing page and payment portal** (Next.js) with subscription management ($6.99/mo), Stripe webhooks, download portal with access control, and affiliate tracking
- **Shared infrastructure** using Firebase Auth across all three apps, Firestore for user data and token tracking, Stripe billing, and AWS Lambda for AI/scoring APIs

**Scale:** 216 commits across 3 repos &middot; Feb – Sep 2025

---

## TikTok Content Automation

**Automated slideshow content generation system for TikTok**
[repo](https://github.com/nickeles7/tiktok-slideshow-automation)

End-to-end system for generating "hopecore" motivational slideshow posts. Mines real posts from X/Twitter via Grok, clusters them into themes, generates hooks through differentiation matrices, and renders final slideshows with Python.

**What I built:**

- **Differentiation matrix system** that rotates across 4 dimensions (hook frame, story engine, viewer identity, numeric type) to prevent repetitive content. No combination repeats within a batch; no triplet repeats in last 8 posts
- **Progressive reveal funneling** — slides 1-2 hook broad side-hustle audiences with no jargon, slide 3 introduces the concept, slides 4+ go niche
- **Python rendering engine** (Pillow + Cairo) with TikTok-safe zone awareness (19% top, 50% bottom for UI overlays), multi-pass shadow system, scrim for bright backgrounds, and highlight colors with stroke outlines
- **State machine workflow** for session management — routes to generation, rendering, or posting based on current state of drafts and data freshness

---

## Other Work

**Basimo Blends.** E-commerce site for a specialty food brand. React 18, Sanity CMS for content management, Shopify Buy SDK for checkout. 54 commits, 2025.

**BlokCrafters, WAX Blockchain Guild** (CEO, Sep 2021 – May 2024). Ran a blockchain infrastructure guild on the WAX network. Directed a team operating full/partial node infrastructure, API endpoints, and monitoring systems. Under the hood: Ansible-managed deployments, Python monitoring and alerting (WAXMon), Hyperion API indexing, WireGuard mesh networking, Oracle price feeds, and NFT platform services across 28 repositories. Submitted quarterly guild evaluation reports to the WAX Office of Inspector General, coordinating technical deliverables and governance compliance across a 2.5-year span.

---

## Technical Summary

| | Languages | Frameworks | Cloud / Infra | Data | Payments | AI/ML |
|---|---|---|---|---|---|---|
| | TypeScript, Python, C# | Next.js, React, Flask, WPF | AWS (S3, Lambda, ECS, SQS, EventBridge, CloudFront, SES, DynamoDB, EC2), Vercel | PostgreSQL, Prisma, Firebase/Firestore, Supabase, SQLite, Upstash Redis | Stripe Connect, Stripe Billing | LangGraph, Grok Vision, OpenAI, Claude, multi-agent orchestration, k-means clustering |

**How I work:** I use AI as my primary development tool. I architect the system, define the data models, make the infrastructure decisions, and direct AI through implementation. The output is production software with real users and real money moving through it. I'm strongest when the problem requires understanding how all the pieces connect and turning that into something that ships.

---

*Public repos linked above. Private repos available for code walkthrough on request.*
