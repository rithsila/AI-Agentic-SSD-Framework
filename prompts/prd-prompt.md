Start a Product Requirements Document for a new project.

Take the rough idea from `$ARGUMENTS` (or ask for it if empty) and produce `PRD.md` ready to save.

## Before writing
- Ask 3–5 clarifying questions about anything truly unclear (scope, users, payments, auth, data, deadline, skill level, hosting). Never guess on critical decisions.
- Wait for answers before drafting.

## PRD sections (in this order)
1. Problem & Goal — who, what problem, success criteria
2. Scope — In scope (v1) / Out of scope (defer to v2). Be aggressive about cutting.
3. User Flows — step-by-step for each main feature, very specific
4. Data Model — entities, key fields, ASCII relationship diagram
5. Tech Stack — locked decisions, specific versions, prefer the user's known stack (read `.claude/rules/stack.md` if present)
6. Architecture Overview — system diagram, external integrations, auth strategy, data flow for the main user action
7. API Design — every endpoint with method, path, auth, body, response, errors; assign task IDs (e.g. → BACKEND-02). Must fit one page — if it doesn't, v1 scope is too big.
8. UI/UX Requirements — style direction, key pages with one-line purpose each
9. Non-Functional Requirements — performance, security, browsers, mobile, accessibility
10. Open Questions — things the user needs to decide. Do NOT fill these in.

## Rules
- Be aggressive with "Out of scope" — anything that would add more than a week to v1 goes there.
- Recommend the SIMPLEST tech that achieves the goal.
- For the user's skill level and timeline, flag any feature that's unrealistic with 🔴 and suggest an alternative.
- For Open Questions, list things YOU need to know. Do not fill them in.

## After writing
Output:
- 🔴 Risks the user should know about
- ⚠️ Features I'd recommend cutting from v1 even if they asked for them
- 💡 Simplifications they might not have considered

Save to `PRD.md`.

Usage:
/spec prd
/spec prd "online clothing store with KHQR + COD for Cambodia"
