# Resume-API Feature Plan

**Title:** Feature Plan: Resume-API - Backend Service on Cloudflare Workers

## Overview
Core backend service providing APIs for resume management, document processing, AI features, and website generation using Cloudflare's serverless platform.

## Tech Stack
- **Platform**: Cloudflare Workers (Serverless)
- **Runtime**: TypeScript with Hono or itty-router
- **Database**: Cloudflare D1 (SQLite)
- **Storage**: Cloudflare R2
- **AI**: Cloudflare AI (Workers AI)
- **Queue**: Cloudflare Queues
- **Cache**: Cloudflare KV
- **Email**: Cloudflare Email Workers or SendGrid
- **Search**: Cloudflare Vectorize (for semantic search)

---

## Phase 1: MVP (FREE TIER) - Months 1-3

### Core API Endpoints

#### Resume Management
- [ ] **CRUD Operations**
  - `POST /api/resumes` - Create new resume
  - `GET /api/resumes` - List user's resumes
  - `GET /api/resumes/:id` - Get resume by ID
  - `PUT /api/resumes/:id` - Update resume
  - `DELETE /api/resumes/:id` - Delete resume
  - `POST /api/resumes/:id/duplicate` - Duplicate resume

- [ ] **Resume Data Model (D1)**
  - User ID (foreign key to SSO)
  - Resume metadata (title, created_at, updated_at)
  - Resume content (JSON structure)
  - Template ID
  - Version number

- [ ] **Auto-save System**
  - Debounced save mechanism
  - Optimistic updates
  - Conflict resolution

#### Template Management
- [ ] **Template APIs**
  - `GET /api/templates` - List all templates
  - `GET /api/templates/:id` - Get template by ID
  - Template storage in D1
  - Template preview images in R2

- [ ] **5 Base Templates**
  - Modern
  - Classic
  - ATS-Friendly
  - Minimalist
  - Professional

#### PDF Generation
- [ ] **Export Endpoint**
  - `POST /api/resumes/:id/export/pdf` - Generate PDF
  - Use Cloudflare Workers + Puppeteer or pdf-lib
  - HTML to PDF conversion
  - Custom styling preservation
  - Store generated PDFs in R2 (temporary)
  - Signed URL for download

- [ ] **PDF Processing Queue**
  - Use Cloudflare Queues for async processing
  - Handle large documents
  - Retry logic
  - Status tracking

#### Authentication Middleware
- [ ] JWT validation
- [ ] User context extraction
- [ ] Permission checking
- [ ] Rate limiting (per user)

#### Database Schema (D1)
```sql
-- Users table (synced from Resume-SSO)
CREATE TABLE users (
  id TEXT PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  created_at INTEGER NOT NULL,
  subscription_tier TEXT DEFAULT 'free'
);

-- Resumes table
CREATE TABLE resumes (
  id TEXT PRIMARY KEY,
  user_id TEXT NOT NULL,
  title TEXT NOT NULL,
  content TEXT NOT NULL, -- JSON
  template_id TEXT,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL,
  FOREIGN KEY (user_id) REFERENCES users(id)
);

-- Templates table
CREATE TABLE templates (
  id TEXT PRIMARY KEY,
  name TEXT NOT NULL,
  category TEXT NOT NULL,
  preview_url TEXT,
  html_template TEXT NOT NULL,
  css_styles TEXT NOT NULL,
  is_premium BOOLEAN DEFAULT 0
);
```

---

## Phase 2: Premium Features & Mobile Support - Months 4-6

### Payment Integration
- [ ] **Stripe Integration**
  - `POST /api/billing/create-checkout` - Create checkout session
  - `POST /api/billing/webhook` - Handle Stripe webhooks
  - `GET /api/billing/subscription` - Get subscription status
  - `POST /api/billing/cancel` - Cancel subscription
  - Store subscription data in D1

- [ ] **Feature Gating Middleware**
  - Check user tier before operations
  - Enforce limits (resume count, exports)
  - Return appropriate error codes

### Document Import
- [ ] **PDF Parsing**
  - `POST /api/import/pdf` - Upload and parse PDF
  - Extract text content
  - Structure detection (sections)
  - Use Cloudflare AI for intelligent parsing
  - Store uploads in R2

- [ ] **Word Document Parsing**
  - `POST /api/import/docx` - Upload and parse Word doc
  - mammoth.js for conversion
  - Preserve formatting
  - Extract structured data

- [ ] **LinkedIn Import**
  - `POST /api/import/linkedin` - Import from LinkedIn
  - OAuth flow integration
  - Profile data mapping
  - Experience and education parsing

### Advanced Export
- [ ] **Word Export**
  - `POST /api/resumes/:id/export/docx`
  - Use docx library
  - Template-based generation
  - Style preservation

- [ ] **Multi-format Export**
  - `POST /api/resumes/:id/export/batch`
  - Queue-based processing
  - ZIP file creation
  - Formats: PDF, DOCX, HTML, JSON

### Mobile API Support
- [ ] **Mobile-optimized Endpoints**
  - Reduced payload sizes
  - Pagination support
  - Field selection
  - Image optimization

- [ ] **Offline Sync**
  - Conflict resolution
  - Last-write-wins strategy
  - Version vectors

---

## Phase 3: Personal Website & AI Features - Months 7-9

### Website Generation
- [ ] **Static Site Generation**
  - `POST /api/websites/generate` - Generate website from resume
  - Convert resume to HTML/CSS/JS
  - Deploy to Cloudflare Pages via API
  - Custom subdomain assignment

- [ ] **Website Management**
  - `GET /api/websites/:id` - Get website details
  - `PUT /api/websites/:id` - Update website
  - `DELETE /api/websites/:id` - Delete website
  - `POST /api/websites/:id/publish` - Publish changes

- [ ] **Custom Domain**
  - DNS management via Cloudflare API
  - SSL certificate provisioning
  - Domain verification

### AI-Powered Features (Cloudflare AI)
- [ ] **Content Suggestions**
  - `POST /api/ai/suggest-bullets` - Generate bullet points
  - Use @cf/meta/llama-2-7b-chat-int8 or similar
  - Context-aware suggestions
  - Industry-specific content

- [ ] **Resume Analysis**
  - `POST /api/ai/analyze` - Analyze resume
  - ATS score calculation
  - Keyword extraction
  - Improvement suggestions

- [ ] **Smart Rewriting**
  - `POST /api/ai/rewrite` - Rewrite content
  - Tone adjustment
  - Grammar correction
  - Length optimization

### Collaboration
- [ ] **Sharing System**
  - `POST /api/resumes/:id/share` - Create share link
  - `GET /api/shared/:token` - Access shared resume
  - Permission levels (view, comment, edit)
  - Expiring links

- [ ] **Real-time Collaboration**
  - Use Cloudflare Durable Objects
  - WebSocket connections
  - Operational transformation
  - Presence detection

---

## Phase 4: Advanced Features - Months 10-12

### Analytics
- [ ] **Usage Tracking**
  - Resume views
  - Download counts
  - Website visits (via Cloudflare Analytics)
  - Store in D1

- [ ] **Analytics API**
  - `GET /api/analytics/resume/:id`
  - `GET /api/analytics/website/:id`
  - Aggregated metrics
  - Time-series data

### Advanced AI
- [ ] **Job Matching**
  - `POST /api/ai/match-job` - Match resume to job description
  - Similarity scoring
  - Skill gap analysis
  - Use Cloudflare Vectorize for embeddings

- [ ] **Cover Letter Generation**
  - `POST /api/ai/cover-letter` - Generate cover letter
  - Job-specific customization
  - Company research integration

### Search & Discovery
- [ ] **Semantic Search**
  - Use Cloudflare Vectorize
  - Search templates by description
  - Find similar resumes
  - Content recommendations

### Integrations
- [ ] **Job Boards**
  - Indeed API integration
  - LinkedIn Jobs
  - Application tracking

- [ ] **Cloud Storage**
  - Google Drive export
  - Dropbox sync
  - OneDrive integration

---

## Infrastructure & Operations

### Cloudflare Workers Setup
- [ ] **Worker Configuration**
  - Routes and bindings
  - Environment variables
  - Secrets management
  - Wrangler configuration

- [ ] **D1 Database**
  - Schema migrations
  - Backup strategy
  - Read replicas for scaling

- [ ] **R2 Storage**
  - Bucket organization
  - Lifecycle policies
  - CDN integration
  - Access control

### Performance & Scalability
- [ ] **Caching Strategy**
  - Cloudflare KV for templates
  - Cache headers for static content
  - Intelligent cache invalidation

- [ ] **Rate Limiting**
  - Per-user limits
  - Tier-based quotas
  - Cloudflare Rate Limiting

- [ ] **Queue Management**
  - Cloudflare Queues for async tasks
  - PDF generation queue
  - Email queue
  - Analytics processing

### Monitoring & Logging
- [ ] Workers Analytics
- [ ] Error tracking (Sentry)
- [ ] Performance monitoring
- [ ] Request logging

### Security
- [ ] CORS configuration
- [ ] API key validation
- [ ] Input sanitization
- [ ] SQL injection prevention
- [ ] XSS protection

---

## API Documentation
- [ ] OpenAPI/Swagger spec
- [ ] Interactive API docs
- [ ] Example requests/responses
- [ ] Authentication guide
- [ ] Rate limit documentation

---

## Free vs Premium API Limits

### Free Tier
- 2 resumes max
- 10 PDF exports/month
- Basic templates only
- No import capabilities
- 100 API requests/hour

### Pro Tier
- Unlimited resumes
- Unlimited exports
- All import formats
- AI suggestions (100/month)
- 1000 API requests/hour
- Personal website (1 site)

### Premium Tier
- Everything in Pro
- Unlimited AI features
- 5000 API requests/hour
- Multiple websites (5 sites)
- Custom domain support
- API access for integrations
