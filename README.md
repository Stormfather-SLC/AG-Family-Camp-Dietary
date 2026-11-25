# AG Family Camp Dietary

A pilot web form where each BYU Aspen Grove Family Camp group's organizer lists the guests with food allergies, intolerances, or dietary restrictions, and anyone with a dietary preference (vegetarian, vegan, halal, kosher, and so on). Staff get one weekly printout for the kitchen and the dining hall.

**Status:** Pilot. Built Nov 9, 2026, alongside [AG Conference Reservation](https://github.com/Stormfather-SLC/AG-Conference-Reservation), whose patterns it reuses.

- **Live page:** https://claude.ai/artifact/1AMXwYNZtpJiycYB2Gz1iz
- **Owner:** Matt Rowe, Catering Supervisor, Aspen Grove

## Why a separate app

Family Camp groups only provide dietary information. Rooms, meals, and A/V don't apply, so this is a lightweight standalone page rather than a module of the conference form.

## What it does

### Organizers
- Open their group from the invitation link (`…#FC-XXXXX`) or by entering the group code. Only Aspen Grove starts groups.
- Group name, dates, and deadline are read-only; changes go through the office.
- Add each person with a dietary need or preference: name, optional family or household (choose an existing one or "+ New family or household"), one or more items (the nine major allergens, celiac disease, vegetarian, vegan, halal, kosher), and how serious each one is. Anything else goes in the always-visible **Not listed above** box; comma-separated entries become separate items, each with its own seriousness (e.g. `strawberries, no pork`). Seriousness is optional so organizers aren't blocked by details they don't have. Celiac disease is always treated as strict.
- If no one has dietary needs or preferences, check a box instead.
- Lock in when done. After lock-in, changes need a call to the office.

### Staff
Staff view is simulated for the pilot: Alt+Shift+S, a link ending in `?staff`, or five taps on "Aspen Grove" in the header. It is not security; real sign-in comes with BYU hosting.

- **Groups:** start a group (name, organizer, email, camp week, deadline). The camp week is picked from the Saturday check-ins, May through September, for this summer and next; each week runs Saturday to Friday. The deadline defaults to 14 days before check-in. and get a ready-to-copy invitation email. See each group's status (Not started, In progress, Locked in, Past deadline) and number of people with needs or preferences, copy a reminder, reopen a locked group from its form, or delete a group.
- **Weekly printout:** one sheet per camp week (Saturday to Friday) for front and back of house.
  - Top, for the kitchen: guests with needs or preferences, counts by item with serious ones flagged, plate combinations (e.g. `DF + GF`), and a list of serious allergies and celiac.
  - Below, for the dining hall: names and restrictions by group and family.
  - Groups not yet locked in are flagged. Download the sheet, open it, and print. On the GitHub Pages demo, Print works directly.

### Kitchen sheets (staff)

Two printable sheets, adapted from the Daily production list and Weekly meal schedule in [AG Conference Reservation](https://github.com/Stormfather-SLC/AG-Conference-Reservation). Family Camp groups don't choose meals, so these use one fixed menu for every camp week and show dietary counts only.

- **Daily production list:** pick a day. Each meal (breakfast, lunch, dinner) lists its menu items with a check box and a blank PAR line, the special plates for that meal, then a list of guests with special plates for the day and a look at tomorrow.
- **Weekly meal schedule:** a one-page summary of the week (groups on site, special plates, meals), the dietary accommodations by group, then one page per day.
- No snacks, and no total guest counts or "Serve" counts. Special plates assume every listed guest eats every meal.
- **The menu is temporary.** It's copied from the "TEMPLATE (Odd Weeks)" and "TEMPLATE (Even Weeks)" meal schedules while the chefs finalize the real one, and the sheets carry a "temporary menu" note. Odd and even weeks alternate from each season's Week 1 (the `WEEK1` list: May 30 in 2026, June 5 in 2027; add a line each year). To change the menu, edit `MENU_ODD` / `MENU_EVEN` in `index.html` (one entry per day, Saturday to Friday); set `MENU_TEMP` to `false` to remove the note.
- Print works directly on the GitHub Pages demo. Printing hides the page header and controls so only the sheet prints.

## Data and retention

- The form asks only for a name and the need or preference. No other personal information.
- Each group's record is deleted the day after it departs, matching Aspen Grove's current policy of keeping guest allergen information only through the guest's week. Deletion runs when staff open the Groups or Weekly printout tab.
- Demos use made-up data only. Real families' details never go in this repository; `.gitignore` blocks `*.json`.

## How saving works

`index.html` is one self-contained page with no build step.

- **Inside Claude (the live page):** data is stored in the artifact's shared database (collection `groups`, one document per group code). Changes save about half a second after each edit.
- **Opened directly in a browser:** practice mode; saves to that browser only.

### Record format (`groups/{code}`)

| Field | Meaning |
| --- | --- |
| `code` | `FC-` plus 5 characters |
| `name`, `organizer`, `email` | Set by staff |
| `arrive`, `depart`, `deadline` | `YYYY-MM-DD` |
| `status` | `open` or `locked`; `lockedAt` when locked |
| `noneNeeded` | Organizer said no one has dietary needs or preferences |
| `touched` | Organizer has saved at least once |
| `guests[]` | `{id, name, family, items:[{r, level, text?}]}`; `level` is `intol`, `mild`, `severe`, `air`, `pref`, or blank; `r` is `Other` for typed entries, one item per comma-separated entry, with `text` holding the entry |

## Demo (GitHub Pages)

The page runs as a stand-alone demo on GitHub Pages (`*.github.io`) or at any address ending in `?demo`. It never connects to the shared record and opens with four made-up sample groups in two July camp weeks of the next summer season (two locked in, one in progress, one not started). Staff can put the samples back with **Reset sample groups**, on the demo or the live page. Organizer sample: code `FC-DEMO3`. Staff view: add `&staff` (for example `?demo&staff`).

To turn it on: repository **Settings › Pages**, Source **Deploy from a branch**, branch `main`, folder `/ (root)`. The demo is then at `https://stormfather-slc.github.io/AG-Family-Camp-Dietary/`.

## Known limits (pilot)

- Organizers must be signed in to Claude, with the page shared to them, to save.
- Anyone the page is shared with can see every group. Access limits are future work.
- Invitation and reminder emails are copied and sent from the office's own email; nothing sends automatically.
- Deletion runs only when staff open the page. A scheduled job could make it fully automatic.
- `OFFICE_PHONE` near the top of the script is blank, so organizers see "call the Aspen Grove office".

## Open questions

- Who owns this on the operations side: dining hall lead, executive chef, or the reservations office?
- Does BYU privacy/risk agree that name plus restriction is compliant as planned?

## Later

Allergen tags on menu items, a guest-facing weekly menu, change requests after lock-in, and access limits.
