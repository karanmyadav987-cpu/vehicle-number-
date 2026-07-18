# MASTER PROMPT — VNPR (Vehicle Number Plate Recognition) Production Website

> Copy everything below this line and paste it as the initial instruction to your AI coding agent (Claude Code, Cursor, Windsurf, etc.). It contains the full project brief, tech stack, architecture, features, design system, and page-by-page requirements needed to build a production-grade website.

---

## 1. PROJECT OVERVIEW

Build a **production-ready, fully responsive web application** for **VNPR (Vehicle Number Plate Recognition)** — an AI-powered service that lets users upload a photo of a vehicle and instantly receive:

1. The **extracted number plate text** (via OCR — Optical Character Recognition)
2. **Vehicle information** looked up using that number plate (make, model, color, registration state/RTO, owner category type if available, fuel type, etc., depending on data source used)

**Business identity:**
- **Brand name:** VNPR
- **Owner:** Kritesh Yadav
- **Location:** Bardoli, Gujarat, India
- **Established:** 1996
- **Daily recurring customers:** 45+
- **Contact number:** 1234567890

This is a real, commercial product landing page + functional web app — not a demo. Treat every screen as something a paying customer or business client will actually use.

---

## 2. CORE PRODUCT FUNCTIONALITY

The heart of the product is an **image-to-information pipeline**:

1. **User uploads/captures a photo** of a vehicle (drag-drop, file picker, or mobile camera capture).
2. **Detection stage:** The system detects the license plate region within the photo (bounding box).
3. **OCR stage:** The cropped plate region is passed through an OCR engine to extract the alphanumeric plate string.
4. **Normalization stage:** Extracted text is cleaned/formatted to match Indian number plate conventions (e.g., `GJ 05 AB 1234`).
5. **Lookup stage:** The normalized plate number is used to fetch/display vehicle information (from a database, mock dataset, or third-party vehicle-info API).
6. **Result stage:** The plate number + vehicle info + confidence score + annotated image (bounding box overlay) are displayed to the user, with options to copy, download the report, or save to history.

Also include:
- **Detection history/dashboard** for logged-in users (past uploads, extracted plates, timestamps, downloadable PDF report).
- **Batch upload** (optional advanced feature — allow multiple images processed in a queue).
- **Confidence indicator** and manual correction field (if OCR misreads a character, user can edit and confirm).

---

## 3. TECH STACK (REQUIRED)

### Frontend
- **Next.js 14+ (App Router)** with **TypeScript**
- **Tailwind CSS** for styling
- **shadcn/ui** for accessible, composable UI components
- **Zustand** for lightweight global state (upload queue, auth state, history)
- **React Hook Form + Zod** for form validation (contact form, auth forms)
- **Framer Motion** for micro-interactions and page transitions
- **react-dropzone** for drag-and-drop image upload with camera capture support on mobile

### Backend / AI Pipeline
- **FastAPI (Python)** microservice dedicated to the ML/OCR pipeline, OR **Next.js API routes** calling out to the Python service — recommended architecture: separate Python inference service, Next.js as the web/app layer.
- **Plate Detection:** YOLOv8 (Ultralytics) fine-tuned/pre-trained for license plate detection, OR OpenCV Haar cascade fallback for lightweight deployments.
- **OCR Engine:** Tesseract OCR (pytesseract) as baseline; recommend **EasyOCR** or **PaddleOCR** for higher accuracy on Indian plates; architecture should allow swapping OCR engines behind an interface.
- **Image preprocessing:** OpenCV (grayscale, thresholding, perspective correction, noise removal) before OCR for accuracy.
- Expose the pipeline as a REST endpoint: `POST /api/detect` → accepts image, returns `{ plateNumber, confidence, boundingBox, processedImageUrl }`.

### Database & ORM
- **PostgreSQL** as the primary database
- **Prisma ORM** for schema management and queries
- Core tables: `User`, `DetectionRecord`, `Vehicle`, `Subscription/Enquiry`

### Auth
- **NextAuth.js** — email/password + Google OAuth. Roles: `CUSTOMER`, `ADMIN`.

### Storage & Media
- **Cloudinary** for storing uploaded vehicle images and processed/annotated result images.

### Transactional Email
- **Resend** for enquiry confirmations, OTPs, and report delivery emails.

### Payments (if paid plans/reports are offered)
- **Razorpay** integration for premium plan subscriptions or pay-per-report pricing.

### Deployment
- **Vercel** for the Next.js frontend
- **Railway / Render / Fly.io** for the Python FastAPI inference service (Vercel does not support heavy ML inference well)
- **Environment variables** managed via `.env.local` (never commit secrets)

### Other
- **TanStack Query (React Query)** for client-side data fetching/caching of API calls
- **Sharp** for server-side image optimization where applicable

---

## 4. NON-NEGOTIABLE PRODUCTION REQUIREMENTS

- **Fully responsive** — pixel-perfect on mobile (360px+), tablet, and desktop. Mobile-first Tailwind breakpoints (`sm`, `md`, `lg`, `xl`).
- **Mobile camera capture** support for the upload component (`<input type="file" accept="image/*" capture="environment">` fallback plus drag-drop on desktop).
- **Accessibility (WCAG AA):** semantic HTML, proper alt text, keyboard navigation, focus states, sufficient color contrast.
- **SEO:** proper meta tags, Open Graph tags, sitemap.xml, robots.txt, semantic heading hierarchy, Next.js metadata API.
- **Performance:** Lighthouse score 90+ on mobile — lazy-load images, use `next/image`, code-split, minimize bundle size.
- **Error handling:** graceful handling of failed uploads, low-confidence OCR results, network errors, unsupported file types, oversized files.
- **Loading states:** skeleton loaders and progress indicators during image processing (OCR can take a few seconds — communicate this clearly to the user with a processing animation).
- **Security:** input sanitization, file-type/size validation, rate-limiting on the detection API, HTTPS-only, environment secrets never exposed client-side.
- **Testing:** basic unit tests (Jest/Vitest) for utility functions and API routes; component tests with React Testing Library for critical flows (upload → result).
- **Clean, typed, well-commented codebase** — no `any` types in TypeScript unless unavoidable; consistent folder structure (`/app`, `/components`, `/lib`, `/server`, `/types`, `/prisma`).

---

## 5. DESIGN SYSTEM

Give this a **modern, tech-forward, trustworthy** identity — this is a security/AI product, so the visual language should feel precise, technical, and confident (think: dashboards, scanning UI, subtle grid/circuit motifs) rather than "cute."

- **Color palette:** Deep navy/charcoal base (`#0B1220` / `#101826`) with an **electric cyan/teal accent** (`#00D9C0` or `#22D3EE`) for scan-lines, highlights, and CTAs, plus a warm amber (`#FBBF24`) as a secondary accent for alerts/confidence scores. Light mode: off-white background (`#F8FAFC`) with the same navy/cyan accents for contrast.
- **Typography:** A clean geometric sans-serif for body/UI (e.g., **Inter** or **Space Grotesk**), and a slightly technical/monospace font (e.g., **JetBrains Mono**) for plate numbers, confidence scores, and data readouts — this reinforces the "scanning/recognition" feel.
- **Motifs:** subtle animated scan-line effect over the hero upload zone, bounding-box corner brackets (like camera focus UI) around image previews, dashed grid backgrounds, glassmorphism cards for stats.
- **Iconography:** lucide-react icons throughout (camera, scan, shield-check, upload, car, map-pin, phone).

---

## 6. SITE STRUCTURE & PAGES

### 6.1 Landing Page (`/`)
- **Header/Nav:** Logo "VNPR", nav links (Home, Features, How It Works, Pricing, Contact), mobile hamburger menu, "Try Now" CTA button.
- **Hero Section:** Bold headline (e.g., "Instant Number Plate Recognition — Powered by AI"), subheadline explaining the upload → OCR → info flow, primary CTA "Upload a Photo", secondary CTA "See How It Works". Include an interactive/animated visual — e.g., a sample car image with an animated scanning bounding box drawing itself around the plate.
- **Live Demo / Upload Widget:** A prominent drag-and-drop + camera-capture zone directly on the landing page so visitors can try it immediately without signing up (limit free trials, e.g., 3 per session via cookie/localStorage).
- **How It Works:** 3–4 step visual process (Upload → Detect → Extract → Get Info), each with icon + short description.
- **Features/Services section:**
  - Number Plate Detection & OCR Extraction
  - Vehicle Information Lookup
  - Detection History & Reports (downloadable PDF)
  - Batch Processing for fleets/businesses
  - High accuracy even in low light / angled shots
- **Stats/Trust bar:** "Established 1996", "45+ Daily Customers", "Serving Bardoli, Gujarat", "XX,XXX+ Plates Scanned" — displayed as animated counters.
- **Use Cases section:** Parking management, toll booths, fleet management, personal vehicle verification, security/surveillance.
- **Pricing section (if applicable):** Free tier (limited scans/day) vs Business tier (unlimited + API access) — Razorpay checkout integration.
- **Testimonials/Reviews:** carousel of customer feedback (use realistic placeholder testimonials tied to Bardoli-area businesses).
- **About/Owner section:** Brief on Kritesh Yadav and VNPR's establishment since 1996, based in Bardoli, India — build trust and locality.
- **Contact/Enquiry section:** Form (name, phone, email, message) + direct contact number (1234567890) + embedded map showing Bardoli, Gujarat location.
- **Footer:** Logo, quick links, social icons, contact info, address, copyright.

### 6.2 Try / Upload Page (`/detect`)
- Full-screen dedicated detection tool: upload/camera capture → processing animation → result card showing cropped plate image, extracted number (monospace, large, copyable), confidence %, and vehicle info panel.
- "Scan Another" and "Save to History" (if logged in) actions.
- Manual correction input if OCR confidence is below threshold.

### 6.3 Dashboard (`/dashboard`) — Authenticated
- Sidebar/topbar navigation (responsive → collapses to bottom nav or drawer on mobile).
- Detection history table/grid (thumbnail, plate number, date, confidence, action buttons).
- Download report as PDF.
- Account settings, plan/usage info.

### 6.4 Pricing Page (`/pricing`)
- Comparison table of Free vs Business plans, responsive card layout on mobile.

### 6.5 About Page (`/about`)
- Story of VNPR since 1996, Bardoli roots, mission, and technology explanation in plain language.

### 6.6 Contact Page (`/contact`)
- Full enquiry form, contact number, address, embedded Google Map, business hours.

### 6.7 Auth Pages (`/login`, `/signup`)
- Clean centered forms, social login (Google), password visibility toggle, form validation errors.

### 6.8 Legal Pages
- `/privacy-policy`, `/terms-of-service` — important since the product handles uploaded images/personal vehicle data.

---

## 7. DATABASE SCHEMA (Prisma — starting point)

```prisma
model User {
  id            String   @id @default(cuid())
  name          String
  email         String   @unique
  password      String?
  role          Role     @default(CUSTOMER)
  createdAt     DateTime @default(now())
  detections    DetectionRecord[]
}

enum Role {
  CUSTOMER
  ADMIN
}

model DetectionRecord {
  id              String   @id @default(cuid())
  userId          String?
  user            User?    @relation(fields: [userId], references: [id])
  originalImageUrl String
  processedImageUrl String
  plateNumber     String
  confidence      Float
  vehicleInfo     Json?
  createdAt       DateTime @default(now())
}

model Enquiry {
  id        String   @id @default(cuid())
  name      String
  phone     String
  email     String?
  message   String
  createdAt DateTime @default(now())
}
```

---

## 8. API ROUTES TO IMPLEMENT

| Route | Method | Purpose |
|---|---|---|
| `/api/detect` | POST | Accepts image, returns plate number + confidence + vehicle info |
| `/api/history` | GET | Fetch logged-in user's detection history |
| `/api/enquiry` | POST | Save contact form submission, send email via Resend |
| `/api/auth/*` | — | NextAuth.js handlers |
| `/api/report/[id]` | GET | Generate/download PDF report for a detection record |
| `/api/checkout` | POST | Razorpay order creation for paid plans |

---

## 9. MOBILE-SPECIFIC REQUIREMENTS

- Sticky bottom CTA bar on mobile landing page ("Try Now").
- Camera capture must default to rear camera (`capture="environment"`).
- Touch-friendly tap targets (min 44x44px).
- Result cards and dashboard tables must reflow into stacked cards on small screens — no horizontal scrolling of core content.
- Test on iOS Safari and Android Chrome viewport quirks (safe-area insets for notches).

---

## 10. DELIVERABLE EXPECTATIONS FOR THE AI CODING AGENT

1. Scaffold the Next.js 14 App Router project with TypeScript, Tailwind, and shadcn/ui configured.
2. Build the design system (colors, fonts, spacing tokens) in `tailwind.config.ts` and a `globals.css`.
3. Build all pages listed in Section 6, fully responsive, with realistic placeholder content using the business details provided (VNPR, Kritesh Yadav, Bardoli India, 1234567890, est. 1996, 45+ daily customers).
4. Implement the detection UI end-to-end on the frontend with a mocked/stubbed API response first (so the UI is fully demoable), then wire it to the real FastAPI OCR service.
5. Set up Prisma schema and migrations.
6. Set up NextAuth.js with email/password and Google provider.
7. Implement the Python FastAPI service separately with a documented `/detect` endpoint (YOLOv8 + OpenCV preprocessing + Tesseract/EasyOCR), including a `requirements.txt` and Dockerfile for deployment.
8. Write a `README.md` with full setup instructions for both the Next.js app and the Python service, environment variables needed, and deployment steps (Vercel for frontend, Railway/Render for the ML service).
9. Ensure the entire codebase passes `npm run build` with no errors and is deployment-ready.

Build this as if it will go live for real customers in Bardoli, India this month. Prioritize a polished, trustworthy first impression on the landing page and a fast, reliable core detection experience.