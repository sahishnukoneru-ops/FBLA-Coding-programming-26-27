# Harbor — Program Documentation

**Volunteer desk for Ridge Community Partners**
FBLA Coding & Programming · 2026–2027 · Topic: *Serving the Community: Nonprofit Volunteer Management*
Individual entry — Sahishnu Koneru · [Live demo](https://nunu-harbor.netlify.app)

This file is the full documentation package referenced by [`README.md`](README.md) — architecture, rubric alignment, and design rationale, for judges, reviewers, or anyone digging into the code. For setup and the quick demo script, see the README. Third-party libraries and sample-data notes are in [`ATTRIBUTIONS.md`](ATTRIBUTIONS.md).

## Contents

1. [Project Overview](#1-project-overview)
2. [How the Topic Is Addressed](#2-how-the-topic-is-addressed)
3. [Running the Program](#3-running-the-program)
4. [Seven-Minute Presentation Walkthrough](#4-seven-minute-presentation-walkthrough)
5. [Architecture & Code Organization](#5-architecture--code-organization)
6. [User Experience & Accessibility](#6-user-experience--accessibility)
7. [Intelligent Feature: Guide](#7-intelligent-feature-guide)
8. [Input Validation](#8-input-validation)
9. [Data & Reporting](#9-data--reporting)
10. [Testing](#10-testing)
11. [Rubric Alignment](#11-rubric-alignment)
12. [Attributions & Copyright](#12-attributions--copyright)

---

## 1. Project Overview

Harbor is a standalone volunteer-management application built for **Ridge Community Partners**, a specimen (fictional, non-live) Charlotte-area nonprofit created for this competition. It responds directly to the 2026–2027 topic, *Serving the Community: Nonprofit Volunteer Management*, by giving nonprofit leaders and volunteers one shared tool to recruit, organize, manage, and monitor volunteer activity.

The program runs as a static single-page application (SPA) entirely in the browser — no server, no external database, and no live API key required. All sample data (names, hours, seat counts, ZIP codes) is fabricated for demonstration and is not copied from a live roster or an existing volunteer product.

**What Harbor does:**

- Volunteers browse a public calendar of service opportunities and join shifts without overbooking a seat.
- Shifts that fill up automatically waitlist new sign-ups and promote the next person if a seat opens.
- Volunteers log completed hours against shifts that have already happened.
- Leaders see a "desk board" that ranks which understaffed shifts to fill first and who to call.
- Leaders generate a sortable, filterable hours report that exports to CSV.
- A built-in, offline Q&A assistant (**Guide**) answers volunteer questions such as which shifts are understaffed or whether a ZIP code is served.

## 2. How the Topic Is Addressed

| Topic Requirement | Harbor Feature | Where |
|---|---|---|
| Recruit volunteers | Public join flow with validated name, email, phone, ZIP, and age | Join page (`/join`) |
| Organize service opportunities | Shift calendar with capacity, waitlisting, and category filters | Shifts page (`/opportunities`) |
| Manage volunteers & records | Roster, shift claims, and age-restricted routes (e.g., 18+ delivery) | Desk → Roster / Schedule |
| Monitor participation | Hours logging tied to completed shifts only, plus reporting | Desk → Hours / Reports |
| Support organization leaders | Match list ranking understaffed shifts and who to call | Desk → Board |

## 3. Running the Program

Requires **Node 20+**.

```bash
export PATH="$HOME/.local/node/bin:$PATH"
cd ~/Desktop/harbor
npm install
npm run dev -- --host 127.0.0.1 --port 5174
```

Open **http://127.0.0.1:5174**.

A hosted copy is also available: **https://nunu-harbor.netlify.app**
On the hosted copy, Guide runs in offline-only mode (no API key configured), and refreshing the page starts a new demo session.

Because venue wifi can be unreliable, build ahead of time and serve a static preview locally as a fallback:

```bash
npm run build
npm run preview -- --host 127.0.0.1 --port 5174
```

Harbor is a static SPA — Guide runs entirely in the browser, there is no API key, and the interface uses system fonts, so it renders identically without an internet connection.

## 4. Seven-Minute Presentation Walkthrough

Numbers on screen change as seats are claimed; the path itself does not.

1. **Home** — Ridge Community Partners, open seats, most-short shift.
2. **Shifts** — Saturday pantry has seats; Saturday reading buddies is **Full** (waitlist); filter `tutoring`.
3. **Join** — bad phone `704555` is blocked; ZIP `29708` is blocked; ZIP `28211` is accepted. Valid sign-up: `Sahishnu Koneru`, age 17, Tutoring.
4. Claim the pantry seat, then attempt the **18+** pantry delivery route — age 17 is refused.
5. **Guide** — ask "What's understaffed?" then "Do you cover 28209?"
6. **Desk → Board** — short shifts with named people to call; place one.
7. **Hours** — log Priya on **Wednesday community supper** (already happened); try logging tomorrow's pantry shift — refused.
8. **Reports** — sort Hours / Name / Goal, filter Pantry, copy CSV.

Sign-in shortcut: `priya.rao@example.com`

## 5. Architecture & Code Organization

Built with **TypeScript**, **React**, and **Vite**. Logic is split into small, single-purpose modules rather than one large file, so each rubric area (validation, matching, reporting, etc.) has a clear, comment-headed home.

| File | Responsibility |
|---|---|
| `src/data/org.ts` | Agency profile, served ZIP codes, and skill tags |
| `src/data/seed.ts` | Sample roster, shifts, sign-ups, and hour logs (the arrays the app runs on) |
| `src/lib/validate.ts` | Validates name, email, phone, ZIP, age, and hours — both format (syntax) and business rules (meaning) |
| `src/lib/volunteers.ts` | Join, sign-in, and account activation logic |
| `src/lib/shifts.ts` | Claiming a seat, waitlisting, overlap checks, and promotion off the waitlist |
| `src/lib/hours.ts` | Logs hours against a shift only after that shift has finished |
| `src/lib/match.ts` | Ranks understaffed shifts and suggests who to call |
| `src/lib/reporting.ts` | Builds the sortable report and CSV export |
| `src/lib/guide.ts` | The offline Q&A logic behind Guide |

**Routes**

- Volunteer: `/` `/opportunities` `/join` `/me` `/help`
- Desk (leader): `/desk` `/desk/roster` `/desk/schedule` `/desk/hours` `/desk/reports`

**Language selection:** TypeScript was chosen so that a shift, a volunteer, and an hour log are distinct, statically checked types — the compiler catches it if one is mistakenly used in place of another before the program ever runs. React lets the volunteer-facing calendar and the leader-facing desk share a single in-memory data store without duplicating state. Vite was chosen so the finished program compiles into a standalone static site that runs in any browser with no server or database dependency — useful given that conference wifi can be unreliable.

## 6. User Experience & Accessibility

- Every input field carries a visible label, not just a placeholder, so screen readers and quick scanning both work.
- Filter and category selections are keyboard-navigable chips rather than mouse-only controls.
- Color choices maintain contrast between text and background across both the volunteer and desk views.
- A dedicated Help route (`/help`) documents how to use the site for first-time volunteers.

## 7. Intelligent Feature: Guide

**Guide** is an offline question-and-answer helper built into the browser (`src/lib/guide.ts`). It answers operational questions such as which shifts are currently understaffed or whether a given ZIP code falls inside the service area — no internet connection or API key required.

The **Desk Board** is a second intelligent feature: rather than just listing open shifts, it actively ranks which shifts are most understaffed and surfaces specific people to call to fill them.

## 8. Input Validation

Validation in `src/lib/validate.ts` is checked at two levels, matching the rubric's distinction between format and meaning:

| Check | Example enforced in the demo |
|---|---|
| Format (syntax) | Phone number `704555` is rejected for not matching a valid phone format |
| Meaning (business rule) | ZIP `29708` is rejected because it falls outside Ridge Community Partners' service area, while `28211` is accepted |
| Meaning (business rule) | A volunteer must be 18+ to claim the pantry delivery route; a 17-year-old is refused |
| Meaning (business rule) | Hours can only be logged against a shift that has already occurred; logging tomorrow's shift in advance is refused |

## 9. Data & Reporting

All state lives in typed arrays — `volunteers[]`, `shifts[]`, `signups[]`, and `hours[]` — defined in `src/data/seed.ts`. The Reports screen (`src/lib/reporting.ts`) lets a leader sort by hours, name, or goal progress, filter by program (e.g., Pantry), and copy the resulting table as CSV.

## 10. Testing

Type safety:

```bash
npx tsc -b
```

Manual test path used to confirm behavior end-to-end:

- [x] ZIP `29708` is blocked at join.
- [x] ZIP `28211` is accepted and completes the join flow.
- [x] A full shift correctly waitlists rather than overbooking.
- [x] A 17-year-old is blocked from the 18+ delivery route.
- [x] Logging hours before a shift has occurred is refused.
- [x] The CSV export copies correctly from the Reports screen.

## 11. Rubric Alignment

| Rating Sheet Item | Where Harbor Demonstrates It |
|---|---|
| Functionality & Relevance to Topic | Join → Shifts → Desk (roster, hours, reports) covers recruit, organize, manage, and monitor |
| Language Selection | TypeScript + React + Vite, explained in [Section 5](#5-architecture--code-organization) |
| Code Comments | File-header comments in `validate.ts`, `match.ts`, `shifts.ts`, and `seed.ts` |
| Modular / Programming Knowledge | Logic split by responsibility across `src/lib/` |
| User Experience & Accessibility | Labeled fields, keyboard-navigable filter chips, contrast, dedicated Help route |
| Intelligent Feature | Guide offline Q&A and the Desk match/ranking list |
| Input Validation | Two-tier format + business-rule validation in `validate.ts` |
| Output & Data Analysis | Sortable, filterable Reports screen with CSV export |
| Data Structures & Scope | `volunteers[]`, `shifts[]`, `signups[]`, `hours[]` arrays with module-scoped logic |

## 12. Attributions & Copyright

All third-party libraries used to build Harbor (React, TypeScript, Vite, and any additional packages) and notes on the specimen sample data are documented separately in [`ATTRIBUTIONS.md`](ATTRIBUTIONS.md), distributed alongside this document and the source code as required by the competitive event guidelines.

> Ridge Community Partners, its roster names, shift schedule, and ZIP-code service area are fictional and created for this demonstration. They are not drawn from a live nonprofit's data or from any existing volunteer-management product.
