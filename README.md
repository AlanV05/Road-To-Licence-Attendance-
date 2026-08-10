# Road To Licence — Driving School Sign-In System

A full-stack lesson sign-in system built for a real freelance client who owns a local driving school. Students scan a QR code to clock in and out of lessons on their phone, and every session, payment status, and licence expiry gets tracked automatically in a live Google Sheet the owner checks day-to-day.

**(**Note:** the live sign-in page isn't linked publicly here, since it writes directly to a real client's production spreadsheet with no authentication layer (by design, so students can sign in instantly). Screenshots below show the app in action.)**

<img width="593" height="314" alt="image" src="https://github.com/user-attachments/assets/4d0ac0fd-1ae9-4df1-89c7-f10256749335" />
<img width="561" height="295" alt="image" src="https://github.com/user-attachments/assets/d23d0b60-aa24-44bc-98c5-0af76b5b1d54" />
<img width="690" height="366" alt="image" src="https://github.com/user-attachments/assets/e44ec714-e5aa-4ba4-8e20-35e84fe3b2a9" />
<img width="656" height="361" alt="image" src="https://github.com/user-attachments/assets/9154c7fe-a05f-447c-9918-285324a8586c" />



## Overview

Traditional lesson sign-in for a small driving school usually means paper logs or manual spreadsheet entry — slow, error-prone, and easy to lose track of. This system replaces that with a simple, mobile-first web page students use themselves, backed entirely by free infrastructure with no server or database to host.

- **Frontend:** mobile-first HTML/CSS/JavaScript, hosted on GitHub Pages
- **Backend:** Google Apps Script, deployed as a web app
- **Database:** Google Sheets — the business owner's account, familiar tool

No accounts, no app installs, no infrastructure costs.

## Screenshots

| Clock In | Clock Out |
|---|---|
| <img width="593" height="314" alt="image" src="https://github.com/user-attachments/assets/4d0ac0fd-1ae9-4df1-89c7-f10256749335" />|<img width="561" height="295" alt="image" src="https://github.com/user-attachments/assets/d23d0b60-aa24-44bc-98c5-0af76b5b1d54" />  |

| Dashboard | Individual Student Tab |
|---|---|
| <img width="690" height="366" alt="image" src="https://github.com/user-attachments/assets/e44ec714-e5aa-4ba4-8e20-35e84fe3b2a9" /> | <img width="656" height="361" alt="image" src="https://github.com/user-attachments/assets/9154c7fe-a05f-447c-9918-285324a8586c" /> |

## Features

- **QR-code driven sign-in** — students scan, fill in a short form, done
- **Automatic session tracking** — start/finish times, total hours calculated automatically
- **Payment status tracking** — Paid / Pending / Unpaid, colour-coded and counted per student on the Dashboard
- **Licence expiry alerts** — expired licences are automatically flagged in red with a warning marker, both on the Dashboard and the student's own tab
- **Duplicate-safe matching** — case-insensitive name matching prevents the same student accidentally creating multiple records
- **Backend validation** — rejects clock-outs with no matching clock-in, and rejects clock-out times that occur before the corresponding clock-in
- **Phone number validation** — enforced client-side and server-side
- **Per-student history tabs** — automatically created and formatted the first time a student signs in

## Architecture

```
Student's phone
      │
      │  scans QR code
      ▼
GitHub Pages (static frontend)
      │
      │  fetch() POST request
      ▼
Google Apps Script (deployed as web app)
      │
      │  reads/writes
      ▼
Google Sheets (Dashboard + per-student tabs)
```

The frontend and backend communicate over a single `doPost` endpoint. The backend handles all validation, formatting, and calculation. The frontend only collects input and displays the result.

## A few technical highlights

**Timezone-independent date/time comparisons.** An early version of the clock-out validation logic compared date/time values read back from Google Sheets using JavaScript `Date` objects, which silently misbehaved depending on the Apps Script project's configured timezone versus the spreadsheet's actual timezone, occasionally causing valid clock-outs to be rejected as "before" the clock-in. The fix ditches `Date` objects entirely for this comparison: dates and times are parsed as plain text and converted into a single sortable number (`YYYYMMDDHHMM`), removing timezone ambiguity from the comparison altogether.

**Defensive backend validation.** Since the sign-in page has no login system by design (it needs to work instantly for any student), all validation happens server-side as well as client-side. Chronological ordering, duplicate-name matching, and phone number format are all re-checked in Apps Script regardless of what the frontend already validated.

**Unit-tested core logic.** Date parsing, time parsing, the sortable-number comparison, total-hours calculation, phone validation, and case-insensitive name matching are all covered by a small unit test suite (`runUnitTests()` in `Code.gs`), run directly from the Apps Script editor.

## Tech stack

| Layer | Technology |
|---|---|
| Frontend | HTML, CSS, vanilla JavaScript |
| Hosting | GitHub Pages |
| Backend | Google Apps Script |
| Database | Google Sheets |

No frameworks, no build step, no dependencies, deliberately kept simple so a non-technical client can be handed a working system with minimal moving parts to maintain.


## Testing

Run `runUnitTests()` from the Apps Script function dropdown to execute the unit test suite. Results are printed to the execution log with a pass/fail summary and details on any failures.

## Project context

Built as a freelance project for Road To Licence Driving School from given initial requirements through to deployment, debugging, and non-technical client documentation. This was the first project I managed fully end-to-end for a real client, including UX decisions, ongoing feature requests, and a written handoff guide for a non-technical business owner.

## Author

**Alan Verghese**
[LinkedIn](https://www.linkedin.com/in/alan-verghese-8150232b5) · [GitHub](https://github.com/AlanV05)
