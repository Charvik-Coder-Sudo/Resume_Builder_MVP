# Phase 1 Report: Research, Reverse Engineering and Architecture

**Date:** 2026-11-02 · **Status:** Phase 1 documentation complete. Phase 2 has **not** been started.
**Main deliverable:** [`PROJECT_BLUEPRINT.md`](./PROJECT_BLUEPRINT.md) (28 sections, 13 Mermaid diagrams).

## 1. Research completed

| Area | What was done | Evidence quality |
|---|---|---|
| Workspace check | `D:\Resume Builder` was empty; nothing existed to overwrite | Verified |
| Reference repositories | All four cloned (depth 1) into a temporary scratch area and read: structure, dependencies, schema, auth, rendering, export, AI, ATS, tests, security, licence | Verified from source. 93 cited file paths re-checked: 93 exist |
| Dependency licences | 28 candidate packages checked against the npm registry | Verified |
| Official docs | Supabase pricing, RLS, SMTP, Next.js SSR auth; Vercel fair-use; Gemini API terms | Verified (fetched) |
| Competitors (9) | Web search of vendor pricing pages and third-party reviews | **Mostly third-party and often conflicting.** Tagged [R]/[P] in the blueprint. **No competitor product was operated hands-on** |
| AI free tiers | Groq, Gemini, OpenRouter, Cloudflare, Ollama model guidance | Third-party for Groq/Gemini quotas (consoles not readable); OpenRouter and Cloudflare partly from official docs |

## 2. Repositories studied

| Repo | Licence (verified) | Verdict |
|---|---|---|
| [reactive-resume/reactive-resume](https://github.com/reactive-resume/reactive-resume) (primary, deepest) | **MIT** | Learn patterns; copy only with attribution at a recorded commit |
| [Kiranism/cvtailor](https://github.com/Kiranism/cvtailor) | **MIT** | Patterns; its own plans document a real IDOR and a PDF-preview flash problem |
| [akhil-dara/CVAurum](https://github.com/akhil-dara/CVAurum) | **AGPL-3.0-only + commercial licence** | **Ideas only. Do not copy code** |
| [robindev2007/resumix](https://github.com/robindev2007/resumix) | **No licence file** | **Ideas only. Do not copy code** |

## 3. Recommended stack (details and alternatives in blueprint §7)

Next.js (App Router, TypeScript) modular monolith · Tailwind + shadcn/ui · **Supabase** (Auth: email/password and Google; Postgres with RLS; optional Storage) · Zod · Zustand + Zundo · dnd-kit · Tiptap (restricted) · **@react-pdf/renderer** for PDF and live preview (Forme evaluated in a spike) · **docx** for Word · pdfjs/unpdf + mammoth for in-browser parsing · Vercel AI SDK behind an `AiProvider` interface · **Ollama** locally, **Groq** in cloud (pending data-terms check) · Vitest/Playwright. Three framework-free packages: `resume-schema`, `ats-engine`, `ai`.

## 4. Major architectural decisions (blueprint §27)

1. Single deployable; no microservices.
2. **RLS is the authorization layer**; service-role key in two places only.
3. Resume is one JSON document (`content` + `design`); **sections and templates are not tables**. The 9 candidate tables became 6.
4. One renderer for preview and PDF (react-pdf), validated by Spike S1, with fallback F1.
5. **Exports and file parsing run in the browser**; uploaded files are never stored by default.
6. **Deterministic scoring engine first; the LLM is optional, bounded (±15 points) and replaceable.**
7. **Proposed scoring weights are revised** (double counting, N/A dimensions, mixed constructs): Job Match 80 % / ATS Readiness 20 %. **This deviates from the brief and needs your decision.**
8. **No provider may receive resume text unless its terms exclude training and human review.** Verified consequence: Gemini's *unpaid* tier is excluded.
9. Host-agnostic app; Vercel Hobby only while non-commercial.

## 5. Findings that changed the plan

- Supabase's **default email is limited to 2 messages/hour and team members only**, so custom SMTP is required before any beta.
- Supabase free has **no automatic backups**, pauses after 1 week idle, and allows 2 active projects.
- **Vercel Hobby is non-commercial only**; any financial gain by anyone involved in production counts.
- Free LLM quotas are shared by all users: roughly 110 (8B) or 22 (70B) analyses per day for the whole app on Groq's reported limits.
- Gemini's free tier uses submitted content for product improvement and human review.
- Next.js 16 uses `proxy.ts`; server code must use `getUser()`/`getClaims()`, not `getSession()`.
- Reactive Resume's `main` is now **MIT** (current LICENSE file). Its licence history was not examined.

## 6. Verification performed

| Check | Result |
|---|---|
| All 28 required sections present, in order | Pass |
| Mermaid diagrams | **13 of 13 render** with `@mermaid-js/mermaid-cli` on system Chrome (installed in scratch area only) |
| Links | 36 of 40 return HTTP 200. `resumegenius.com` and `trustpilot.com` returned 403 (bot blocking), `zety.com/pricing` timed out (consistent with the search note that it blocks direct checks), 1 is a CSP placeholder |
| Repository file paths | 93 of 93 exist |
| Internal consistency | Every `§` cross-reference resolves; latency targets, spike count and table counts reconciled |
| Technology recommendations | Versions and licences checked against npm; platform limits checked against official docs where reachable |

## 7. Unresolved issues (full list: blueprint §25.2)

| ID | Issue | Safe default |
|---|---|---|
| D1 | react-pdf vs Forme | react-pdf; decide by Spike S1 |
| D2 | SMTP provider and free quota (not researched) | Research 3 providers in Phase 2 |
| D3 | Brand name, domain, legal entity | Placeholder name |
| D4 | Groq / OpenRouter data-retention terms not verified | Groq disabled for real user data until reviewed; Ollama for dev |
| D5 | Exact Groq/Gemini quotas (sources conflict) | Read provider consoles |
| D7/D8 | Skills taxonomy source and licence; ethical calibration data | Hand-built taxonomy; synthetic + consented data |
| D9 | Privacy/legal review (India DPDP; GDPR if EU users) | Required before public launch |
| D10 | Monetisation intent | Assume non-commercial beta |
| D13 | Competitor workflows unverified hands-on | One browser-based review pass |
| D14 | Reactive Resume licence history | Re-check at the exact commit before copying anything |

## 8. Decisions needed from you before Phase 2

1. Approve or amend the **revised scoring model** (ADR-009).
2. Confirm **non-commercial beta** assumption (ADR-011) or state monetisation plans.
3. Confirm **dark-only** UI and the **8 to 10 original templates** scope.
4. Choose whether I should do the **hands-on competitor pass** (D13) at the start of Phase 2.

## 9. Phase 2 prerequisites

Node 24 and pnpm; Docker Desktop (for the Supabase CLI local stack); a GitHub repository; two free Supabase projects (staging, production); a Google Cloud OAuth client; a chosen SMTP provider; Ollama with a 7B/8B model (≥ 6 GB free RAM); decisions above. Phase 2 scope and exit criteria: blueprint §24.

## 10. Document locations

| File | Purpose |
|---|---|
| `D:\Resume Builder\docs\PROJECT_BLUEPRINT.md` | Full blueprint |
| `D:\Resume Builder\docs\PHASE_1_REPORT.md` | This report |

No application code, components, migrations or project dependencies were created. Temporary clones and the diagram-validation tooling live only in the Claude Code scratchpad directory, outside the project.
