# PulseBoard AI

> A workspace-scoped customer CRM and pipeline foundation built with Next.js, Supabase Auth, PostgreSQL Row Level Security, and an intentionally constrained AI/integration layer.

[Live workspace](https://pulseboard-ai.vercel.app) · [Portfolio](https://abbas-portfolio-beta.vercel.app) · [LinkedIn](https://www.linkedin.com/in/abbas-hussain-56a61338b/)

## Why PulseBoard AI

PulseBoard AI is a full-stack learning and portfolio project focused on one practical requirement: **a workspace should load only its own CRM data**. The product starts empty by design, supports authenticated workspace onboarding, and turns customer and pipeline records into workspace-specific dashboard totals.

The public demo does not fabricate customer, revenue, or AI results. It keeps integrations disconnected until a user explicitly authorises and starts them.

## Production validation

The live product has been validated with a controlled test workspace for the following flows:

- Email/password authentication and first-workspace onboarding.
- Caller-bound workspace creation that keeps `owner_id` tied to the authenticated user.
- A workspace-scoped customer record and a linked pipeline deal.
- Dashboard roll-ups for customer value, active customers, open pipeline, and weighted forecast after refresh.
- Empty-data states for unauthenticated and newly created workspaces.

The AI provider, GitHub OAuth, HubSpot OAuth, and manual syncs were intentionally **not** activated during this validation.

## What is implemented

| Area | Current implementation |
|---|---|
| Authentication | Supabase email/password sign-up, sign-in, sign-out, and persisted browser sessions. |
| Workspace security | Authenticated users create a workspace through a caller-bound database RPC; the owner is derived from `auth.uid()`. |
| CRM | Workspace-scoped customer, contact, and deal interfaces with customer lifecycle, health, value, and relationship fields. |
| Pipeline | Qualified, Proposal, Negotiation, Won, and Lost stages with value, probability, expected-close, and customer linking. |
| Dashboard | Real workspace-only customer, customer-health, pipeline, and weighted-forecast summaries. Empty workspaces remain visibly empty. |
| AI Analyst boundary | A server-side route verifies the signed-in user and reads only the selected workspace before any provider request. It remains unavailable until server-only provider values are configured. |
| Integration foundation | GitHub and HubSpot connection cards, user-authorised OAuth entry points, repository selection, disconnect actions, and user-triggered manual-sync paths. |

## Deliberately not claimed

PulseBoard AI is not presented as a finished enterprise CRM or as a completed AI/integration product. The following remain intentionally outside the verified public claim set:

- The AI Analyst requires a configured server-only provider before it can answer questions.
- GitHub and HubSpot are disconnected by default; no automatic sync is promised.
- Metrics and reports are data-backed foundations with honest empty states, not a finished reporting suite.
- Multi-workspace switching is planned but is not yet shipped.
- This project has not received a third-party security audit.

## Architecture

```text
Browser (React / Next.js)
  ├─ Supabase Auth with public URL + publishable key
  ├─ Workspace-scoped customer, contact, and deal queries
  └─ No browser-side service-role or AI-provider credential

Supabase (PostgreSQL)
  ├─ Auth identities and workspace membership
  ├─ RLS policies on PulseBoard tables
  ├─ Caller-bound workspace creation RPC
  └─ Triggered owner-membership foundation

Next.js server routes
  ├─ Authenticated, workspace-scoped AI Analyst boundary
  └─ User-authorised GitHub / HubSpot integration actions
```

## Security and data boundaries

- RLS remains enabled on PulseBoard workspace, CRM, pipeline, AI-run, and integration tables.
- Workspace creation does not accept a browser-supplied owner ID; the database derives the owner from the authenticated caller.
- The browser client uses only Supabase public configuration. Server-side database and AI credentials stay server-side.
- AI prompts are built from the selected workspace only and instruct the provider not to invent missing evidence.
- Integration connections and syncs are explicit user actions; they are not started automatically.

> This project is a learning/portfolio implementation. These controls are source-backed design boundaries, not a claim of an independent security audit.

## Local development

### Prerequisites

- Node.js 20+
- pnpm 11+
- A Supabase project with the migrations in `supabase/migrations` applied

### Configure environment variables

Create a local environment file without committing it:

```bash
NEXT_PUBLIC_SUPABASE_URL=your_supabase_project_url
NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY=your_supabase_publishable_key

# Optional server-only values: required only for the corresponding feature.
SUPABASE_SERVICE_ROLE_KEY=server_only_database_key
PULSEBOARD_AI_BASE_URL=provider_base_url
PULSEBOARD_AI_API_KEY=server_only_provider_key
PULSEBOARD_AI_MODEL=provider_model_name
```

Never expose `SUPABASE_SERVICE_ROLE_KEY` or `PULSEBOARD_AI_API_KEY` in the browser, source control, issue reports, or screenshots.

### Run locally

```bash
pnpm install
pnpm dev
```

Open `http://localhost:3000`.

### Verify before deployment

```bash
pnpm test:schema
pnpm check
NODE_ENV=production pnpm build
```

## Stack

`Next.js 16` · `React 19` · `TypeScript` · `Tailwind CSS 4` · `Supabase Auth` · `PostgreSQL` · `Row Level Security` · `Lucide`

## Project status

PulseBoard AI is an independently built full-stack portfolio project by Abbas Hussain. The workspace onboarding, CRM customer/deal flow, and dashboard roll-ups have been controlled-tested on the live deployment. The AI provider and third-party integrations remain intentionally opt-in and unconfigured in the public demonstration.
