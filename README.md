# GovSync

GovSync is a prototype citizen-services portal for submitting, validating, and tracking government-service applications in one place.

It demonstrates two connected workflows:

- **Residence / Domicile Certificate** applications
- **Scholarship** applications

The project includes separate citizen and administrator experiences, consent-based data sharing, server-side validation, application tracking, decision history, English/Hindi/Marathi translations, and a local Cloudflare D1 database.

> **Prototype notice:** GovSync uses fictional records and illustrative rules. It does not verify real identities, connect to live government systems, send OTPs or messages, issue real certificates, or process scholarship payments.

## What you can explore

### Citizen experience

- Create an account with a mobile number and password
- Save a one-time recovery code
- Select a service and grant department permissions
- Review normalized information from fictional department sources
- Submit, track, clarify, or revoke an application
- View notifications and a clearly marked demonstration certificate
- Switch between English, Hindi, and Marathi

### Administrator experience

- Review submitted applications in a queue
- Inspect source records and normalized field mappings
- Approve, reject, or request clarification
- Record a decision note and show the result in the citizen's history

### Demonstration scenarios

The sample citizens are fictional:

| Citizen | Scholarship scenario |
| --- | --- |
| Asha Sharma | Eligible demonstration case |
| Rohan Patil | Income exceeds the demonstration limit |
| Meera Joshi | Conflicting dates of birth |

The Residence flow also includes optional address-mismatch and department-outage scenarios so the validation and recovery behavior can be demonstrated.

## Run locally

### Requirements

- Node.js 22.13 or newer
- npm

### Setup

```sh
npm run install:ci
node scripts/setup-local.mjs
node scripts/create-admin.mjs
npm run dev -- --hostname 127.0.0.1
```

Open the local URL printed by the development server, normally `http://127.0.0.1:5173`.

The administrator setup asks for a display name, a 10-digit mobile number, and a password of at least 12 characters. It writes local credential files that are ignored by Git. For a disposable demonstration administrator, use:

```sh
node scripts/create-admin.mjs --generate-demo
```

Read `.private/ADMIN-LOGIN.txt` for the generated login. Never publish `.private/`, `.dev.vars`, or any other environment file.

If PowerShell blocks `npm.ps1`, use `npm.cmd` instead.

## Try the main flow

1. Create a citizen account, or choose a sample citizen from the login screen.
2. Open **Services** and start a Residence or Scholarship application.
3. Grant the requested permissions and review the fictional source information.
4. Validate the application, confirm the declaration, and submit it.
5. Sign out and use **Administration Login** in a separate browser profile.
6. Review the application and approve, reject, or request clarification.
7. Return to the citizen account to view the decision, notification, and tracking history.

For the full walkthrough, see [DEMO-GUIDE.md](DEMO-GUIDE.md).

## Architecture

```text
Citizen portal
    │
    ├── consent and permissions
    ├── fictional department sources
    └── application form
            │
            ▼
GovSync API → normalization and validation → Cloudflare D1
            │
            ▼
Administrator review → decision and audit history → citizen tracking
```

The application is built with:

- React 19 and TypeScript
- Next-compatible routing through Vinext and Vite
- Cloudflare Workers runtime and D1
- Drizzle ORM and SQL migrations
- Zod validation
- Tailwind CSS and reusable UI components

## Project structure

| Path | Purpose |
| --- | --- |
| `app/` | Pages, layouts, API routes, styling, translations, and application views |
| `components/` | Reusable UI components |
| `db/` | Database connection and schema |
| `drizzle/` | Versioned D1 migrations |
| `lib/` | Authentication, demo data, validation, and server helpers |
| `public/` | Browser-served images, icons, and static documentation |
| `scripts/` | Local setup, administrator provisioning, and test scripts |
| `hooks/` | Shared React hooks |
| `vendor/` | Vendored CSS and license information |

## Documentation

- [DEMO-GUIDE.md](DEMO-GUIDE.md) — detailed citizen and administrator walkthrough
- [AUTHENTICATION.md](AUTHENTICATION.md) — account model, sessions, recovery, roles, and deployment requirements
- [BACKEND-GUIDE.md](BACKEND-GUIDE.md) — workflows, API behavior, data mappings, and validation scenarios
- [CUSTOMIZATION.md](CUSTOMIZATION.md) — colors, branding, illustrations, layout, and translations

## Checks

With a local server and a generated demonstration administrator:

```sh
node scripts/test-auth-flow.mjs
node scripts/test-i18n.mjs
node scripts/test-validation.mjs
npm run lint
npm run build
```

The test scripts use fictional records in the local database. They do not target hosted URLs.

## Security and deployment boundaries

This repository is suitable for demonstrating the product concept, not for handling real citizen data. Before any real deployment, add verified identity and phone enrollment, administrator MFA, department-scoped permissions, operational account recovery, abuse monitoring, data retention controls, audit operations, and a formal security review.

Important limitations:

- Mobile numbers are login identifiers, not verified phone ownership.
- Sample-citizen login is enabled for demonstrations and should be disabled for a real deployment with `GOVSYNC_DEMO_LOGIN=off`.
- The administrator account is provisioned out-of-band; there is no public administrator registration.
- The certificate is demonstration-only.
- No email, SMS, Aadhaar, payment, or live government API integration is included.

## Before publishing this repository

Do not commit or upload:

- `.private/`
- `.dev.vars` or any `.env*` file
- `.wrangler/`
- `node_modules/`
- `dist/`, `.next/`, `out/`, or other generated build output
- local database files, exports, backups, or test data
- logs, coverage output, or editor/agent work directories

These paths are already covered by `.gitignore` where appropriate. Review the repository history as well: deleting a secret from the current tree does not remove it from old commits.

The tracked `.openai/hosting.json` contains deployment metadata rather than credentials. Remove the `.openai/` directory before publishing if this repository should not retain its original hosting project association. Keep it only when the deployment integration is intentionally part of the public project.

## License

No open-source license has been declared yet. Add a `LICENSE` file before accepting external contributions or communicating reuse permissions.
