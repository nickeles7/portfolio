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

**Multi-agent AI pipeline that turns construction blueprints into priced material takeoff quotes**
[backend](https://github.com/nickeles7/backend-opgen-ai-public) &middot; [opgen.ai](https://www.opgen.ai/)

Contractors need material takeoffs to estimate costs and run projects. The process is manual, slow, and inconsistent. OpGen.ai automates the full pipeline end-to-end. Blueprint in, priced quote PDF out.

**Stack:** Python &middot; Flask &middot; LangGraph &middot; Grok 2 Vision (xAI) &middot; AWS (S3, Lambda, ECS Fargate) &middot; SerpAPI &middot; Mailgun &middot; WeasyPrint &middot; Firebase Auth

**What I built:**

- **Six-stage LangGraph pipeline.** Blueprint Analyzer extracts materials via Grok 2 Vision. Validator cross-references quantities against blueprint specs. Approver accepts or denies with a reason, looping back to Validator for correction (max 3 loops). Pricer queries SerpAPI for real Home Depot pricing. Router handles conditional flow. PDF Generator renders the final quote via Jinja2 and WeasyPrint, uploads to S3
- **Adaptive vision pipeline.** Started with OpenAI, moved to Grok 2 Vision. Low-res preview of all pages for context, then high-res per page. Adaptive DPI conversion so blueprint pages fit within Grok's token window
- **Lambda-triggered email delivery.** S3 event triggers PDF processing, generates pre-signed download URLs, delivers via Mailgun
- **Production deployment on AWS ECS Fargate** running a containerized Python backend with Gunicorn workers

**Scale:** Jun 2024 – Mar 2025

---

## MetaBloom

**AI-powered Hearthstone gaming assistant — built three times**
[overlay](https://github.com/nickeles7/metabloom-overlay) &middot; [web app](https://github.com/nickeles7/metabloom-frontend)

MetaBloom went through three pivots. Phase 1 was a failed attempt that informed the direction. Phases 2 and 3 were built to completion.

1. **Phase 1: AI Match Simulator.** Forked Fireplace (Python Hearthstone engine) 42 times trying to get AI-vs-AI games working. Built custom AI agents with 5-dimensional archetype profiles (tempo, risk, prioritization, mana usage, action ordering). Goal: outpace HSReplay's data collection with synthetic matches. Never got stable enough to ship
2. **Phase 2: Conversational AI System.** Not a chatbot. A routed system that turns natural language into valid Hearthstone outputs (deck codes, card queries, strategy). LLM handles interpretation, deterministic systems handle execution. Built an AST-based query compiler (QueryMaster) that translates natural language into PostgreSQL. Domain-aware ambiguity resolver with frustration tracking
3. **Phase 3: Arena Draft Overlay.** Windows desktop overlay (C# WPF) that reads Hearthstone's game memory via UnitySpy during Arena drafts. Detects card options in under 100ms. Scores contextually via AWS Lambda. Sends 10 structured analytical matrices to Claude for recommendations. Reverse-engineered HSReplay, HearthArena, and Firestone's scoring systems to build the base scoring foundation

**Stack:** C# / WPF / .NET 4.8 &middot; Next.js 15 &middot; React 19 &middot; TypeScript &middot; Firebase &middot; Stripe &middot; Grok API &middot; Claude AI &middot; AWS Lambda &middot; PostgreSQL &middot; Zustand &middot; Tailwind CSS

**What I built (Phase 3 — overlay):**

- **Game memory reading** via UnitySpy/MindVision. Attaches to Hearthstone's Unity process, reads draft state, detects card options in real-time. Handles multiple arena modes (standard and underground) with different memory layouts
- **10-matrix decision system.** Structured analytical context sent to Claude per pick: deck composition, mana curve, synergies, coverage gaps, archetype coherence, risk assessment. Which matrices get sent depends on draft phase. Early picks get different context than late picks
- **Cloud scoring engine** via AWS Lambda. Reverse-engineered three competitor scoring systems (HSReplay, HearthArena, Firestone) to build the base foundation, then built MetaBloom's own variation
- **Six overlay windows** managed by a central WindowManager. Multi-monitor support, z-order management relative to the game window, percentage-based positioning. Overlays appear only during card selection
- **Event-driven architecture** with pub-sub EventBus, MVVM, 34 services, phased DI bootstrap

**What I built (Phase 2 — web app):**

- **QueryMaster AST system.** Natural language goes in, an abstract syntax tree comes out (AND/OR/NOT conditions, operators like ILIKE, BETWEEN). AST compiles to SQL. SQL runs against PostgreSQL via Lambda. LLM decides what to query. System guarantees how it's executed
- **Deterministic pre-routing.** Deck codes caught by regex at 100% confidence before the LLM sees them. Each intent type routes to a different function set
- **Ambiguity resolver** with Hearthstone domain awareness and frustration tracking. Moved from "ask, ask, ask" to "infer, adjust, only ask when necessary"

**Scale:** Mar 2025 – Feb 2026

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

**[Basimo Blends](https://github.com/nickeles7/Basimo-Blends).** E-commerce site for an organic specialty spice brand. React 18, headless Shopify for checkout, Sanity CMS for blog content. 2025.

**BlokCrafters, WAX Blockchain Guild** (CEO, Jul 2019 – Mar 2025). Ran a blockchain infrastructure guild on the WAX network. Directed a team operating full/partial node infrastructure, API endpoints, and monitoring systems. Under the hood: Ansible-managed deployments, Python monitoring and alerting (WAXMon), Hyperion API indexing, WireGuard mesh networking, Oracle price feeds, and NFT platform services across 28 repositories. Submitted quarterly guild evaluation reports to the WAX Office of Inspector General, coordinating technical deliverables and governance compliance across a 2.5-year span.

---

## Technical Summary

| | Languages | Frameworks | Cloud / Infra | Data | Payments | AI/ML |
|---|---|---|---|---|---|---|
| | TypeScript, Python, C# | Next.js, React, Flask, WPF | AWS (S3, Lambda, ECS, SQS, EventBridge, CloudFront, SES, DynamoDB, EC2), Vercel | PostgreSQL, Prisma, Firebase/Firestore, Supabase, SQLite, Upstash Redis | Stripe Connect, Stripe Billing | LangGraph, Grok Vision, OpenAI, Claude, multi-agent orchestration, k-means clustering |

**How I work:** I use AI as my primary development tool. I architect the system, define the data models, make the infrastructure decisions, and direct AI through implementation. The output is production software with real users and real money moving through it. I'm strongest when the problem requires understanding how all the pieces connect and turning that into something that ships.

---

*Public repos linked above. Private repos available for code walkthrough on request.*
