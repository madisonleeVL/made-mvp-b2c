# MADE MVP — "Turn AI into Momentum"

## Overview
MADE is a premium AI workspace that helps revenue teams turn AI into measurable momentum.  
This MVP delivers a self-serve, multi-user application where each client logs in, uses their personalized MADE Agent, and saves results to their Playbook.  

Built with Next.js, Tailwind, Supabase, and OpenAI, this version focuses on the three core MADE workflows:
- Grow → Prospecting and activation  
- Accelerate → Deal enablement and meeting preparation  
- Close → ROI and executive communication  

The goal is to deploy a fully functional, multi-tenant MVP on Vercel with clean design, private user data, and premium MADE branding.

---

## Tech Stack

| Layer | Tool | Purpose |
|-------|------|----------|
| Frontend | Next.js + Tailwind | Web app and UI logic |
| Design | Figma Make | AI-generated UI / UX exported as React components |
| Backend | Next.js API Routes | Serverless endpoints for OpenAI and Supabase |
| AI Model | OpenAI GPT-4o-mini | Generates MADE Agent outputs |
| Database | Supabase | Stores user profiles and Playbook entries |
| Auth | Supabase Auth | Multi-client login and signup |
| Hosting | Vercel | Frontend and API hosting |

---

## Architecture

User → MADE App (Vercel)
      ├── UI (React + Tailwind)
      ├── /api/agent/respond → OpenAI (AI generation)
      ├── /api/playbook/... → Supabase (data)
      ├── /auth → Supabase Auth (login/signup)
      └── /lib/madePrompt.ts → Master Agent prompt and JSON contracts

---

## Core Modules

| Module | Use Cases | Description |
|---------|------------|-------------|
| Grow | Prospecting Email · Optimized DM · Role Brief | Outbound creation, account strategy, and prospect insights upsell |
| Accelerate | Meeting Prep · Objection Handling · Deal Prep Sheet | Deal enablement and in-flight acceleration |
| Close | ROI Recap · Closing Message | Executive-ready business justification and closing tools |

Each workflow follows MADE’s format: Draft → Rationale → Hacks (or structured Meeting Prep layout).

---

## Data Model

### profiles
Stores personalized agent context per user.
```sql
user_id uuid pk, name text, role text, company text, what_you_sell text,
icp_title text, icp_industry text, icp_top_challenge text,
primary_goal text, tone text, win_that_matters text, voice_samples text[],
created_at timestamptz, updated_at timestamptz
```

### playbook
Saves generated outputs and meeting prep payloads.
```sql
id uuid pk, user_id uuid fk, workflow text, scenario text,
prompt text, draft text, rationale text[], hacks text[], payload jsonb, created_at timestamptz
```

RLS: Each user only accesses their own data (auth.uid() = user_id).

---

## Environment Variables

Add to .env.local and in Vercel → Settings → Environment Variables:

```bash
OPENAI_API_KEY=sk-...
NEXT_PUBLIC_SUPABASE_URL=https://xyz.supabase.co
SUPABASE_SERVICE_KEY=your-service-role-key
```

---

## Setup Instructions

```bash
# 1. Create project
npx create-next-app@latest made-mvp --ts --eslint
cd made-mvp

# 2. Install dependencies
npm i tailwindcss postcss autoprefixer @supabase/supabase-js openai framer-motion
npx tailwindcss init -p

# 3. Replace/Add files
# - tailwind.config.js (brand tokens)
# - app/globals.css (cream theme)
# - lib/ (supabase.ts, db.ts, madePrompt.ts)
# - pages/api/ (agent/respond.ts, playbook/saveMeetingPrep.ts)
# - app/page.tsx (main layout)
# - components/ (from Figma Make export)
```

---

## Brand System (Tailwind Tokens)

```js
colors: {
  cream:"#FAFAF7", charcoal:"#111418",
  accentA:"#00E5FF", accentB:"#F4A6C8",
  success:"#22C55E", warning:"#F59E0B", error:"#EF4444"
},
fontFamily:{
  display:["Space Grotesk","sans-serif"],
  body:["Inter","system-ui","sans-serif"]
},
borderRadius:{ xl2:"16px", modal:"24px" },
boxShadow:{ soft:"0 10px 24px rgba(0,0,0,.12)" }
```

---

## API Endpoints

### POST /api/agent/respond
Generates workflow output via OpenAI.

**Request:**
```json
{
  "userId": "uuid",
  "workflow": "GROW | ACCELERATE | CLOSE",
  "useCase": "PROSPECT_EMAIL | MEETING_PREP | ROI_RECAP ...",
  "userPrompt": "string",
  "extras": { "scenario": "Email", "pastedIntel": "optional context" }
}
```

**Response:** JSON matching the chosen use case (draft, rationale, hacks, etc.)

---

### POST /api/playbook/saveMeetingPrep
Saves structured Meeting Prep JSON to Supabase.

**Request:**
```json
{
  "userId":"uuid",
  "scenario":"MeetingPrep",
  "prompt":"Prep for <Company>",
  "prep":{ "agenda":[],"context_summary":"","top_topics":[],"discovery_questions":[],"challenge_positions":[],"talk_track":"","next_steps":[] }
}
```

---

## Authentication — Multi-Client Setup

Implement Supabase Auth for email/password and magic-link login.

**Deliverables:**
- Add /auth page for sign-up and sign-in.
- On first login, auto-create a profile record in Supabase.
- Update all /api/* routes to fetch user via supabase.auth.getUser().
- Use that user.id in all DB calls and RLS checks.

Result: multiple clients can sign up, generate content, and securely access their own Playbooks.

---

## Level of Effort

| Area | Lift | Notes |
|------|------|-------|
| Frontend | Light to Moderate | Mostly connecting Figma Make export, state, and animation |
| Backend | Light | One AI endpoint and one save endpoint |
| Data | Light | Two tables with RLS |
| Auth | Light | Supabase drop-in |
| Deploy | Light | Vercel + environment setup |

Estimated: approximately one week for one full-stack developer.

---

## Definition of Done

- App deployed on Vercel (cream theme, MADE flag top-left, 3D fire icon bottom).  
- Login and signup functional via Supabase Auth.  
- New users auto-create profiles on first login.  
- /api/agent/respond returns valid JSON for each workflow.  
- Tabs switch correctly across Grow, Accelerate, Close.  
- Meeting Prep displays structured layout and allows Save to Playbook.  
- Each user sees only their own Playbook items (RLS verified).  
- Tokens and motion match Figma Make v5.  
- Secrets stored securely server-side.

---

## Optional Stretch Goals

- Add Google Auth and forgot password flow.  
- Add /playbook viewer route.  
- Add success toast and loading states.  
- Connect Slack and Calendar integrations for Team Packages.

---

## Developer Notes

- Backend supports multiple tenants; Auth enforces identity separation.  
- Master Agent logic provided in /lib/madePrompt.ts.  
- Use a single motion curve: cubic-bezier(.4, 0, .2, 1).  
- Deliver clean, modular code that deploys to Vercel in under 5 minutes once environment variables are configured.

---

## Deliverable

A deployed multi-user MADE MVP where any client can sign up, log in, and use their personalized MADE Agent within a premium MADE-branded UI.

Made with MADE — Turn AI into Momentum.

