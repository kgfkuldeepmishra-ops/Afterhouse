# AfterHours — browser website package

This project is a browser-first website. It is not an Android/iOS app and has no app-store build.

## What is included

- `index.html`: standalone browser prototype (community feed, local demo chat, report UI, accepted-chat phone display illustration).
- `backend/`: Express + TypeScript + Prisma + PostgreSQL development scaffold, including encrypted-at-rest phone/message fields, accepted-chat contact authorization, report submission, and report-scoped moderator review with audit events.

## Run the browser prototype immediately

1. Extract this ZIP.
2. Open `index.html` in Chrome, Firefox, Edge, or Safari.
3. This is a local-only demonstration. Posts, messages, sign-in, and reports do not reach other users or a server.

## Run the API development scaffold

Requirements: Node.js 20+, npm, Docker.

1. Open a terminal in `backend/`.
2. Copy `.env.example` to `.env`.
3. Set strong, unique development secrets. Generate them with:
   - `openssl rand -base64 32` for `PHONE_ENCRYPTION_KEY` (must decode to 32 bytes)
   - `openssl rand -hex 32` for `JWT_SECRET`
   - `openssl rand -hex 32` for `PHONE_LOOKUP_HMAC_KEY`
4. For local-only testing, set `ALLOW_DEV_AUTH=true`. Never use the dev auth routes on a public site.
5. Start PostgreSQL: `docker compose up -d db`
6. Install and prepare: `npm install && npx prisma generate && npx prisma migrate dev --name initial`
7. Start API: `npm run dev`
8. In another terminal, serve the backend sign-in demo: `python3 -m http.server 5173 --directory web`
9. Open `http://localhost:5173`.

## Not production-ready — important

The included backend is deliberately a scaffold. It does **not** yet provide a complete public social network. In particular:

- Authentication is development-only; there is no real SMS OTP provider or verified phone ownership.
- Conversation creation, invitation acceptance/decline, inbox listing, and message-history retrieval are not implemented.
- File upload/storage/scanning is not implemented.
- A production moderator console, rate limits, abuse controls, backups, retention/deletion workflows, and incident response are not implemented.
- The standalone `index.html` prototype is not connected to the API.

Do not expose this scaffold to the public internet as a live service. Before launch, implement and independently security-test the missing features, use HTTPS, secure HttpOnly cookies, strict origin allowlists, private object storage, malware scanning, short-lived signed download URLs, rate limiting, age assurance, privacy notices, retention/deletion rules, and qualified India-specific legal review.

## Privacy and moderation model

Phone numbers should never appear on public profiles. The backend contact endpoint only returns the other participant's number to a participant in an `ACCEPTED` one-to-one conversation. Message and phone data are encrypted at rest in the scaffold. This is **not end-to-end encryption**: the server can decrypt a message specifically attached to a user report, and that limitation must be clearly disclosed. Moderator review is intended to be report-scoped and audit-logged, not blanket browsing of private chats.

## Deployment

A public website needs a hosting provider, a domain (optional but recommended), a managed PostgreSQL database, HTTPS, configured environment secrets, and a production authentication provider. None of those accounts or credentials are included in this ZIP. Once those are configured and the missing production features are implemented, deploy the static website and API as browser-accessible web services.
