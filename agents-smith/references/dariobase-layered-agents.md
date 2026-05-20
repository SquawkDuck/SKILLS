# Dariobase Layered Agents Fallback

Use this reference when working in `/home/fabrizio/dev/dariobase` or another
Dariobase checkout where the layered `AGENTS.md` commit is not present. Prefer
local `AGENTS.md` files when they exist; otherwise use the relevant section
below and include it in delegated agent briefs.

## Root Working Rules

- Preserve the existing architecture and compatibility layers. Prefer local
  abstractions and established module patterns over new architecture.
- Keep edits narrow, especially in shared, legacy, or migration-heavy areas.
- Do not simplify old/new GAE, Firebase, Firestore, and UI boundaries unless
  the task is explicitly a migration.
- For backend/functions work, respect the central request pipeline: auth
  strategy, endpoint mode, write batch creation, commit behavior, logging,
  route versioning, and GAE compatibility are coupled.
- For frontend work, use `DCGlib`, `h(...)`, existing route/page patterns, and
  shared UI/schema-table components before creating local alternatives.
- If working primarily inside a subdirectory, check whether that subdirectory
  has its own `AGENTS.md` and follow it together with this root guide.
- Compatibility and business behavior matter more than stylistic
  modernization.

## Concern Map

- `web/`: React clients and shared web UI.
- `functions/`: Firebase Functions endpoint modules and request pipeline.
- `functions-lib/`: Shared Functions libraries, Firestore models, storage,
  mail, auth, and logging.
- `gae/`: Legacy Python 2 App Engine application.
- `gae2/`: Node.js App Engine bridge service.

## Web Client Guide

This section applies to work under `web/`, including the `admin`, `driver`,
`login`, `supplier`, and `_common` clients.

### Frontend Architecture

- Use `DCGlib` imports and the repo's `h(...)` React style.
- Prefer existing shared components under `web/_common/components/` before
  creating local alternatives.
- Use route wrappers such as `routePage` and `routeSupplierPage`; page state is
  keyed by `pageId`.
- Follow the established page-module shape where present:
  `actions.js`, `reducers.js`, `thunks.js`, `schema.js`, and `index.js`.
- Use `createActionsWithMeta({pageId}, ...)` for page actions that belong to a
  routed page.
- Keep async loading and mutation calls in thunks, context helpers, or API
  helpers, not in render bodies.

### UI And Data Flow

- Keep admin and supplier screens dense, table-driven, and operational.
- Keep driver screens mobile-first, simple, and task-focused.
- Use schema-table fields and shared table helpers for tabular screens.
- Use shared formatters from `DCGlib` for dates, times, currency, quantities,
  percentages, and ancestor paths.
- Use `web/_common/api/*` and `requestManager` helpers for HTTP calls.
- Keep explicit field mapping at API/UI boundaries. Do not hide old GAE field
  names, Firestore names, and UI names behind generic converters unless the
  task is specifically to migrate that boundary.
- Driver mutations often return partial update payloads such as `new_props`;
  apply those partials unless the existing flow refetches the whole page.

### Errors And Styling

- Use `showDialogs` helpers for modal/error flows. They handle detached/opened
  windows.
- In thunks, `showError()` followed by `throw error` is often intentional UX
  behavior, for example to keep dialogs or editors open.
- Treat shared JSS as global. `makeStyle()` rejects duplicate selectors.
- For Ant Design table style bugs, inspect cell-level selectors as well as
  row-level selectors.
- Keep `/mobile` routing, touch detection, and phone/tablet classification as
  separate concerns.

### Verification

- Use focused Vitest tests for isolated logic.
- Use Chromium/Playwright for visual, layout, and interaction bugs when
  available.
- If shared JSS changes do not appear in Chromium, rebuild the relevant web
  client before changing selectors again.

## Firebase Functions Guide

This section applies to work under `functions/`, including all function
codebases and endpoint modules.

### Endpoint Modules

- Endpoint modules export `{endpoints, timeoutSeconds, memory}`.
- Preserve the tuple style:
  `['get_', 'read', '/path', '', 'FirebaseSession', GROUP, handler]`.
- Keep endpoint paths in reverse alphabetical order.
- Keep `/GET-EXTERNAL-IP-ADDRESS` and `/LIST-ALL-ENDPOINTS` last, in that
  order.
- Use 4-character method and mode values such as `get_`, `post`, `read`, and
  `writ`.
- Endpoint mode is functional. `writ` creates `request.writeBatch`; handlers
  that return `commit: true` need write permission.
- Preserve route matching guardrails and old non-versioned routes. Frozen GAE
  callers may still depend on them.

### Request Pipeline

- Auth strategy names are meaningful and should not be swapped casually:
  `FirebaseSession`, `GAEdirectRequest`, `UserPassword`,
  `DeviceAuthTokenQuickLogin`, `public`, `emulatorAnonymous`, and related
  variants.
- Use the narrowest correct endpoint group from `endpoint-groups`.
- Every route handler must return `{commit, response}` with optional `status`,
  `contentType`, `attachmentFileName`, `setResponseCookie`, or
  `clearSessionCookie`.
- Use `request.database`, `request.writeBatch`, `request.storage`,
  `request.user`, `request.params`, `request.query`, and `request.body`.
- Use `request.runPostCommit` for work that must happen after a successful
  batch commit.
- Prefer existing domain exception classes, especially `DariobaseError`
  subclasses, when clients need serialized error data.

### Compatibility And Schedulers

- Preserve old GAE-compatible routes and newer Firebase-native variants such
  as `-fb` routes unless the task is explicitly a migration.
- Do not hand-roll GAE bridge calls. Use the existing helpers so bearer tokens
  and Dariobase user headers are handled consistently.
- Authentication is hybrid in places: Firebase login can still validate or
  update old GAE session state.
- Scheduler write jobs use a mock request so Firestore logging can work.
  Preserve that shape for logged scheduler writes.

### Testing

- Add focused contract tests under `functions-lib/generic/api` or the relevant
  shared library when changing endpoint behavior.
- Test endpoint tuple handlers and model/structure methods directly where
  practical.

## Functions-Lib Guide

This section applies to work under `functions-lib/`, especially shared Firebase
Functions libraries, Firestore models, storage, mail, auth, and logging.

### Firestore Model Layer

- Use `DocumentSetup`, `CollectionSetup`, `DocumentSchema`, and field classes.
- Direct Firestore create/update/delete is disabled by default; use batch and
  `structure` methods.
- Every document setup must define `userData.shouldLogAfterWriting`.
- If logging is enabled, preserve `makeLogGroup`, `serializeLog`, and batch
  metadata behavior.
- Native document access must be exposed through structure methods such as
  `getSnapshot()`.
- Keep create, edit, delete, serialize, and cache-update behavior on document
  or collection `structure` methods.
- Tests often call `setup.__structure.method(...)`; keep structure methods
  testable without full Firebase boot.

### Business Rules And Legacy Data

- Put mutation guards in model structure methods where practical: reviewed
  records, archived run instances, supplier changes, date thresholds,
  submitted/completed slip constraints, and related invariants.
- Use authorization caches where existing model methods pass them, especially
  distribution run instance and stop editing.
- Keep obsolete fields in schemas when old documents may still contain them.
- Expect stale historical references to deleted products, vehicles, partners,
  runs, and legacy entities.
- Keep explicit old/new field mapping. Do not "clean up" GAE, Firestore, and
  UI names unless the task is specifically a migration.

### Logging, Cache, Storage, And Auth

- Firestore logging is part of the write pipeline. Do not bypass it unless the
  existing code deliberately uses `Dariobase_documentsToNotLog`.
- Some cache documents intentionally allow direct create and disable logging.
- Memory cache is per function instance and can be stale. Updating it is useful
  but is not global invalidation.
- GCS object paths deliberately avoid leading `/`.
- Validate storage delete paths before deleting files.
- Use existing mail, storage, GAE bridge, authentication, and request helpers
  instead of hand-rolled equivalents.

### Testing

- Keep tests focused on endpoint/model contracts rather than broad integration
  flows.
- Newer tests may use the `Purpose / Scope / Guardrail` header style.
- Use shared test setup and custom matchers instead of recreating local
  utilities.

## Legacy GAE Guide

This section applies to work under `gae/`, the legacy Python 2 Google App
Engine application.

### Legacy Boundary

- Treat this application as frozen legacy unless the user explicitly asks for
  a GAE change.
- Prefer modern work in Firebase Functions, `functions-lib`, or `gae2` when
  possible.
- Preserve compatibility with existing Datastore entities and historical data.
- Do not rename or simplify legacy field names casually; many Firebase and UI
  flows still map to them explicitly.

### Flask, Auth, And Transactions

- GAE endpoints use Flask blueprints and read/write authorization decorators.
  Preserve endpoint group semantics.
- Write authorization creates a central transaction. Avoid nested write
  decorators or nested transaction assumptions.
- Transaction commits intentionally log first, then write/delete entities.
  Broken logs are preferred over unlogged data changes.
- Preserve `read_possibly_write_authorization_required` where a route may
  write conditionally.
- API bridge helpers in `gae/app/lib/firebase.py` attach bearer and Dariobase
  user headers. Use them instead of hand-rolled `urlfetch` calls.

### API Compatibility

- If touching `api8`, keep local Python GAE behavior aligned with the GAE2
  remote implementation.
- Old GAE callers may depend on non-versioned Firebase Function routes and
  old payload shapes.
- Keep explicit conversions between Datastore keys, urlsafe keys, numeric IDs,
  and Firebase/Firestore IDs.

### Testing And Safety

- Be explicit when a local workflow may talk to remote services.
- Keep changes as small as possible and test through the existing task/test
  harness when available.

## GAE2 Guide

This section applies to work under `gae2/`, the Node.js Google App Engine
service.

### Runtime And Style

- GAE2 is an ESM Node 22 App Engine service.
- Follow the existing Express style in `server.mjs`.
- Use the existing bearer-token authorization pattern.
- Remove temporary debug logs before finishing.

### Datastore And Compatibility

- GAE2 mostly operates on legacy Datastore and exposes API paths for the
  Firebase environment.
- Be explicit when local execution may use remote Datastore credentials.
- `api8` routes must stay aligned with the legacy Python GAE implementation
  where duplicated.
- Preserve response shapes expected by Firebase Functions and old GAE
  compatibility code.

### Operational Rules

- Keep changes narrow. This service is a bridge, not the place for broad
  migration refactors unless explicitly requested.
- Prefer existing Datastore and logging patterns over new wrappers.
