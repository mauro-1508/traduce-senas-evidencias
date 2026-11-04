<div align="center">

**Traduce Señas: Technical Manual**

Juan Mauricio Suaza Solórzano

Luis Alejandro Duarte Aldana

Juan Camilo Fierro Esquivel

Instructor: Karol Daniela Correa

November 4, 2026

</div>

## Table of Contents

- [Introduction](#introduction)
- [Purpose](#purpose)
- [1. System Requirements](#1-system-requirements)
- [2. Development Tools](#2-development-tools)
- [3. Architecture](#3-architecture)
   - [3.1 Backend Services](#31-backend-services)
   - [3.2 API Routes](#32-api-routes)
- [4. Database](#4-database)
   - [4.1 One Database per Service](#41-one-database-per-service)
   - [4.2 Current SQL Migrations](#42-current-sql-migrations)
   - [4.3 Retention and Data Deletion](#43-retention-and-data-deletion)
   - [4.4 Mismatches With the Documentation](#44-mismatches-with-the-documentation)
- [5. Security](#5-security)
   - [5.1 Authentication](#51-authentication)
   - [5.2 Email Verification and Recovery](#52-email-verification-and-recovery)
   - [5.3 Authorization (RBAC)](#53-authorization-rbac)
   - [5.4 Open Risks](#54-open-risks)
- [6. Sign Recognition (AI)](#6-sign-recognition-ai)
- [7. Frontend](#7-frontend)
- [8. Diagrams](#8-diagrams)
- [9. Tests](#9-tests)
- [10. Installation](#10-installation)
   - [10.1 Local Installation (Summary)](#101-local-installation-summary)
   - [10.2 Installation on a Web Server](#102-installation-on-a-web-server)
- [11. Running the Application](#11-running-the-application)
- [12. Actual vs Documented](#12-actual-vs-documented)
- [13. Pending Items](#13-pending-items)
- [References](#references)

## Introduction

This manual describes the current version of the system: Backend `v1.4.0` (`main` branch, commit `a962982`) and Frontend `v1.3.1` (`main` branch, commit `a5e231d`). To install the system, see the [Installation manual](installation-manual-apa.md).

- `Backend@main:<path>` = file from the `main` branch of the Backend repository. It can be read without switching branches with `git show origin/main:<path>` (inside the Backend repository).
- `Frontend@main:<path>` = file from the `main` branch of the Frontend repository.

## Purpose

This manual explains to developers joining the project how Traduce Señas is built: tools, microservice architecture, API routes, database, security, sign recognition, frontend, diagrams, tests and installation.

## 1. System Requirements

See the Installation manual, section 2.1.

## 2. Development Tools

**Table 1**

*Development Tools*

| Topic | Tools | Source |
|-------|-------|--------|
| Backend | Node.js 20, Express 5 and TypeScript in every service and in the gateway. PostgreSQL 16 with `pg` (one database per service). MongoDB 7 with `mongodb` (gesture samples). RabbitMQ 3 with `amqplib` (events). `jsonwebtoken` for JWTs, `bcrypt` for passwords, `zod` to validate iam request bodies, `nodemailer` for email, `google-auth-library` for Google sign-in, `express-rate-limit` and `http-proxy-middleware` in the gateway. Tests with `node:test`. | `Backend@main:services/*/package.json`, `packages/shared/package.json`, `scripts/run-tests.js` |
| Infrastructure | Docker and Docker Compose (13 containers locally). Caddy 2 as the HTTPS proxy in production. Every Dockerfile defines a `HEALTHCHECK`. | `Backend@main:docker-compose.yml`, `docker-compose.prod.yml`, `deploy/Caddyfile`, `services/*/Dockerfile` |
| Frontend | Expo 57, React Native 0.86, react-native-web, React Navigation 6, axios, AsyncStorage and expo-secure-store for the token, expo-auth-session for Google, react-native-webview for Words mode on phones, TensorFlow.js 4 for the custom model, Jest 29 and Playwright 1.63. **No React Query.** | `Frontend@main:package.json` |

## 3. Architecture

**Table 2**

*Architecture*

| Topic | Status | Source |
|-------|--------|--------|
| Overall style | **Microservices**: one API gateway and five services (`iam`, `recognition`, `lexicon`, `analytics` and `profile`), each with its own process and database. | `Backend@main:docker-compose.yml` |
| Internal style | **Hexagonal** in each service: `domain`, `application`, `ports` (inbound and outbound) and `adapters` folders. `packages/shared` only shares configuration, JWT, HTTP middleware, event contracts and the event bus. | `Backend@main:services/*/src`, `packages/shared/src` |
| Communication | HTTP from the gateway to each service and between services through their internal names. Asynchronous events through the broker, on the `traduce.events` exchange, with a dead-letter queue for failed messages. | `Backend@main:packages/shared/src/events` |
| Data | One relational database per service (five in total) and one document collection in `recognition`. | `Backend@main:services/*/db`, `docker-compose.yml` |

```
Browser or app ──► api-gateway :8080 ──► iam-service :3001
                                     ├──► recognition-service :3002
                                     ├──► lexicon-service :3003
                                     ├──► analytics-service :3004
                                     └──► profile-service :3005
Services ──► events ──► broker (traduce.events exchange)
```

In production, Caddy receives requests over HTTPS and forwards `/api/*` to the gateway. Everything else goes to the exported web app. Services 3001 to 3005 are not published outside the internal Docker network.

### 3.1 Backend Services

**Table 3**

*Backend Services*

| Service | Internal port | Database | What it does | Prefixes the gateway serves |
|---------|---------------|----------|--------------|-----------------------------|
| `api-gateway` | 8080 | None | Single entry point: CORS with an allow list, rate limits per IP, optional token check, proxy and aggregated `/health`. | Every `/api` prefix |
| `iam-service` | 3001 | `iam` | Sign-up, sign-in, email verification, password recovery and change, Google and Facebook sign-in, account deletion, roles and permissions. | `/api/auth`, `/api/users`, `/api/iam` |
| `recognition-service` | 3002 | `recognition` and MongoDB | Saved translations, sign templates, gesture samples (administrators only) and training scripts that run outside the server. | `/api/translations`, `/api/recognition`, `/api/sign-templates`, `/api/samples` |
| `lexicon-service` | 3003 | `lexicon` | Sign catalog, alphabet and media (3D models and thumbnails). | `/api/lexicon` |
| `analytics-service` | 3004 | `analytics` | Usage events and reports aggregated by section. | `/api/analytics` |
| `profile-service` | 3005 | `profile` | Profile and preferences, achievements, daily challenges and points, notifications, and weekly and monthly reports by email to administrators. | `/api/profile`, `/api/notifications`, `/api/achievements` |

The gateway forwards each prefix to its service without reading the request body. It checks the token optionally: an expired or invalid token does not stop a request from reaching `/api/auth`, so people can sign in again. It applies a general limit per IP and a stricter one to the sensitive authentication routes (sign-in, sign-up, codes and recovery). It answers `404` with the common error format when a route does not exist.

The services communicate through events. These are the ones defined in the shared package:

- `iam` publishes `iam.UserRegistered` and `iam.UserDeleted`. It first writes them to an outbox table (`outbox_events`) in its own database, and a process publishes them afterwards. This way an event is not lost if the broker is unavailable at that moment.
- `recognition` publishes `recognition.TranslationProduced`.
- `profile` publishes `profile.AchievementUnlocked` and `profile.PreferencesChanged`.
- `recognition`, `analytics` and `profile` consume `iam.UserDeleted` to delete the account's data. Each one creates its queue when it starts.

### 3.2 API Routes

"Token" = header `Authorization: Bearer <token>`. The permission or role shown is the one the route checks; business errors follow the `{ success, code, message }` format.

**Table 4**

*API Routes*

| Method | Route | Token | Permission or role |
|--------|-------|-------|--------------------|
| POST | `/api/auth/register` | No | — |
| POST | `/api/auth/login` | No | — |
| POST | `/api/auth/verify-email` | No | — (requires the code **and** the password) |
| POST | `/api/auth/verify-code` | No | — |
| POST | `/api/auth/resend-verification` | No | — |
| POST | `/api/auth/forgot-password` | No | — |
| POST | `/api/auth/reset-password` | No | — |
| POST | `/api/auth/google` | No | — (asks for consent if the account is new) |
| POST | `/api/auth/facebook` | No | — (asks for consent if the account is new) |
| GET | `/api/users/me` | Yes | — |
| DELETE | `/api/users/me` | Yes | — (password, or an emailed code if the account has no password) |
| POST | `/api/users/me/delete-code` | Yes | — |
| POST | `/api/users/me/password` | Yes | — |
| GET | `/api/users/stats` | Yes | — |
| GET | `/api/users/admins` | Service token only | Internal directory permission |
| GET | `/api/iam/me/access` | Yes | — (returns my roles and permissions) |
| GET | `/api/iam/roles` | Yes | `users.manage` |
| POST | `/api/iam/users/:userId/roles` | Yes | `users.manage` |
| DELETE | `/api/iam/users/:userId/roles/:roleName` | Yes | `users.manage` |
| DELETE | `/api/iam/roles/:roleId` | Yes | `users.manage` |
| POST | `/api/translations` | Yes | — |
| GET | `/api/translations/history` | Yes | — |
| GET | `/api/translations/me/stats` | Yes | — |
| GET | `/api/translations/stats` | Yes | — (the route does not require a role; see 5.4) |
| DELETE | `/api/translations/:id` | Yes | — |
| GET | `/api/sign-templates` | No | — (public on purpose) |
| POST | `/api/sign-templates` | Yes | `models.manage` |
| DELETE | `/api/sign-templates/:id` | Yes | `models.manage` |
| GET and POST | `/api/samples` | Yes | ADMIN role |
| GET | `/api/lexicon`, `/search`, `/alphabet`, `/categories`, `/:code` | No | — |
| POST, PATCH, DELETE and publishing under `/api/lexicon` (categories, signs, localizations and resources) | Yes | ADMIN role |
| POST | `/api/analytics/events` | Yes | — |
| GET | `/api/analytics/summary`, `/top-signs`, `/daily` | Yes | ADMIN role |
| GET | `/api/analytics/reports/sections` | Yes | `stats.read` |
| GET and PATCH | `/api/profile/me` | Yes | — (changing `notificationsEnabled` requires `notifications.manage`) |
| GET and PATCH | `/api/profile/preferences` | Yes | — (same case as `/me`) |
| GET | `/api/profile/challenges/today` | Yes | — |
| GET | `/api/profile/challenges/rewards/summary` | Yes | — |
| GET | `/api/achievements` | Yes | — |
| POST | `/api/achievements/activity` | Yes | — |
| GET | `/api/notifications` | Yes | — |
| PATCH | `/api/notifications/:id/read` | Yes | — |
| GET | `/health` and `/api/health` | No | — (aggregated status of the services) |

Notes:

- The `iam` routes that change roles check `users.manage` inside the use case.
- The OpenAPI contracts for each service are in `docs/07-api/contracts/openapi`. They have not been checked one by one against the code in this manual.
- `profile` calls `GET /api/users/admins` with its own service token; no user receives it.

## 4. Database

### 4.1 One Database per Service

Each service owns its database. There are no shared tables and no queries between services: when a service needs data from another, it asks over internal HTTP or receives it through events. In total there are **33 tables** across the five relational databases and **one collection** in the document database of `recognition`, where gesture samples are stored.

### 4.2 Current SQL Migrations

The migrations are in `services/<service>/db/`. The compose file mounts them by name in `/docker-entrypoint-initdb.d` of each database container. PostgreSQL runs them in order **only when the volume is created for the first time**.

**Table 5**

*Migrations Mounted When the Volume Is Created*

| Service | Mounted files |
|---------|---------------|
| `iam` | `000_users`, `003_iam_roles_permissions`, `005_auth_email_normalization_user_tokens`, `006_users_status_email_verification`, `009_outbox_events`, `010_users_password_nullable`, `011_account_deletion_token`, `012_outbox_claim_token`, `013_users_terms_version` |
| `recognition` | `001_translations`, `002_sign_templates`, `003_erased_users`, `004_sign_templates_word_kind`, `005_translation_retention` |
| `lexicon` | `003_lexicon` (includes the alphabet seed) |
| `analytics` | `001_usage_event`, `002_erased_users` |
| `profile` | `001_profile` (includes the achievements), `002_erased_users`, `003_ui_languages`, `004_achievement_catalog`, `005_obtainable_achievements`, `006_translation_milestones`, `007_functional_achievements`, `008_admin_reports`, `009_daily_challenges`, `010_challenge_reward_achievements` |

A new file does nothing until its line is added to the `volumes` section of the matching database service in `docker-compose.yml`. To apply changes to a database that already exists, see the Installation manual, section 1.2.

The `master.xml` files and Liquibase changelogs found in `analytics-service`, `iam-service`, `lexicon-service` and `recognition-service` are not run by Docker Compose. They no longer reproduce the current schema; the `.sql` files are the source of truth.

### 4.3 Retention and Data Deletion

- **Translations.** `recognition` really deletes the translations older than the retention period. The period is set with `RECOGNITION_TRANSLATION_RETENTION_DAYS`; with `0`, nothing is deleted. Before deleting, daily and per-word counts move to `translation_daily_counts` and `translation_user_words`, so the statistics keep their history.
- **Account.** When an account is deleted, `iam` removes the user and its codes, and leaves `iam.UserDeleted` in its outbox table. Each consuming service deletes its own data for that user and stores the identifier in `erased_users`. This way a late event does not recreate data for a deleted account.
- **Last administrator.** The account of the only ADMIN cannot be deleted (code `LAST_ADMIN`).

### 4.4 Mismatches With the Documentation

- The reference data model in `docs/06-data` may not match the migrations. The migrations are the source of truth.
- The documented migration strategy (`node-pg-migrate`) is not used. The migrations are hand-written SQL, applied by Docker Compose or by hand on an existing database.
- ADR-003 describes PostgreSQL and MongoDB as one store. The current per-service separation matches ADR-008.

## 5. Security

### 5.1 Authentication

- **JWT** signed with the shared secret `JWT_SECRET`. `iam` issues it at sign-in, and the other services verify it with the same secret. The secret must have at least 32 characters: startup fails if it does not.
- The token carries the user identifier (`sub`), the email, the roles and the permissions. It expires after a fixed period and **there is no renewal**: the API has no `refresh` or `logout`.
- The services check the signature, the issuer and the audience. They do not query `iam` on every request.
- **Passwords** with bcrypt (Provos & Mazières, 1999). Sign-in always compares against a hash: if the account does not exist it uses a fake hash, so the response time does not reveal whether the email exists. The account status (inactive or blocked) is shown only after the password has been checked.
- **Google and Facebook sign-in.** The backend verifies the provider's token before it creates or opens a session: for Google, by verifying the ID token; for Facebook, by checking the access token with the Graph API. If the account is new, the backend requires consent to the terms.

### 5.2 Email Verification and Recovery

- New account = `INACTIVE`. A **6-digit code** is sent, generated with `crypto.randomInt` and stored as a bcrypt hash. It expires after 15 minutes, allows 5 attempts, requires a 60-second wait between sends and allows 5 sends per hour.
- `verify-email` requires the code **and** the password. This way someone else cannot activate another person's account with only the code.
- Sign-up requires accepting the terms and records their version (`termsVersion`), as consent under Law 1581 of 2012.
- If the email cannot be sent, sign-up keeps the account and the user can request a new code.
- To delete an account without a password, a code is sent by email under its own token type.

### 5.3 Authorization (RBAC)

- There are three base roles: **USER**, **LINGUIST** and **ADMIN**, and 13 permissions (`packages/shared/src/security/permissions.ts`). Every new account receives USER at sign-up.
- Roles and permissions travel **inside the token**. A change takes effect only after the user signs in again, and after a role is removed the token keeps its permissions until it expires. To end all sessions at once, `JWT_SECRET` must be changed.
- `requirePermission` checks a permission in the token. `requireRole` checks a role in the token.
- Rules in `iam`: base roles cannot be deleted, an assigned role cannot be deleted, and the last ADMIN cannot lose its role (code `LAST_ADMIN`).
- The first ADMIN is assigned by hand with an SQL statement, because there is no endpoint for it. See the Administrator manual (`docs/14-training/admin-manual.md`), section 3.
- Gesture samples are biometric data: the whole `/api/samples` resource requires the ADMIN role.
- Analytics are stored as aggregates per section. The section report requires `stats.read`.

### 5.4 Open Risks

**Table 6**

*Open Risks*

| Risk | Severity | Details |
|------|----------|---------|
| Token still valid after the account is blocked | Medium | The shared middleware (`packages/shared/src/http/auth_middleware.ts`) only verifies the signature. A blocked account keeps its token until it expires. |
| Global translation statistics available to any account | Medium | `GET /api/translations/stats` requires only a token. It should require `stats.read` if global data should not be visible to every user. |
| No password strength rule on the server | Medium | The server only limits the size (72 bytes, the bcrypt limit). The rule of 8 characters and at least three of four types (uppercase, lowercase, number and symbol) exists only in the client (`passwordStrength.ts`). |
| Attempt limit only per IP | Low/Medium | The gateway limits by IP. A temporary lock after several failed attempts on one account has not been verified. |
| Token in web storage | Medium | On the web the token is kept in AsyncStorage, which uses `localStorage`: an injected script (XSS) can read it. On phones, secure storage is used. |
| Credentials in earlier branches | High | A plain-text password was found in documentation on an earlier branch. It is not copied into this manual. It must be rotated and removed from that branch. |
| Client legal texts not verified | Medium | The frontend privacy texts have not been compared with the real behavior. They are not reproduced here. |

No longer risks compared with earlier versions: validation of `JWT_SECRET` at startup, role control on templates (`models.manage`), the CORS allow list, the gateway's request limit, and account deletion, which does delete the data.

## 6. Sign Recognition (AI)

- **Recognition runs in the browser** (on the web and inside the WebView of the Android app). `recognition-service` only stores and distributes templates and samples: it does not infer (`services/recognition-service/README.md`).
- Vision engine: **MediaPipe Tasks Vision 0.10.14**, loaded from a CDN, with HandLandmarker and PoseLandmarker (Lugaresi et al., 2019; Zhang et al., 2020). It extracts 21 points per hand and normalizes them.
- Two modes in `TranslationScreen`. **Words** is the default mode. **Alphabet** is the second one.
  - **Words:** if the custom model exists (`public/models/lsc/`, TensorFlow.js, input of 30 frames by 128 values, and `glosas.json` with 54 words), it uses it (`wordEngine.web.ts`). Otherwise it uses the generic 7-gesture `GestureRecognizer`.
  - **Alphabet:** hand geometry, a KNN classifier (Cover & Hart, 1967) and temporal comparison with DTW (Sakoe & Chiba, 1978) against 16-frame templates. The templates are downloaded from `GET /api/sign-templates?kind=motion`.
- Two input formats coexist (16 frames by 126 values in DTW and 30 frames by 128 values in the TensorFlow.js model). They are not the same calculation path.
- **On phones:** Words mode opens a WebView that loads the web app in embedded mode (`?embed=word-engine`). Alphabet mode does not recognize signs with the camera on phones: it shows a notice.
- **Training outside the server** (`services/recognition-service/training/`): TypeScript scripts (`npm run ia:*`) to download and convert the LSC-54 dataset (Mora-Zarate et al., 2025) and to upload templates. The Python scripts and Colab notebooks train a Conv1D with GRU model and export it to TensorFlow.js (Google, n.d.). The resulting model is copied to `Frontend/public/models/lsc/`.
- **LSC-54 dataset license:** CC BY-NC 4.0 (non-commercial use, with citation). This conflicts with the open decision about a commercial fee.
- The model learned from people recorded in a studio and may fail with a different camera. The samples screen (`ModelSamplesScreen`, administrators only) records repetitions to readjust the model. **The accuracy of the current model is not measured** in this manual.

## 7. Frontend

Structure (`src` of the Frontend repository): `app/` (configuration, providers and routes), `feature/` (Admin, Alphabet, History, Profile, Stats, Translation and auth), `shared/` (components, constants, services, types and utilities), and `public/models/lsc/` (model).

- **Providers** (`App.tsx`): the session state is managed by `AuthProvider`, and the permissions by `AccessProvider`.
- **AccessContext** (`app/providers/AccessContext.tsx`): on sign-in it calls `GET /api/iam/me/access` and stores the roles and permissions. If the call fails, it denies everything and shows a warning. `adminMode` equals `users.manage`, `canStats` equals `stats.read`, and `canAdmin` equals `users.manage` or `models.manage`.
- **Navigation** (`MainTabNavigator.tsx`): with `adminMode`, Statistics, Admin and Profile are shown. Without it, Translation, Alphabet, History and Profile are shown. An administrator does not see the Translation tab.
- **HTTP client** (`shared/services/api.client.ts`): axios with the token in `expo-secure-store` (phones) or AsyncStorage (web). An interceptor adds `Authorization` only to calls to the app's own API and warns on a 401.
- **API address** (`app/config/api.config.ts`): by default it points to `https://traducesenas.tech/api`. The variable `EXPO_PUBLIC_API_URL` changes it (for example, `http://localhost:8080/api` for the local backend). The production web build uses the relative path `/api`.
- **i18n** (`app/config/i18n/locales/`): Spanish, English, French and Portuguese. The default language is Spanish.
- **`.web.ts` files:** Metro picks `*.web.ts` on the web (for example, `wordEngine.web.ts`).

Connection status of the screens with the backend:

**Table 7**

*Frontend Connection Status*

| Screen or function | Connected? |
|--------------------|------------|
| Sign-in, sign-up, verify email and resend code | Yes |
| Google and Facebook sign-in | Yes, if the platform has its credential configured. If it is missing, the button shows a notice. Google is not available in Expo Go. |
| Forgot password, code and new password | Yes (`forgot-password`, `verify-code` and `reset-password`) |
| Change password | Yes (`POST /api/users/me/password`). If the account has no password, it creates one with the code flow. |
| Delete account | Yes (`DELETE /api/users/me`, with an emailed code or the password) |
| Translation (save) and History (list and delete) | Yes. History requests up to 100 translations. |
| Permissions (`/api/iam/me/access`) | Yes |
| Sign templates (`/api/sign-templates`) | Yes |
| Daily challenges, achievements and notifications | Yes |
| Statistics | Yes (`translations/stats`, `users/stats` and `analytics/reports/sections`) |
| Role management (`/api/iam/roles` and related) | **There is no screen**: API only |

## 8. Diagrams

The current class diagrams are in `docs/08-uml/diagrams` (exported to SVG, with the PlantUML source in `source/`). The index is in `docs/08-uml/diagram-index.md`. The use case diagram is in the Software Analysis Documentation (evidence 02).

The class figures in the previous version of this manual described the monolith and have been removed. The MVP scope is sign-to-text or speech only.

## 9. Tests

- **Backend.** `npm test` builds `@traduce/shared` and runs every `*.test.ts` file under `services/*/tests` and `packages/shared/tests` with `node:test` (`scripts/run-tests.js`). Result on November 4, 2026: **918 tests pass, 0 fail and 13 are skipped** because they need PostgreSQL. Tests against a real database are enabled with the variables `PROFILE_TEST_DATABASE_URL`, `PROFILE_PREFERENCES_PG_TESTS` and `GAMIFICATION_PG_TESTS`.
- **Frontend.** `npm test` runs Jest, and the result on November 4, 2026 was **469 of 469**. `npm run typecheck` checks the types. `npm run e2e` runs Playwright over `e2e/` (authentication, alphabet and history) and needs the backend running.
- **CI.** The Backend repository has a GitHub Actions workflow (`.github/workflows/ci.yml`) that builds and tests every PR and every push to `main` and `develop`, with a disposable PostgreSQL database. The Frontend repository has another one that checks types, tests, the web bundle, the Docker image and the E2E tests against the `main` backend.

## 10. Installation

### 10.1 Local Installation (Summary)

See the [Installation manual](installation-manual-apa.md). In short, `docker-compose.yml` at the root of the Backend repository starts 13 containers: the gateway, the five services, the five PostgreSQL databases, MongoDB and RabbitMQ. No web service is in that compose file: the frontend runs with Expo and points to the gateway. Each service has a `HEALTHCHECK`, and the gateway starts when the five services respond. `GET /health` returns the aggregated status.

### 10.2 Installation on a Web Server

The file `docker-compose.prod.yml` is added to the base compose with `-f`. It removes the publication of port 8080, adds Caddy (ports 80 and 443, with automatic HTTPS through Let's Encrypt) and adds the `web` service, built from the Frontend repository (path `FRONTEND_DIR`, by default `../frontendTS`). In production, the gateway only receives traffic from Caddy and trusts one proxy (`TRUST_PROXY`) to count requests by the real client IP.

Requirements: the domain pointing to the server, and ports 80 and 443 open on the server and in the provider's panel.

Deployment status at `traducesenas.tech`: checked on November 4, 2026: HTTPS is active, HTTP redirects to HTTPS and `/api/health` answers `ok` with version 1.4.0 and all five services up.

## 11. Running the Application

See the Installation manual, sections 2.3 and 3.

## 12. Actual vs Documented

**Table 8**

*Actual vs Documented*

| Topic | Documented | Actual |
|-------|------------|--------|
| Architecture | Monolith in earlier documents; microservices as a template | Microservices: a gateway and five services, each with a hexagonal internal structure |
| Database | PostgreSQL and MongoDB in one store (ADR-003) | One database per service (ADR-008) and MongoDB for gesture samples |
| Recognition | MediaPipe with OpenCV; TensorFlow.js | MediaPipe with KNN and DTW, and a custom TensorFlow.js model. No OpenCV |
| Frontend | React Query | No React Query |
| JWT | RS256 in templates | HS256 with a shared secret, fixed expiry and no renewal |
| API | `/refresh`, `/logout` and `/jwks` in templates | They do not exist. Real routes in section 3.2 |
| Migrations | `node-pg-migrate` | Hand-written SQL per service. Liquibase is not run |
| Scope | Sign to text or speech | The same. The `translations` table also accepts `texto_sena` and `voz_sena`, unused in the MVP |
| Integration branch | `dev` (convention in the documents) | `main` is the current branch of Backend and Frontend |

## 13. Pending Items

1. Close sessions of blocked accounts: check the account status in the shared middleware or in the gateway (section 5.4).
2. Decide whether `GET /api/translations/stats` should require `stats.read`.
3. Add the password strength rule on the server too (sign-up and password change).
4. Create a role management screen for administrators (today there is only the API).
5. Remove or synchronize the Liquibase changelogs that no longer reproduce the schema.
6. Check the OpenAPI contracts in `docs/07-api/contracts/openapi` against the routes in section 3.2.
7. Align the reference model (`docs/06-data`) and ADR-003 with the migrations and with ADR-008.
8. Unify the two AI input formats and measure the model's accuracy.
9. Review and rotate the credentials that may remain in earlier branches.
10. Confirm the state of HTTPS and of ports 80 and 443 on the server: checked on November 4, 2026: HTTPS is active, HTTP redirects to HTTPS and `/api/health` answers `ok` with version 1.4.0 and all five services up.

## References

Cover, T., & Hart, P. (1967). Nearest neighbor pattern classification. *IEEE Transactions on Information Theory*, *13*(1), 21–27. https://doi.org/10.1109/TIT.1967.1053964

Expo. (n.d.). *Expo documentation*. Retrieved November 4, 2026, from https://docs.expo.dev/

Google. (n.d.). *TensorFlow.js*. Retrieved November 4, 2026, from https://www.tensorflow.org/js

Jones, M., Bradley, J., & Sakimura, N. (2015). *JSON Web Token (JWT)* (RFC 7519). Internet Engineering Task Force. https://doi.org/10.17487/RFC7519

Ley 1581 de 2012 [Law 1581 of 2012]. *Por la cual se dictan disposiciones generales para la protección de datos personales* [Issuing general provisions for the protection of personal data]. October 17, 2012. D.O. No. 48587.

Lugaresi, C., Tang, J., Nash, H., McClanahan, C., Uboweja, E., Hays, M., Zhang, F., Chang, C.-L., Yong, M. G., Lee, J., Chang, W.-T., Hua, W., Georg, M., & Grundmann, M. (2019). *MediaPipe: A framework for building perception pipelines* [Preprint]. arXiv. https://doi.org/10.48550/arXiv.1906.08172

Meta Platforms, Inc. (n.d.). *React Native*. Retrieved November 4, 2026, from https://reactnative.dev/docs/getting-started

Mora-Zarate, J. E., Garzón-Castro, C. L., & Castellanos Rivillas, J. A. (2025). LSC-54: A landmark-based dataset for Colombian Sign Language. *Data in Brief*, *63*, Article 112145. https://doi.org/10.1016/j.dib.2025.112145

OpenJS Foundation. (n.d.-a). *Express 5.x API reference*. Retrieved November 4, 2026, from https://expressjs.com/en/5x/api.html

OpenJS Foundation. (n.d.-b). *Node.js documentation*. Retrieved November 4, 2026, from https://nodejs.org/docs/latest/api/

Provos, N., & Mazières, D. (1999). A future-adaptable password scheme. In *Proceedings of the 1999 USENIX Annual Technical Conference*. USENIX Association.

Sakoe, H., & Chiba, S. (1978). Dynamic programming algorithm optimization for spoken word recognition. *IEEE Transactions on Acoustics, Speech, and Signal Processing*, *26*(1), 43–49. https://doi.org/10.1109/TASSP.1978.1163055

The PostgreSQL Global Development Group. (n.d.). *PostgreSQL 16 documentation*. Retrieved November 4, 2026, from https://www.postgresql.org/docs/16/

Zhang, F., Bazarevsky, V., Vakunov, A., Tkachenka, A., Sung, G., Chang, C.-L., & Grundmann, M. (2020). *MediaPipe Hands: On-device real-time hand tracking* [Preprint]. arXiv. https://doi.org/10.48550/arXiv.2006.10214
