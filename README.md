# Paradise Cutz

A modern barber shop landing page with an appointment request workflow.

## Project structure

```text
paradise-cutz-project/
├── index.html
├── README.md
├── css/
│   └── styles.css
├── js/
│   └── script.js
├── backend/
│   └── Code.gs
└── img/
    └── barbers/
```

## Front-end implementation

- responsive landing page
- barber cards and service information
- date selection with a minimum date
- dynamic time-slot rendering and demo availability logic
- booking form that submits a JSON payload
- demo fallback to `mailto:` when the Apps Script URL is still a placeholder

## Backend implementation

The Google Apps Script backend stores bookings in a `Bookings` sheet and sends action links to the barber via email. The backend supports:

- `doPost(e)` for creating bookings
- `doGet(e)` for confirm/decline/cancel actions
- token validation and reservation status checks
- customer and barber email notifications

## Local demo

Open the site in a browser using a local static server:

```bash
cd "e:\VS CODE\PROJECTS"
python -m http.server 8000
```

Then visit:

```text
http://localhost:8000
```

## Google Sheets reservation database

This project uses a free Google Sheets database for reservation tracking. To turn it on, create a Google Cloud service account and share a spreadsheet with that service account email.

Set these environment variables before starting the backend:

```bash
GOOGLE_SHEET_ID=your_google_sheet_id
GOOGLE_SHEET_NAME=reservations
GOOGLE_SERVICE_ACCOUNT_FILE=C:/path/to/your-service-account.json
```

The reservation sheet stores:
- client name
- phone number
- date
- time
- barber
- service
- status
- created timestamp

Reservation logic:
- confirm: keep the booking record in the database
- decline: remove the slot record and free the time again
- no action before 2 hours before appointment: keep the record until the cutoff time is reached
- at 2 hours before the appointment, pending bookings are automatically deleted

## Production deployment checklist

Before going live, configure these values in a real `.env` file:

```env
PUBLIC_BASE_URL=https://your-domain.com
ALLOWED_ORIGINS=https://your-domain.com,https://www.your-domain.com
GOOGLE_SHEET_ID=your_google_sheet_id
GOOGLE_SHEET_NAME=reservations
GOOGLE_SERVICE_ACCOUNT_FILE=/absolute/path/to/service-account.json
SMTP_HOST=smtp.abv.bg
SMTP_PORT=587
SMTP_USER=paradise_cutz2026@abv.bg
SMTP_PASSWORD=your_smtp_password
BARBER_ACTION_EMAIL=paradise_cutz2026@abv.bg
```

Then deploy the FastAPI backend to a public host such as Render, Railway, Fly.io, or a VPS, and serve the static front-end via the same domain or a CDN.

Important production requirements:
- use HTTPS only
- set the live domain in `PUBLIC_BASE_URL`
- add the live domain to `ALLOWED_ORIGINS`
- keep the Google service account JSON file secured and never committed to source control
- use real SMTP credentials for email delivery

## Notes

The booking availability is intentionally simulated in the front end to match the prototype described in the project specification. The reservation database provides persistence for the real booking flow while the UI remains a front-end demo.
