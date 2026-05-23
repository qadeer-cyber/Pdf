# DocuForge AI Architecture

## Product Positioning
Build a differentiated PDF SaaS inspired by category leaders while keeping brand, UX, and visual system fully original.

## Core User Flow
1. User picks a single-purpose tool page.
2. Uploads document(s) via drag-and-drop.
3. Chooses tool options.
4. Job is queued and processed.
5. User downloads output and sees suggested next actions.

## App Surfaces
- Marketing site (SEO, category pages, blog, pricing, trust pages)
- Tool pages (40–60 over time)
- Auth + account
- User dashboard
- Admin dashboard
- API docs + key management

## MVP Tool Set
- Merge PDF
- Split PDF
- Compress PDF
- PDF↔Office/Image conversions
- Rotate/Organize/Extract/Remove pages
- Protect/Unlock/Watermark/Sign/OCR

## High-Level Technical Design

### Frontend
- Next.js App Router + TypeScript
- Tailwind + shadcn/ui + lucide-react + Framer Motion
- Reusable primitives for cards, tool shells, upload states

### Backend
- Next.js Route Handlers
- Prisma ORM + PostgreSQL
- Redis + BullMQ workers for async processing
- Storage abstraction (S3-compatible)

### Processing Pipeline
1. Upload API validates file type, size, and filename.
2. File stored in private bucket with TTL metadata.
3. `PdfJob` created in DB + enqueued in BullMQ.
4. Worker selects processor by `toolSlug`.
5. Output written to storage + DB status updates.
6. Frontend polls job status endpoint.
7. Download endpoint issues scoped signed URL.
8. Retention worker deletes expired inputs/outputs.

### PDF Processing Engines
- `pdf-lib` for lightweight composition tasks
- `qpdf` for split/protect/unlock style operations
- `Ghostscript` for compression profiles
- `LibreOffice CLI` for Office conversions
- `Tesseract` for OCR fallback where needed

## Data Model (Prisma-Oriented)

### Core Models
- `User`
- `Subscription`
- `UsageLimit`
- `File`
- `PdfJob`
- `Tool`
- `AiCreditTransaction`
- `ApiKey`
- `AuditLog`

### Suggested Enums
- `PlanType` (FREE, PRO, BUSINESS)
- `JobStatus` (QUEUED, PROCESSING, COMPLETED, FAILED, EXPIRED)
- `StorageScope` (PRIVATE_INPUT, PRIVATE_OUTPUT, PUBLIC_EXPORT)

## Security & Compliance Baseline
- MIME + magic-byte checks
- Server-side filename sanitization
- Rate limiting on upload/job endpoints
- Signed URLs with short TTL
- Per-plan limits (file size, pages, daily jobs)
- Job timeouts + retry ceilings
- Malware scan hook placeholder
- Auto-delete policy by plan
- Audit logs for sensitive events

## Monetization Architecture
- Free tier with strict limits and light ads on public pages
- Paid plans via Stripe for higher limits + no ads + batch tools
- AI credit ledger for chat/summarize/translate operations
- API metering for B2B billing

## Dashboard Requirements

### User Dashboard
- Overview KPIs
- Files and task history
- Favorites/workflows
- AI assistant and credit usage
- Billing/API/team settings

### Admin Dashboard
- Growth/revenue/failure metrics
- Tool usage and conversion funnel
- Queue health and failed job drill-down
- Coupons/referrals/content manager
- Abuse detection + audit logs

## Build Phases
- Phase 1: foundation + homepage + first 3 tools
- Phase 2: queue/storage hardening + dashboards
- Phase 3: billing/limits + SEO/blog
- Phase 4: AI intelligence and API commercialization
