# CHIRASWARA — Concert & Singing Competition Platform

A responsive Sinhala-first website and Node.js backend for Sudarshana Sri Dansing Academy. Includes persistent SQLite storage, admin authentication, registration approval, image uploads, competition rules versioning, category-level voting, voting schedule controls, CSV export, PDF reports, audit log, and optional SMTP notifications.

## Requirements
- Node.js 20+
- npm

## Run locally
1. Copy `.env.example` to `.env`.
2. Set `SESSION_SECRET` to a long random value, and set `ADMIN_EMAIL` / `ADMIN_PASSWORD` before first startup. The initial admin is created once from these variables; there is no public hardcoded admin password.
3. `npm install`
4. `npm start`
5. Open `http://localhost:3000`.

The database and uploaded images are stored in `data/` and `uploads/`. Back up both directories. Do not commit `.env`, the database, session database, or uploads to a public repository.

## Email notifications
Set `SMTP_HOST`, `SMTP_PORT`, `SMTP_SECURE`, `SMTP_USER`, `SMTP_PASS`, and `ADMIN_NOTIFICATION_EMAIL`. Use an email provider credential or Gmail app password; do not use a normal Gmail password. If SMTP is not configured, registrations are still saved and the admin dashboard reports the email status. Failed notifications can be retried from the dashboard.

## Sinhala PDF font
PDFKit uses a standard Latin font by default. For Sinhala text in PDF reports, provide a suitable Unicode Sinhala `.ttf` font file on the server and set `SINHALA_FONT_PATH` to its absolute path. Check font embedding and text shaping with your chosen font before production use.

## Voting model and important limitation
The server checks category availability and the configured start/end times for every vote submission. SQLite enforces `UNIQUE(category_id, voter_key)` so simultaneous requests using the same normalized email cannot create two votes in the same category. A voter can vote once in each category using the same email address. **This starter uses email-address-based limited verification; it does not send or verify a one-time code and cannot guarantee one vote per human.** Before a high-stakes public vote, add verified email/SMS OTP, abuse monitoring, and an operational review process. Do not advertise this as perfect one-person-one-vote enforcement.

## Production checklist
- Deploy behind HTTPS; set `NODE_ENV=production` and a strong `SESSION_SECRET`.
- Use a persistent disk/volume for SQLite and uploads, or migrate to managed database/object storage for multi-instance deployments.
- Restrict file-system permissions and configure automated encrypted backups.
- Configure SMTP and verify delivery, quotas, and bounce handling.
- Configure a Sinhala-capable font and verify the actual PDF output.
- Review privacy notices, consent language, retention policy, and local legal requirements.
- Consider a CSRF token strategy, a managed database, cloud object storage, verified voter OTP, and stronger monitoring before opening public voting.
- The `datetime-local` admin form uses the browser's local timezone when converting dates; keep the admin workstation set to Sri Lanka time (Asia/Colombo) or replace this with a timezone-aware date-time picker before deployment in other timezones.

## Routes overview
- `GET /api/public` — public categories, approved contestants, rules, hero image
- `POST /api/register` — contestant registration
- `GET /api/results` — category results / rankings
- `POST /api/vote` — one vote per email per category
- `POST /api/admin/login`, `POST /api/admin/logout`, `GET /api/admin/me`
- `GET /api/admin/dashboard` — protected dashboard data
- `PATCH /api/admin/registrations/:id` — approve/reject/archive
- `PATCH /api/admin/categories/:id` — update category
- `POST /api/admin/hero` — update hero image
- `PUT /api/admin/rules` — create a new rules version
- `PUT /api/admin/voting` — schedule and public results settings
- `GET /api/admin/export.csv`, `GET /api/admin/report.pdf` — protected exports
