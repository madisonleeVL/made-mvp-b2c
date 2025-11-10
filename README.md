# made-mvp-b2c# 🧭 MADE MVP — Development Scope (v2, Multi-Client Enabled)

## Overview
MADE is a premium AI workspace that helps revenue teams turn AI into measurable momentum.  
This MVP delivers a personalized MADE Agent experience for multiple clients (each with their own login, profile, and Playbook).  
Architecture, design system, and AI contracts are already defined — this document details what remains for implementation and deployment.

---

## ✅ Provided Assets & Decisions

| Area | What You Already Have |
|------|-----------------------|
| **Design** | Figma Make master prompt (Cream Theme v5) with layout, spacing, typography, tokens, and icon placement |
| **Architecture** | Next.js + Tailwind frontend, Supabase backend, OpenAI API integration |
| **AI Logic** | Master MADE Agent prompt + JSON contracts for Grow / Accelerate / Close workflows |
| **Data Model** | Supabase tables `profiles` and `playbook` + RLS policies (SQL provided) |
| **Brand System** | Tokens: Cream #FAFAF7, Charcoal #111418, Teal #00E5FF, Pink #F4A6C8; fonts Space Grotesk + Inter |
| **Docs** | Complete `README.md` with setup steps, environment variables, and Definition of Done |
| **Goal** | Launch a live, multi-user MADE MVP where each client signs up, generates content, and stores their Playbook privately |

---

## 🧱 Build Requirements

### 1️⃣ Frontend (Next.js + Tailwind)
- Import Figma Make export into `/components`.
- Implement:
  - Tabs **Grow / Accelerate / Close**
  - Use-case dropdown + textarea + “Generate Momentum” button
  - Output reveal → Draft → Rationale (300 ms) → Hacks (+150 ms)
  - Structured **Meeting Prep** view (agenda / context / topics / questions / challenges / talk track / next steps)
  - **Save to Playbook** button for Meeting Prep
- Right-rail upsells: **Prospect Insights** and **Team Packages**
- Maintain MADE brand visuals: cream background, dark text, teal accents, 3D fire icon in footer.

---

### 2️⃣ Backend (API Routes)
- `/api/agent/respond`  
  - Build system prompt from user profile + workflow/useCase.  
  - Call OpenAI (gpt-4o-mini), parse JSON response, return clean keys.
- `/api/playbook/saveMeetingPrep`  
  - Save structured meeting-prep JSON to Supabase `playbook.payload`.
- Integrate **Supabase server client** for authenticated requests.  
  Use `supabase.auth.getUser()` to pull the logged-in user’s ID (no hard-coded IDs).

---

### 3️⃣ Data (Supabase)
- Create `profiles` and `playbook` tables with RLS (policies provided).
- Seed 1 test profile for QA.
- Implement helper functions:
  - `getProfileByUserId()`
  - `savePlaybook()`

---

### 4️⃣ Authentication (Multi-Client Enablement)
**Implement Supabase Auth** for multi-tenant sign-up and sign-in.

Deliverables:
- Enable Email + Magic Link + (optional Google) Auth.
- Add lightweight `/auth` page:
  - Sign-up (name, company, email)
  - Login (password or magic link)
  - Redirect → `/`
- On first login, auto-create user profile (row in `profiles`).
- API routes use Supabase server client to derive `user.id`.
- Verify RLS: each user sees only their own Playbook and profile records.

✅ Result: Multiple clients can sign up → generate → save outputs → stay fully isolated.

---

### 5️⃣ Deployment / DevOps
- Set environment variables in Vercel:
  ```bash
  OPENAI_API_KEY=sk-...
  NEXT_PUBLIC_SUPABASE_URL=https://xyz.supabase.co
  SUPABASE_SERVICE_KEY=your-service-role-key
