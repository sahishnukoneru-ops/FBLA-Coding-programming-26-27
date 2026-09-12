# FBLA-Coding-programming-26-27
# Harbor

Volunteer desk for **Ridge Community Partners**, a specimen Charlotte nonprofit.

**Event:** FBLA Coding & Programming, 2026–2027  
**Topic:** Serving the Community: Nonprofit Volunteer Management  
**Owner:** Sahishnu Koneru (individual)

Harbor lets a nonprofit recruit volunteers, post service opportunities, fill seats without overbooking, log hours, and run a customizable hours report. Leaders get a desk board that ranks who to call when a shift is short. Volunteers get a public calendar and a Q&A helper named Guide.

Ridge Community Partners is a made-up agency for the demo. Names, hours, and seat counts are sample data. They are not copied from a live roster or from another volunteer product.

## Run

Needs Node 20 or newer.

```bash
export PATH="$HOME/.local/node/bin:$PATH"
cd ~/Desktop/harbor
npm install
npm run dev -- --host 127.0.0.1 --port 5174
```

Open **http://127.0.0.1:5174**

Anyone can also use the live site:

**https://nunu-harbor.netlify.app**

Guide on that link is offline (no API key). Refresh starts a new demo session.

Venue wifi may be dead. Build at home, then:

```bash
npm run build
npm run preview -- --host 127.0.0.1 --port 5174
```

The program is a static SPA. Guide runs in the browser. No API key. System fonts. Refresh starts a new demo session.

## Seven-minute demo

Numbers change if you claim seats; the path does not.

1. **Home** — Ridge Community Partners, open seats, most-short shift.
2. **Shifts** — Saturday pantry has seats. Saturday reading buddies is **Full** (waitlist). Filter `tutoring`.
3. **Join** — bad phone `704555` is blocked. ZIP **29708** is blocked. ZIP **28211** is in. Good name `Sahishnu Koneru`, age 17, pick Tutoring.
4. Claim pantry. Try the **18+** pantry delivery route — age 17 is refused.
5. **Guide** — “What’s understaffed?” then “Do you cover 28209?”
6. **Desk → Board** — short shifts with named people to call. Place one.
7. **Hours** — log Priya on **Wednesday community supper** (already happened). Try logging tomorrow’s pantry — refused.
8. **Reports** — sort Hours / Name / Goal, filter Pantry, Copy CSV.

Sign-in shortcut: `priya.rao@example.com`

## Where the code lives

| File | Job |
|---|---|
| `src/data/org.ts` | Agency, ZIPs, skills |
| `src/data/seed.ts` | Roster, shifts, signups, hour logs (arrays) |
| `src/lib/validate.ts` | Name, email, phone, ZIP, age, hours (syntax and meaning) |
| `src/lib/volunteers.ts` | Join, sign in, activate |
| `src/lib/shifts.ts` | Claim, waitlist, overlap, promote |
| `src/lib/hours.ts` | Log hours against a finished shift |
| `src/lib/match.ts` | Who to call when a shift is short |
| `src/lib/reporting.ts` | Sortable report + CSV |
| `src/lib/guide.ts` | Offline Q&A |

Volunteer routes: `/` `/opportunities` `/join` `/me` `/help`  
Desk routes: `/desk` `/desk/roster` `/desk/schedule` `/desk/hours` `/desk/reports`

## Language choice

TypeScript so a shift, a volunteer, and an hour log cannot be mixed up at compile time. React so the volunteer calendar and the leader desk share one in-memory store. Vite so the program runs standalone in a browser without a database.

## Rubric map

| What they score | Where it is |
|---|---|
| Topic: recruit, organize, manage, monitor | Join → Shifts → Desk roster / hours / reports |
| Language selection | This README; TypeScript + React |
| Comments | File headers on `validate.ts`, `match.ts`, `shifts.ts`, `seed.ts` |
| Modular design | `src/lib/` split by job |
| UX / accessibility | Labels on fields, keyboard chips, contrast, Help |
| Intelligent feature | Guide Q&A; Desk match list |
| Input validation | Syntax in `validate.ts`; meaning in claim and hour log |
| Customizable report | Reports: sort, program filter, CSV, print |
| Arrays / lists | `volunteers[]`, `shifts[]`, `signups[]`, `hours[]` |

## Testing

```bash
npx tsc -b
```

Manual: 29708 blocked → 28211 joins → full shift waitlists → age 17 cannot drive → hours before the shift refused → CSV copies.

## Attributions

Libraries and specimen-data notes: **[ATTRIBUTIONS.md](ATTRIBUTIONS.md)**.
