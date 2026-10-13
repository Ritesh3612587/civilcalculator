# BuildScope AI --- Product and Technical Implementation Documentation

**Version:** 1.0\
**Date:** October 2026\
**Product type:** Civil engineering and construction SaaS\
**Initial market:** India, with an architecture that can support
international users later\
**Primary goal:** Build a reliable construction workflow platform that
starts with engineering calculators and grows into secure project
document chat, drawing quantity takeoff, quality checks, and paid team
workspaces.

------------------------------------------------------------------------

## 1. Executive Summary

BuildScope AI is a web application for civil engineers, quantity
surveyors, contractors, estimators, and construction teams. The product
will be delivered in five phases:

1.  **Public website and calculators:** marketing pages, tool directory,
    concrete calculator, steel calculator, beam calculator, pricing, and
    contact.
2.  **Accounts and projects:** registration, login, project workspaces,
    private PDF uploads, and document library.
3.  **AI document chat:** ask questions about project documents and
    receive answers with page-level citations and uncertainty handling.
4.  **Quantity takeoff:** calibrate drawing scale, measure lengths and
    areas, count objects, annotate plans, and export results.
5.  **Quality checks and SaaS billing:** discrepancy review, usage
    limits, subscriptions, team permissions, auditability, and
    enterprise options.

The strategy is to deliver useful, testable value at each phase. Do not
start with a fully autonomous AI construction engineer. Start with
deterministic calculators, then secure document management, then
retrieval-based AI, then measurement tools, and finally commercial
controls and advanced quality checks.

### Product principles

-   **Engineering transparency:** show inputs, units, assumptions,
    formulas, and limitations.
-   **Human review:** AI outputs are suggestions until reviewed by a
    qualified person.
-   **Traceability:** answers and takeoff items should link back to
    source documents or drawing locations.
-   **Security by default:** every private resource must be authorized
    on the server.
-   **Progressive delivery:** every phase should be deployable and
    usable independently.
-   **Cost awareness:** limit expensive AI and document-processing
    operations by plan and usage.
-   **No false precision:** show uncertainty and ask for clarification
    when the source is unclear.

------------------------------------------------------------------------

## 2. Scope and Non-Goals

### Included in the initial roadmap

-   Responsive marketing website and tool directory.
-   Concrete volume and material estimate calculator.
-   Steel bar weight calculator.
-   Basic simply supported beam analysis for clearly defined load cases.
-   Contact form and pricing presentation.
-   User authentication and account management.
-   Projects and secure PDF document library.
-   Document extraction, indexing, AI question answering, and
    source-page citations.
-   Manual drawing scale calibration and basic length, area, and count
    measurements.
-   Takeoff items, review status, and CSV/XLSX/PDF export.
-   Subscription plans, quotas, team roles, audit logs, and admin
    controls.

### Not included in the first release

-   Autonomous approval of structural designs.
-   Guaranteed interpretation of every scanned or handwritten drawing.
-   Automatic code compliance certification.
-   Full BIM authoring or replacement for CAD software.
-   Automatic legal or contractual interpretation.
-   Unreviewed AI-generated engineering decisions.
-   Payment processing before product value, security, and pricing have
    been validated.

------------------------------------------------------------------------

## 3. Users and Core Jobs-to-Be-Done

### Primary users

1.  **Civil engineering student:** wants quick calculations, unit
    conversions, and formula explanations.
2.  **Site engineer:** needs material estimates, saved calculations, and
    quick project references.
3.  **Quantity surveyor/estimator:** needs to measure drawings and
    prepare quantity schedules.
4.  **Small contractor:** needs faster tender estimates and a central
    place for project drawings.
5.  **Engineering consultancy:** needs team collaboration, document
    search, traceable outputs, and governance.
6.  **Enterprise construction organization:** needs access control,
    auditability, integrations, and custom capacity.

### Core user journeys

-   Visitor → calculator → results → save/share or create account.
-   Visitor → pricing → registration → project creation.
-   User → create project → upload PDF → extraction status → document
    chat.
-   User → drawing viewer → calibrate scale → measure → review takeoff →
    export.
-   Workspace owner → invite team → assign roles → choose plan → monitor
    usage.
-   Admin → inspect failed jobs, billing state, abuse reports, and audit
    events.

------------------------------------------------------------------------

## 4. Information Architecture

### Public routes

-   `/` --- Home
-   `/tools` --- Tool directory
-   `/tools/concrete-calculator`
-   `/tools/steel-weight-calculator`
-   `/tools/beam-calculator`
-   `/pricing`
-   `/contact`
-   `/about`
-   `/blog` --- optional after initial release
-   `/privacy`
-   `/terms`
-   `/acceptable-use`
-   `/engineering-disclaimer`

### Authentication routes

-   `/login`
-   `/register`
-   `/forgot-password`
-   `/reset-password`
-   `/verify-email`

### Authenticated application routes

-   `/app` --- workspace dashboard
-   `/app/projects`
-   `/app/projects/new`
-   `/app/projects/:projectId`
-   `/app/projects/:projectId/documents`
-   `/app/projects/:projectId/documents/:documentId`
-   `/app/projects/:projectId/chat`
-   `/app/projects/:projectId/takeoff`
-   `/app/projects/:projectId/quality-checks`
-   `/app/settings/profile`
-   `/app/settings/security`
-   `/app/settings/team`
-   `/app/settings/billing`
-   `/app/usage`

### Internal/admin routes

-   `/admin` --- restricted to authorized staff
-   `/admin/users`
-   `/admin/jobs`
-   `/admin/usage`
-   `/admin/audit`

Do not expose admin routes or data merely by hiding links. Every admin
API must verify authorization on the server.

------------------------------------------------------------------------

## 5. Recommended Technology Stack

Use a stack that is familiar enough for a small team and supports a
gradual move from calculators to document-heavy SaaS.

### Frontend

-   **Next.js with TypeScript** for routing, server-rendered public
    pages, and application UI.
-   **React** for reusable calculators, dashboard components, forms, and
    viewers.
-   **Tailwind CSS** for consistent responsive styling.
-   **Zod** for client/server input schemas where suitable.
-   **React Hook Form** for complex forms if needed.
-   **PDF.js** for browser-side PDF rendering.
-   **A charting/SVG layer** for beam diagrams and drawing measurement
    overlays.

### Backend

Choose one primary backend path rather than mixing frameworks without a
reason:

**Recommended MVP path:** Next.js route handlers/server actions for
ordinary application APIs, plus a separate worker for long-running
document jobs.

-   TypeScript API layer.
-   PostgreSQL relational database.
-   Object storage for PDFs and exports, using private buckets and
    signed URLs.
-   Background job queue for extraction, indexing, exports, and AI
    processing.
-   Redis-compatible queue/cache only when needed by the chosen
    deployment.
-   A vector index or PostgreSQL vector extension for document retrieval
    once semantic search is required.

### AI and document processing

-   Use a model provider behind an internal adapter so the provider can
    be changed.
-   Use text extraction first; OCR only when needed.
-   Store extracted text with page numbers and source offsets.
-   Chunk content with overlap and metadata.
-   Retrieve relevant chunks before generating answers.
-   Require source references in answers.
-   Keep model prompts, model names, token usage, latency, and failures
    observable.
-   Treat document contents as untrusted input; documents must never be
    allowed to override system instructions or trigger unauthorized
    actions.

### Payments

Integrate a payment provider that supports the business's target
geography, recurring payments, taxes, webhooks, refunds, and
subscription lifecycle. Confirm availability and compliance requirements
before choosing the provider.

### Deployment

-   Git repository with development, staging, and production
    environments.
-   Managed PostgreSQL and private object storage.
-   Environment variables/secrets manager.
-   CI checks for linting, type checking, tests, and production builds.
-   Error monitoring, structured logs, backups, and uptime monitoring.

**Important:** This is a proposed architecture, not a claim about
Civils.ai's internal stack. Choose services based on current price,
regional availability, data residency, and team experience.

------------------------------------------------------------------------

## 6. High-Level Architecture

``` text
Browser
  ├── Public website and calculators
  └── Authenticated project workspace
          │
          ▼
Application API / Authorization Layer
  ├── Authentication and user profile
  ├── Projects and membership
  ├── Document metadata and signed uploads
  ├── Chat and retrieval
  ├── Takeoff and geometry
  ├── Usage metering and billing
  └── Audit events
          │
          ├── PostgreSQL: users, projects, documents, messages, takeoffs, billing
          ├── Private object storage: original PDFs, page images, exports
          ├── Background queue: parse, OCR, index, export, AI jobs
          └── AI/model adapter: embeddings, answer generation, structured extraction
```

### Trust boundaries

-   The browser is untrusted.
-   Every object ID received from the browser must be checked against
    the authenticated user's access.
-   Never expose permanent public URLs for private project documents.
-   API keys remain on the server.
-   Workers receive only the permissions and document references
    required for their job.
-   Webhook signatures must be verified before changing billing state.
-   Uploaded documents are data, not instructions.

------------------------------------------------------------------------

## 7. Suggested Project Structure

``` text
buildscope-ai/
├── public/
│   ├── images/
│   ├── icons/
│   └── logo.svg
├── src/
│   ├── app/
│   │   ├── (marketing)/
│   │   │   ├── page.tsx
│   │   │   ├── tools/page.tsx
│   │   │   ├── tools/concrete-calculator/page.tsx
│   │   │   ├── tools/steel-weight-calculator/page.tsx
│   │   │   ├── tools/beam-calculator/page.tsx
│   │   │   ├── pricing/page.tsx
│   │   │   └── contact/page.tsx
│   │   ├── (auth)/
│   │   │   ├── login/page.tsx
│   │   │   ├── register/page.tsx
│   │   │   └── forgot-password/page.tsx
│   │   ├── app/
│   │   │   ├── page.tsx
│   │   │   ├── projects/page.tsx
│   │   │   ├── projects/[projectId]/page.tsx
│   │   │   ├── projects/[projectId]/documents/page.tsx
│   │   │   ├── projects/[projectId]/chat/page.tsx
│   │   │   ├── projects/[projectId]/takeoff/page.tsx
│   │   │   └── settings/
│   │   ├── api/
│   │   ├── privacy/page.tsx
│   │   ├── terms/page.tsx
│   │   └── engineering-disclaimer/page.tsx
│   ├── components/
│   │   ├── layout/
│   │   ├── forms/
│   │   ├── calculators/
│   │   ├── pdf/
│   │   ├── takeoff/
│   │   ├── chat/
│   │   └── ui/
│   ├── lib/
│   │   ├── auth/
│   │   ├── db/
│   │   ├── validation/
│   │   ├── storage/
│   │   ├── ai/
│   │   ├── pdf/
│   │   ├── geometry/
│   │   ├── billing/
│   │   └── usage/
│   ├── server/
│   │   ├── services/
│   │   └── repositories/
│   ├── types/
│   └── tests/
├── worker/
│   ├── jobs/
│   └── index.ts
├── prisma/ or db/
├── docs/
├── .env.example
├── package.json
├── tsconfig.json
└── README.md
```

Keep business logic in reusable services rather than embedding formulas
or authorization checks inside page components.

------------------------------------------------------------------------

# Phase 1 --- Public Website and Engineering Calculators

## 8. Objectives

Build a polished public site that explains the product and provides
three useful calculators. This phase should work without accounts, AI
APIs, or a paid subscription.

### Deliverables

-   Responsive home page.
-   Tool directory with search and category filters.
-   Concrete volume/material calculator.
-   Steel bar weight calculator.
-   Beam calculator for explicitly supported cases.
-   Pricing page with honest plan descriptions.
-   Contact form with server-side validation and spam protection.
-   SEO metadata, sitemap, robots rules, favicon, and basic analytics
    with appropriate privacy controls.
-   Terms, privacy, and engineering disclaimer pages.

## 9. Page requirements

### Home page

Sections:

1.  Header with logo, Tools, Features, Pricing, Sign in, and primary
    CTA.
2.  Hero: clear statement of who the platform helps and what it does.
3.  Primary CTA: "Explore free tools"; secondary CTA: "See how it
    works."
4.  Featured calculators.
5.  Future/available document workflow features, clearly labelled by
    availability.
6.  How it works: calculate → save → review → export.
7.  Trust section with transparent limitations; do not invent
    testimonials or customer logos.
8.  Pricing preview.
9.  FAQ.
10. Footer with legal links and contact.

### Tool directory

-   Search by tool name.
-   Filter by category: Materials, Structural, Surveying, Geotechnical,
    Water, and Project Management.
-   Each tool card displays title, short description, status, and "Open
    calculator."
-   Do not list unfinished tools as working products. Mark planned tools
    as "Coming soon" only if they are genuinely on the roadmap.

### Pricing page

-   Free and future paid plans may be displayed, but do not charge users
    until billing is implemented.
-   Clearly state included features, usage limits, taxes if applicable,
    and cancellation terms.
-   Avoid unsupported claims such as "unlimited" unless technically
    enforced and financially sustainable.

### Contact page

-   Name, business email, organization (optional), subject, message, and
    consent where required.
-   Validate on the server.
-   Add rate limiting, spam controls, and a success/failure state.
-   Never display submitted messages publicly.

## 10. Calculator specifications

### 10.1 Concrete volume calculator

**Inputs**

-   Shape: rectangular slab, beam, column, footing, or custom
    rectangular volume.
-   Length, width, depth/height.
-   Unit selector: mm, cm, m, ft, in.
-   Optional wastage percentage.
-   Optional mix ratio for a rough material estimate, explicitly
    labelled as an assumption.

**Core calculation**

Convert all dimensions to metres, then:

-   Rectangular volume: `V = length × width × depth`
-   Total with wastage: `V_total = V × (1 + wastage_percent / 100)`

For multiple elements, calculate each volume and sum them. Do not
confuse wet concrete volume with the dry material volume used in a mix
estimate.

**Outputs**

-   Geometric volume in m³ and ft³.
-   Volume before and after wastage.
-   If a mix estimate is offered: show the assumed dry-volume factor,
    ratio, material density assumptions, and estimated cement bags.
-   Copy result, reset, and print/save summary.

**Validation**

-   Reject blank, non-numeric, zero, and negative dimensions.
-   Set sensible maximum values to prevent invalid inputs.
-   Unit conversion must happen before multiplication.
-   Round only for display; retain full precision internally.
-   Show assumptions and note that final mix design should be specified
    by the responsible engineer.

### 10.2 Steel bar weight calculator

For a nominal round steel bar, the common approximate formula is:

`Weight (kg) = diameter² × length / 162`

where diameter is in millimetres and length is in metres. This is an
approximation based on nominal steel density.

**Inputs**

-   Bar diameter (mm).
-   Length per bar (m).
-   Number of bars.
-   Optional wastage percentage.

**Outputs**

-   Weight per bar.
-   Total length.
-   Total weight in kg and tonnes.
-   Estimated weight including wastage.

**Validation and explanation**

-   Diameter and length must be positive.
-   Number of bars must be a positive integer.
-   Explain that nominal/theoretical weight can differ from delivered
    weight and applicable product standards.

### 10.3 Beam calculator

**MVP scope:** simply supported beam with a clearly defined set of
loads, such as a single point load at a specified location and/or a
uniform distributed load over the full span. Expand only after test
cases are established.

**Inputs**

-   Span and units.
-   Point load and position.
-   Uniformly distributed load (if enabled).
-   Sign convention.
-   Optional display scale for diagrams.

**Outputs**

-   Support reactions.
-   Shear-force diagram.
-   Bending-moment diagram.
-   Maximum shear and bending moment for supported cases.
-   Deflection only if material properties, section properties, boundary
    conditions, and load cases are fully specified and implemented.

For a simply supported beam with a full-span uniform load `w` and span
`L`, reactions are `RA = RB = wL/2`, and maximum bending moment is
`wL²/8`. Implement the correct equations for each supported load case
and unit system.

**Important boundaries**

-   Do not label a basic analysis result as a complete structural
    design.
-   Do not output reinforcement requirements until design code, material
    grades, load combinations, section geometry, and applicable limit
    states are implemented.
-   Include equilibrium checks: total vertical reactions must equal
    total applied vertical load, and moments must balance within
    numerical tolerance.
-   Create unit tests from hand-calculated examples.

## 11. Phase 1 design and quality requirements

-   Mobile-responsive layout and keyboard accessibility.
-   Consistent units and clear error messages.
-   Results visible without unnecessary sign-up walls.
-   Shareable URLs only for non-sensitive preset examples, not private
    calculations.
-   Structured data and unique metadata for each tool.
-   Fast page load and compressed assets.
-   Automated tests for formulas, edge cases, unit conversion, and
    formatting.

## 12. Phase 1 acceptance criteria

-   All seven main routes work on desktop and mobile.
-   Calculators handle valid, invalid, boundary, and unit-conversion
    test cases.
-   Beam results pass independent benchmark examples for every supported
    load case.
-   Contact form validates and safely handles submissions.
-   No secrets are present in browser bundles.
-   Privacy, terms, and engineering disclaimer pages are accessible.
-   Production build, linting, type checking, and automated tests pass.

------------------------------------------------------------------------

# Phase 2 --- Accounts, Projects, Secure PDF Uploads, and Document Library

## 13. Objectives

Turn the public site into a multi-user application where users can
create projects and store private documents. AI chat is not required
yet.

### Deliverables

-   Registration, login, logout, password reset, and email verification.
-   Profile and account settings.
-   Project create, list, rename, archive, and delete.
-   Project membership model, initially supporting owner and member.
-   PDF upload with file validation and progress.
-   Document library with status, size, upload time, and processing
    state.
-   Private object storage and authorized downloads.
-   Background processing status and retry behavior.

## 14. Authentication and authorization

Use a reputable authentication library or managed identity provider. Do
not create a custom password system unless there is a compelling reason
and the security expertise to maintain it.

Requirements:

-   Store only properly hashed passwords if passwords are managed
    directly.
-   Secure session cookies: `HttpOnly`, `Secure` in production, and
    suitable `SameSite` settings.
-   Protect state-changing operations against CSRF where applicable.
-   Rate-limit login, reset, registration, and verification endpoints.
-   Use generic login/reset responses where needed to reduce account
    enumeration.
-   Verify email addresses where the product requires trusted contact.
-   Provide session revocation and logout.
-   Enforce authorization server-side for every project, document, and
    export.
-   Never trust a project ID supplied by the browser without checking
    membership.

## 15. Project workflow

1.  User signs in.
2.  User creates a project with name, optional project code, location,
    and description.
3.  Application creates the project and owner membership in one
    transaction.
4.  User opens the project workspace.
5.  User uploads a PDF.
6.  Server validates the upload and stores it in private object storage.
7.  A background job extracts metadata and text, if supported.
8.  UI displays processing status and any error.
9.  User can retry or delete the document.

### Document states

-   `uploading`
-   `uploaded`
-   `queued`
-   `processing`
-   `ready`
-   `failed`
-   `deleting`
-   `deleted`

State transitions must be explicit. Do not leave failed jobs looking
like they are still processing.

## 16. Secure PDF upload design

-   Permit PDF only in the initial release.
-   Validate extension, MIME type, and file signature; do not rely on
    filename alone.
-   Enforce file-size and page-count limits.
-   Generate server-side object keys; do not use user filenames as
    storage paths.
-   Store files in a private bucket.
-   Use short-lived signed URLs after checking authorization.
-   Consider malware scanning and safe PDF parsing in an isolated
    worker.
-   Do not render uploaded files through an unsafe HTML path.
-   Set per-user and per-plan storage limits.
-   Record file checksum, page count, file size, uploader, and
    timestamps.
-   Avoid logging document contents or signed URLs.
-   Implement deletion of original files and derived artifacts according
    to a documented retention policy.

## 17. Database schema --- initial entities

### `users`

-   `id`
-   `email` (unique)
-   `name`
-   `email_verified_at`
-   `created_at`
-   `updated_at`
-   `status`

### `projects`

-   `id`
-   `name`
-   `project_code` (optional)
-   `description` (optional)
-   `created_by`
-   `created_at`
-   `updated_at`
-   `archived_at` (optional)

### `project_members`

-   `project_id`
-   `user_id`
-   `role` (`owner`, `admin`, `editor`, `viewer`)
-   `created_at`

Unique constraint: `(project_id, user_id)`.

### `documents`

-   `id`
-   `project_id`
-   `uploaded_by`
-   `original_filename`
-   `storage_key`
-   `mime_type`
-   `file_size_bytes`
-   `checksum`
-   `page_count`
-   `status`
-   `processing_error_code` (not raw secrets)
-   `created_at`
-   `updated_at`
-   `deleted_at`

Add foreign keys, indexes, and appropriate cascading/restrictive
deletion rules. A document must not be readable by users who are not
members of its project.

## 18. Phase 2 acceptance criteria

-   Users can register, verify, sign in, sign out, and reset passwords.
-   Project operations enforce role-based access.
-   A user cannot read another user's project by guessing an ID.
-   Invalid, oversized, or unsupported files are rejected.
-   Private PDFs cannot be downloaded without authorization.
-   Upload and processing errors are understandable and recoverable.
-   Deletion removes or schedules removal of files and derived
    artifacts.
-   Security tests cover cross-user access and project membership
    changes.

------------------------------------------------------------------------

# Phase 3 --- AI Document Chat with Source-Page References

## 19. Objectives

Allow users to ask questions about drawings and specifications stored in
their project. Answers must be grounded in extracted document content
and show where the answer came from.

### Deliverables

-   PDF text extraction and page-level metadata.
-   OCR fallback for scanned pages.
-   Chunking and indexing pipeline.
-   Project-scoped semantic and keyword search.
-   Chat UI with source citations.
-   Page opening or highlighting from a citation.
-   "Not found in the uploaded documents" behavior.
-   Usage metering, logging, and error handling.

## 20. Document ingestion pipeline

1.  Confirm the user has access to the project and document.
2.  Check the document's current processing state.
3.  Extract embedded text per page.
4.  If text is missing or too sparse, use OCR for that page.
5.  Preserve page number, document ID, extraction method, and text
    offsets.
6.  Normalize whitespace while preserving meaningful tables, units, and
    labels.
7.  Split text into chunks suitable for retrieval; do not mix unrelated
    pages without retaining provenance.
8.  Generate embeddings if using semantic search.
9.  Store chunks and index metadata.
10. Mark the document ready only when required processing is complete;
    otherwise report partial availability.

### Chunk metadata

Each chunk should include:

-   `document_id`
-   `project_id`
-   `page_number`
-   `chunk_index`
-   `text`
-   `start_offset` and `end_offset` where available
-   `extraction_method` (`text`, `ocr`, or `manual`)
-   `embedding` if used
-   `created_at`
-   optional section/title metadata

## 21. Question-answering flow

1.  Authenticate the user.
2.  Confirm membership in the requested project.
3.  Validate the question and enforce request limits.
4.  Retrieve relevant chunks only from documents the user is authorized
    to access.
5.  Combine semantic retrieval with keyword search when useful.
6.  Rerank candidates if necessary.
7.  Send the question and retrieved evidence to the model with explicit
    instructions to treat evidence as untrusted content.
8.  Generate an answer with source references.
9.  Validate that cited document IDs and page numbers exist in the
    retrieved evidence.
10. Save the message and usage record, then return the answer.

### Answer format

Each answer should include:

-   **Answer:** concise response.
-   **Evidence:** short explanation grounded in the source.
-   **Sources:** document name and page number, with a link to open the
    page.
-   **Confidence/limitations:** use meaningful labels such as "Supported
    by source," "Partial evidence," or "Not found."
-   **Next step:** ask for clarification or suggest a manual review when
    evidence is incomplete.

Do not present a model's subjective confidence score as a calibrated
probability unless it has been empirically calibrated.

### Example

**Question:** "What concrete grade is specified for the ground-floor
columns?"

**Response when supported:** "The specification identifies M30 concrete
for the ground-floor columns."\
**Source:** `Structural Specifications.pdf`, page 14.\
**Status:** Supported by extracted text.

If documents conflict, the assistant should say that two sources differ
and cite both, rather than silently selecting one.

If the information is not found, respond: "I could not locate this
information in the currently indexed project documents." Do not invent a
value.

## 22. AI safety and reliability

-   Treat all uploaded document text as untrusted data.
-   Do not allow document instructions to override system or developer
    instructions.
-   Do not let retrieved text invoke tools or access other projects.
-   Apply project authorization before retrieval, not only after
    generation.
-   Filter retrieval results by project and permission scope.
-   Prevent one project's chunks from entering another project's prompt.
-   Validate structured outputs against a schema.
-   Limit context size, requests per minute, and maximum output.
-   Log metadata and source IDs without unnecessarily logging sensitive
    content.
-   Provide deletion and re-indexing flows.
-   Keep model/API keys server-side.
-   Track retrieval quality, citation validity, unsupported-answer rate,
    latency, and cost.

## 23. Phase 3 acceptance criteria

-   Supported answers cite correct documents and pages.
-   The model declines to invent unsupported specifications.
-   Conflicting source values are surfaced.
-   Users cannot retrieve another project's content.
-   OCR and parsing failures are visible.
-   Citations open the correct page.
-   AI usage is metered and errors are recoverable.
-   A test set of representative project questions passes defined
    grounding criteria.

------------------------------------------------------------------------

# Phase 4 --- Drawing Viewer and Quantity Takeoff

## 24. Objectives

Provide a reviewable measurement workspace for PDF drawings. Begin with
user-guided measurement; add AI-assisted element detection only after
the measurement engine is dependable.

### Deliverables

-   In-browser PDF viewer.
-   Page navigation and zoom.
-   Drawing scale calibration.
-   Manual point, line, polyline, rectangle, and polygon tools.
-   Length, perimeter, and area calculations.
-   Count markers.
-   Takeoff item list with units and notes.
-   Measurement overlays on the drawing.
-   Undo/redo, edit, and delete.
-   Save and reload measurements.
-   Export to CSV/XLSX and PDF summary.
-   Clear distinction between manually measured and AI-suggested items.

## 25. Scale calibration

PDF coordinates are not automatically real-world dimensions. Each page
must be calibrated, and different pages may use different scales.

### Calibration workflow

1.  User opens a page.
2.  User selects two points representing a known dimension.
3.  User enters the known real-world length and unit.
4.  Application calculates the scale factor from the displayed or PDF
    coordinate distance.
5.  UI shows the calibrated scale and lets the user confirm it.
6.  User measures geometry using that calibration.
7.  The calibration is saved against that document page and revision.

If PDF coordinates are in points, the system must account for page
rotation, zoom, and viewport transforms. Use a consistent coordinate
space for stored geometry; do not store screen pixels as permanent
measurement coordinates.

### Example

If a known 5 m wall spans 250 PDF coordinate units in the chosen
coordinate system:

`scale = 5 m / 250 units = 0.02 m per unit`

A measured line of 75 units then represents:

`length = 75 × 0.02 = 1.5 m`

This example illustrates the calibration process only. The
implementation must account for the actual PDF rendering transform.

### Scale validation

-   Warn if the calibration line is extremely short.
-   Allow recalibration.
-   Store calibration method, known dimension, measured coordinate
    distance, unit, and timestamp.
-   Do not assume a printed scale is accurate after scanning or
    resizing.
-   Require calibration separately for pages that may have different
    scales.
-   Show a visible "Not calibrated" state before enabling real-world
    quantity output.

## 26. Geometry and measurement model

Represent measurement geometry in document/page coordinates, not browser
pixels.

### Supported MVP measurement types

-   `length`: line or polyline.
-   `area`: polygon.
-   `count`: point markers.
-   `perimeter`: derived from a closed polygon when applicable.

### Example `takeoff_items` fields

-   `id`
-   `project_id`
-   `document_id`
-   `page_number`
-   `created_by`
-   `label`
-   `measurement_type`
-   `geometry_json`
-   `raw_measurement`
-   `real_measurement`
-   `unit`
-   `scale_calibration_id`
-   `status` (`draft`, `needs_review`, `approved`, `rejected`)
-   `source_type` (`manual`, `ai_suggested`)
-   `notes`
-   `created_at`
-   `updated_at`

### Geometry rules

-   Apply the saved page calibration to convert coordinates into
    real-world units.
-   Calculate polygon area using a suitable geometry algorithm.
-   Handle page rotation and transform inversion consistently.
-   Detect self-intersecting polygons and invalid geometry.
-   Prevent negative lengths and impossible areas.
-   Recalculate quantities if calibration changes; mark prior values as
    changed and require review.
-   Keep original geometry so a user can edit the measurement later.

## 27. Takeoff review workflow

1.  User opens a calibrated drawing page.
2.  User draws or selects an object.
3.  User assigns a label, such as "Internal partition wall."
4.  Application calculates the measured quantity.
5.  User reviews units, calibration, and assumptions.
6.  User saves the item as draft or submits it for review.
7.  Reviewer approves, rejects, or requests changes.
8.  Approved items contribute to a quantity summary.
9.  User exports the schedule.

### Quantity aggregation

Group compatible items by project, category, material, unit, and
relevant specification. Do not sum incompatible units. Keep a clear
record of exclusions, deductions, and wastage assumptions.

### AI-assisted takeoff, later in this phase

AI may suggest likely walls, rooms, doors, windows, or drawing symbols.
Suggestions must include:

-   Suggested element type.
-   Proposed geometry or count.
-   Source page and location.
-   Model/processing version.
-   Review status.
-   Ability to edit or reject.

Never treat AI-detected geometry as approved quantity by default.

## 28. Exports

### CSV/XLSX schedule

Include:

-   Project name and identifier.
-   Document and page.
-   Item number and description.
-   Measurement type.
-   Quantity and unit.
-   Scale/calibration reference.
-   Status and reviewer.
-   Notes, exclusions, and assumptions.
-   Export date and application version.

### PDF report

Include a summary page, quantities table, measurement assumptions,
source page references, and selected annotated drawing images. Avoid
implying that the report is a certified bill of quantities unless a
qualified professional has reviewed and approved it.

## 29. Phase 4 acceptance criteria

-   Calibration is repeatable and correctly saved per page.
-   Geometry transforms remain accurate through zoom, pan, and rotation.
-   Measurement results pass known geometry test cases.
-   Invalid polygons and missing calibration are handled safely.
-   Users can edit, delete, save, reload, and review items.
-   Exports reconcile with the on-screen quantity schedule.
-   AI-suggested quantities are visibly distinguished from manual or
    approved quantities.

------------------------------------------------------------------------

# Phase 5 --- Quality Checks, SaaS Billing, Usage Limits, and Teams

## 30. Objectives

Turn the product into a commercial multi-user SaaS with controlled
usage, subscription lifecycle management, quality review, and enterprise
capabilities.

### Deliverables

-   Quality-check rules and discrepancy review.
-   Subscription plans and entitlements.
-   Checkout and billing portal.
-   Payment webhook handling.
-   Usage tracking and limits.
-   Team invitations and role-based access.
-   Audit logs.
-   Admin dashboard for support and operational visibility.
-   Enterprise plan configuration and integration hooks.

## 31. Quality checks

Start with deterministic rules before adding AI-based discrepancy
detection.

### Initial deterministic checks

-   Required drawing/specification field missing.
-   Inconsistent unit usage within a defined dataset.
-   Duplicate takeoff item candidates.
-   Quantity item with no source page.
-   Measurement with missing or outdated calibration.
-   Unapproved AI-suggested measurement included in an export.
-   Conflicting values found in selected project documents.
-   Drawing revision changed after a takeoff was approved.

### Discrepancy record

-   `id`
-   `project_id`
-   `check_type`
-   `severity` (`info`, `warning`, `critical`)
-   `title`
-   `description`
-   `source_references`
-   `related_document_ids`
-   `related_takeoff_item_ids`
-   `status` (`open`, `in_review`, `resolved`, `dismissed`)
-   `assigned_to`
-   `resolution_note`
-   `created_at`
-   `resolved_at`

Severity must represent a defined business rule. Do not call an issue
"critical" solely because a model uses alarming language.

### AI-assisted checks

AI may identify possible mismatches between specifications, schedules,
drawings, and takeoff items. Every finding must show evidence from both
sides, the reason it was flagged, and a reviewer action. False positives
must be expected, measured, and tracked.

## 32. SaaS plan and entitlement design

Do not hard-code plan names and limits throughout the UI. Store plan
entitlements centrally and check them server-side.

Possible dimensions:

-   Number of active projects.
-   Storage capacity.
-   Pages processed per month.
-   AI question/answer usage.
-   Takeoff items or drawing pages.
-   Team seats.
-   Export features.
-   Retention period.
-   Support level.
-   Enterprise integrations.

### Example plan structure

  -----------------------------------------------------------------------
  Plan                    Intended user           Example entitlements
  ----------------------- ----------------------- -----------------------
  Free                    Student or evaluator    Public calculators,
                                                  sample project, limited
                                                  document demo

  Individual              Independent engineer    Private projects,
                                                  modest storage, capped
                                                  AI usage

  Professional            Estimator or consultant Higher usage, takeoff
                                                  exports, saved work,
                                                  revision workflow

  Team                    Small construction      Multiple seats, shared
                          company                 projects, role-based
                                                  access

  Enterprise              Large organization      Custom quotas, SSO,
                                                  audit controls,
                                                  integrations,
                                                  negotiated terms
  -----------------------------------------------------------------------

Limits and prices should be determined by customer research and actual
operating costs. Do not copy competitor prices without understanding the
economics.

## 33. Billing lifecycle

1.  User selects a plan.
2.  Server checks eligibility and creates a checkout session with the
    payment provider.
3.  User completes payment on the provider's secure checkout.
4.  Provider sends a signed webhook.
5.  Server verifies the webhook signature and event identity.
6.  Subscription state and entitlements are updated idempotently.
7.  User sees current plan, usage, renewal date, and billing status.
8.  Renewal, cancellation, payment failure, refund, and plan changes are
    processed through verified events.
9.  User can access invoices and manage cancellation according to the
    published terms.

### Billing rules

-   Never trust a client-side "payment successful" redirect as proof of
    payment.
-   Verify webhook signatures.
-   Make webhook processing idempotent.
-   Store provider customer and subscription identifiers.
-   Define grace periods and behavior after failed payment.
-   Keep a history of plan changes.
-   Avoid deleting user documents immediately when a subscription
    expires; apply a clear retention and export policy.
-   Provide transparent cancellation and refund terms.
-   Follow applicable tax, consumer protection, and recurring payment
    requirements for the target market.

## 34. Usage metering

Create usage events for billable operations, such as:

-   Document pages processed.
-   OCR pages processed.
-   AI input/output tokens or provider cost units.
-   Takeoff jobs.
-   Export jobs.
-   Stored bytes.
-   Active seats.

Each event should include:

-   User and workspace.
-   Event type.
-   Quantity.
-   Timestamp.
-   Related project/document where appropriate.
-   Idempotency key.
-   Provider usage/cost metadata where available.

Usage checks must be performed on the server before starting expensive
jobs. Reserve usage where necessary, then reconcile actual usage after
completion. Failed jobs should follow a documented charge/refund policy.

## 35. Team roles and permissions

Recommended roles:

-   **Owner:** manages billing, workspace, members, and deletion.
-   **Admin:** manages members and project settings, but billing access
    can be separately controlled.
-   **Editor:** uploads documents and edits project content.
-   **Reviewer:** reviews and approves takeoffs and discrepancy
    findings.
-   **Viewer:** read-only access to permitted project information.

Implement permissions at the service/API layer. The UI should reflect
permissions but must not be the enforcement mechanism.

## 36. Enterprise capabilities

Do not build every enterprise feature before customers request it.
Prepare an architecture that can support:

-   SSO/SAML or an enterprise identity provider.
-   Audit exports.
-   Configurable data retention.
-   Organization-level workspaces.
-   Custom quotas.
-   API keys with scopes, rotation, and revocation.
-   Webhooks and integrations.
-   Security questionnaire and data-processing documentation.
-   Support/SLA options appropriate to actual operational capacity.

------------------------------------------------------------------------

## 37. Database Model --- Expanded

Core entities:

-   `users`
-   `sessions` or identity-provider references
-   `organizations`
-   `organization_members`
-   `projects`
-   `project_members`
-   `documents`
-   `document_pages`
-   `document_chunks`
-   `chat_threads`
-   `chat_messages`
-   `message_sources`
-   `scale_calibrations`
-   `takeoff_items`
-   `takeoff_revisions`
-   `quality_checks`
-   `discrepancies`
-   `plans`
-   `subscriptions`
-   `usage_events`
-   `invoices` or billing-provider references
-   `audit_events`
-   `background_jobs`

### Data-model guidance

-   Use UUIDs or equivalent non-guessable identifiers, but do not treat
    unguessable IDs as authorization.
-   Add foreign keys and indexes for common project/document queries.
-   Store timestamps in UTC and format them for the user.
-   Use transactions for related changes such as project creation plus
    owner membership.
-   Keep billing-provider state synchronized through verified webhooks.
-   Preserve revisions for approved measurements and important quality
    decisions.
-   Establish data-retention and deletion policies before storing
    sensitive project files.
-   Consider tenant isolation and organization boundaries from the
    beginning.

------------------------------------------------------------------------

## 38. API Design

Use a consistent response format, server-side schema validation, and
authorization checks on every private endpoint.

### Representative endpoints

  ------------------------------------------------------------------------------------------
  Method                  Route                                      Purpose
  ----------------------- ------------------------------------------ -----------------------
  `GET`                   `/api/tools`                               List public calculators

  `POST`                  `/api/projects`                            Create project

  `GET`                   `/api/projects`                            List accessible
                                                                     projects

  `GET`                   `/api/projects/:id`                        Read project after
                                                                     authorization

  `PATCH`                 `/api/projects/:id`                        Update permitted
                                                                     project fields

  `DELETE`                `/api/projects/:id`                        Request project
                                                                     deletion

  `POST`                  `/api/projects/:id/documents/upload-url`   Start authorized upload

  `POST`                  `/api/documents/:id/complete-upload`       Verify and finalize
                                                                     upload

  `GET`                   `/api/documents/:id`                       Read document metadata

  `DELETE`                `/api/documents/:id`                       Delete document and
                                                                     derived data

  `POST`                  `/api/projects/:id/chat`                   Ask a document-grounded
                                                                     question

  `POST`                  `/api/projects/:id/takeoff-items`          Create measurement

  `PATCH`                 `/api/takeoff-items/:id`                   Edit a measurement

  `POST`                  `/api/takeoff-items/:id/review`            Review/approve
                                                                     according to role

  `POST`                  `/api/projects/:id/exports`                Queue an export job

  `GET`                   `/api/usage`                               Read current usage

  `POST`                  `/api/billing/checkout`                    Create checkout session

  `POST`                  `/api/webhooks/billing`                    Receive verified
                                                                     provider events
  ------------------------------------------------------------------------------------------

These routes are illustrative. Match the final design to the chosen
framework and payment provider. Validate IDs, body size, data types, and
user permissions. Return safe errors without leaking internal stack
traces.

------------------------------------------------------------------------

## 39. Background Jobs and Reliability

Use background workers for tasks that can take longer than a normal web
request:

-   PDF metadata extraction.
-   OCR and text extraction.
-   Chunking and embeddings.
-   AI-assisted checks.
-   Takeoff batch processing.
-   XLSX/PDF export.
-   File deletion and retention cleanup.

Every job should have:

-   Unique job ID.
-   Type and safe input references.
-   State and progress.
-   Attempt count and retry policy.
-   Idempotency behavior.
-   Error category.
-   Start/completion timestamps.
-   Usage accounting.
-   Cancellation policy where practical.

Use bounded retries and a dead-letter/manual-retry path. Avoid retry
loops that repeatedly charge for or duplicate the same operation.

------------------------------------------------------------------------

## 40. Security, Privacy, and Compliance Checklist

### Application security

-   Server-side authorization for every private resource.
-   Input validation and output encoding.
-   Rate limiting and abuse prevention.
-   CSRF protection where applicable.
-   Safe session handling.
-   Secrets manager and key rotation.
-   Dependency scanning and patching.
-   Secure error messages.
-   Audit events for sensitive operations.

### Document security

-   Private object storage.
-   Short-lived signed links.
-   File type and size validation.
-   Malware scanning where appropriate.
-   Isolated parsing/OCR workers.
-   No public indexing of customer files.
-   Retention and deletion policy.
-   Avoid sensitive content in logs and analytics.

### AI security

-   Prompt-injection-resistant document handling.
-   Project-scoped retrieval.
-   Citation validation.
-   Structured output validation.
-   Tool permission boundaries.
-   Cost limits and abuse controls.
-   Evaluation dataset and regression tests.
-   Clear distinction between generated suggestions and verified facts.

### Privacy and legal readiness

-   Privacy policy and terms reviewed for the operating jurisdictions.
-   Document ownership and processing terms.
-   Disclosure of AI providers and relevant subprocessors.
-   Data deletion and export procedures.
-   Retention periods and backup deletion schedule.
-   Appropriate consent and cookie/analytics controls.
-   Applicable data protection, tax, payment, and consumer requirements
    reviewed with qualified advisers.

------------------------------------------------------------------------

## 41. Testing Strategy

### Unit tests

-   Unit conversions.
-   Concrete volume and material estimates.
-   Steel bar weight.
-   Beam reactions, shear, moment, and supported deflection cases.
-   Geometry transforms and area/length.
-   Entitlement calculations.
-   Usage aggregation.
-   Billing webhook idempotency.

### Integration tests

-   Registration and password reset.
-   Project creation and membership.
-   Upload authorization and object-storage workflow.
-   Document processing state transitions.
-   Retrieval restricted to a project.
-   Citation mapping.
-   Takeoff persistence and exports.
-   Billing lifecycle and webhook replay.

### End-to-end tests

-   Visitor uses a calculator.
-   User creates a project and uploads a PDF.
-   User asks a question and opens a cited source page.
-   User calibrates a drawing, measures an area, and exports the
    schedule.
-   Reviewer approves a measurement.
-   Subscription limit blocks a new expensive job with a clear
    explanation.
-   Viewer cannot edit, approve, or download resources outside their
    permissions.

### AI evaluation

Maintain a versioned benchmark of realistic, permission-safe test
documents and questions. Track:

-   Retrieval recall.
-   Citation correctness.
-   Unsupported-claim rate.
-   Conflict detection.
-   OCR error rate.
-   Latency and cost.
-   User correction rate.

Do not rely on a few impressive demos as proof of production
reliability.

------------------------------------------------------------------------

## 42. Deployment and Environment Setup

### Initial setup

Install a current supported Node.js LTS release and Git. Create the app
with the selected framework and TypeScript.

Example setup for the recommended Next.js path:

``` bash
npx create-next-app@latest buildscope-ai
cd buildscope-ai
npm run dev
```

Choose TypeScript, linting, and the styling/routing options appropriate
to the installed framework version. Commit the initial project before
adding integrations.

### Environment variables

Create `.env.example` with variable names only, no real secrets. For
example:

``` env
DATABASE_URL=
AUTH_SECRET=
APP_BASE_URL=http://localhost:3000

OBJECT_STORAGE_ENDPOINT=
OBJECT_STORAGE_BUCKET=
OBJECT_STORAGE_ACCESS_KEY=
OBJECT_STORAGE_SECRET_KEY=

AI_PROVIDER_API_KEY=
AI_MODEL_NAME=

PAYMENT_SECRET_KEY=
PAYMENT_WEBHOOK_SECRET=
```

Rename variables to match the selected providers. Keep `.env.local` out
of version control. Never place private AI, storage, database, or
payment keys in `NEXT_PUBLIC_*` variables.

### Environments

-   **Local:** developer machine and test data.
-   **Staging:** production-like configuration with test credentials and
    synthetic documents.
-   **Production:** real customer data, restricted secrets, backups,
    monitoring, and access controls.

Before each deployment, run formatting/linting, type checking, unit
tests, integration tests, and a production build.

------------------------------------------------------------------------

## 43. Suggested Milestones and Priorities

The schedule depends on available hours, experience, and provider
choices. Use deliverable-based milestones rather than promising a fixed
date before implementation starts.

  ------------------------------------------------------------------------
  Milestone               Deliverable             Exit condition
  ----------------------- ----------------------- ------------------------
  M1                      Public layout and       All marketing pages
                          routes                  responsive

  M2                      Calculators             Formula and unit tests
                                                  pass

  M3                      Public launch           SEO, legal pages,
                                                  analytics, contact form

  M4                      Authentication and      Access-control tests
                          projects                pass

  M5                      Secure document library Private
                                                  upload/download/delete
                                                  works

  M6                      Document indexing and   Grounded answers and
                          chat                    valid citations

  M7                      Manual takeoff          Calibration, geometry,
                                                  saving, export verified

  M8                      Quality checks          Findings are reviewable
                                                  and traceable

  M9                      Billing and quotas      Webhooks, entitlements,
                                                  and usage tested

  M10                     Pilot and hardening     Customer feedback,
                                                  security review,
                                                  monitoring
  ------------------------------------------------------------------------

### Prioritization rule

If time or budget is limited, prioritize:

1.  Reliable core workflows.
2.  Security and data isolation.
3.  Accuracy tests and clear limitations.
4.  Customer validation.
5.  Billing and support.
6.  Advanced AI automation and enterprise extras.

------------------------------------------------------------------------

## 44. Business and Product Metrics

Measure whether the product solves a real problem and whether usage is
economically sustainable.

### Acquisition

-   Organic visits by tool page.
-   Visitor-to-signup conversion.
-   Signup source.
-   Cost per qualified lead.

### Activation

-   Time to first successful calculation.
-   Percentage of new users creating a project.
-   Percentage uploading a first document.
-   Percentage completing a first cited answer or measurement.

### Engagement and retention

-   Weekly active workspaces.
-   Projects with repeat usage.
-   Takeoffs completed per active account.
-   30-day and 90-day retention.
-   Reasons for cancellation.

### Revenue

-   Monthly recurring revenue (MRR).
-   Annual recurring revenue (ARR).
-   Average revenue per account.
-   Trial-to-paid conversion.
-   Expansion and contraction.
-   Churn and refunds.

### Cost and quality

-   AI cost per successful task.
-   OCR and storage cost per project.
-   Processing failure rate.
-   Median and 95th-percentile latency.
-   Citation correctness.
-   Measurement correction rate.
-   Support requests per paying account.

Revenue alone is not enough. Monitor gross margin and task quality as
well.

------------------------------------------------------------------------

## 45. Risks and Mitigations

  -----------------------------------------------------------------------
  Risk                                Mitigation
  ----------------------------------- -----------------------------------
  AI hallucinates a specification     Retrieval-grounded answers,
                                      citations, "not found" response,
                                      evaluation tests

  Wrong drawing scale                 Per-page calibration, visible scale
                                      status, test cases, reviewer
                                      confirmation

  Quantity errors                     Editable measurements,
                                      traceability, benchmark drawings,
                                      approval workflow

  Cross-customer data leak            Server-side authorization,
                                      project-scoped retrieval, isolation
                                      tests

  AI costs exceed revenue             Usage quotas, cost metering, job
                                      limits, plan-level entitlements

  Long processing jobs fail           Queue, retries, progress status,
                                      idempotency, manual retry

  Too many features before launch     Phase gates, pilot users, focused
                                      MVP

  Search traffic fails to convert     Measure signup and paid conversion,
                                      interview target users

  Enterprise sales take too long      Start with a narrow segment and
                                      self-serve workflow

  Unsupported engineering claims      Published assumptions, disclaimers,
                                      qualified review, no certification
                                      claims
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 46. Definition of a Successful First Commercial Release

The first commercial release is ready when:

-   Public pages and calculators are reliable and accessible.
-   Users can create accounts and private projects.
-   Documents are isolated by project membership.
-   AI answers cite correct source pages and can decline unsupported
    questions.
-   Takeoff measurements are calibrated, editable, traceable, and
    exportable.
-   Quality findings require appropriate review.
-   Subscription status and quotas are enforced server-side.
-   Payment events are verified and idempotent.
-   Monitoring, backups, support process, and deletion procedures exist.
-   A small group of real target users has completed representative
    workflows and given feedback.
-   Cost per successful task is understood well enough to set
    sustainable limits and pricing.

------------------------------------------------------------------------

## 47. Immediate First Steps

Do these in order:

1.  Create the Git repository and scaffold the Next.js TypeScript app.
2.  Build the shared header, footer, buttons, cards, typography, and
    responsive layout.
3.  Create the seven initial public routes.
4.  Implement and unit-test the concrete volume calculator.
5.  Implement and unit-test the steel bar weight calculator.
6.  Implement a constrained beam calculator and benchmark its outputs.
7.  Add pricing, contact, privacy, terms, and engineering disclaimer
    pages.
8.  Deploy the public MVP and gather feedback before adding
    authentication.
9.  Add the database and authentication only after the public foundation
    is stable.
10. Keep each phase in version control with a working, deployable
    checkpoint.

### First implementation task

Build the public shell and concrete calculator first. Do not start with
AI chat or drawing detection. A reliable first calculator establishes
reusable patterns for validation, units, formulas, result cards, tests,
and documentation that will be used throughout the product.

------------------------------------------------------------------------

## Appendix A --- Engineering Disclaimer Copy

> BuildScope AI provides calculation and document-analysis tools to
> support engineering workflows. Results depend on user inputs,
> source-document quality, assumptions, and implemented methods. Outputs
> may contain errors or omissions and are not a substitute for
> applicable codes, project specifications, independent verification, or
> review by a qualified professional. Users are responsible for checking
> inputs, assumptions, calculations, and final project decisions. Do not
> use unreviewed outputs as the sole basis for safety-critical design,
> construction, or compliance decisions.

Have qualified legal and engineering professionals review this language
before production use.

## Appendix B --- Product Status Labels

Use consistent labels throughout the application:

-   **Available:** implemented and tested.
-   **Beta:** usable but limited; known limitations disclosed.
-   **Coming soon:** planned, not yet usable.
-   **Needs review:** generated or measured output has not been
    approved.
-   **Approved:** reviewed by an authorized user, with reviewer and
    timestamp recorded.
-   **Failed:** processing did not complete; provide a safe retry path
    where possible.

------------------------------------------------------------------------

**End of document.**
