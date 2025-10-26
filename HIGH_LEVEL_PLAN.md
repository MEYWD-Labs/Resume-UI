# High-Level Development Plan - Resume-UI

**Service Type**: User-Facing Frontend Application (Next.js 14+ on Cloudflare Pages)
**Build Approach**: AI-Automated Development
**Priority**: HIGH - Primary user interface

---

## Dependency Tree Overview

```
Resume-UI (Web Frontend Application)
├── DEPENDS ON:
│   ├── Resume-SSO (authentication/session management)
│   ├── Resume-API (all backend operations)
│   └── Resume-Account-API (subscription management)
│
├── BLOCKS: None (end-user facing application)

Services that Resume-UI DEPENDS ON:
├── Resume-SSO (MVP-1: Authentication, OAuth)
├── Resume-API (MVP-1: Resume CRUD, templates, exports)
├── Resume-Account-API (MVP-2: Subscriptions, billing)
└── Resume-Processor (indirect: via Resume-API for jobs)

MVP Breakdown:
├── MVP-1: Core Resume Builder ⟶ Blocks: Nothing (user-facing)
│   ├── Authentication Integration (uses Resume-SSO)
│   ├── User Dashboard
│   ├── Resume Editor (WYSIWYG)
│   ├── Template System
│   └── Basic PDF Export UI
│
├── MVP-2: Payment UI & Feature Gating ⟶ Uses: Resume-Account-API
│   ├── Pricing Page
│   ├── Stripe Checkout Integration
│   ├── Subscription Management
│   └── Feature Gating (client-side)
│
├── MVP-3: Import & Advanced Editor ⟶ Uses: Resume-API import features
│   ├── Import UI (PDF, DOCX, LinkedIn)
│   ├── Advanced Template Customization
│   ├── Smart Content Suggestions
│   └── Multi-format Export UI
│
├── MVP-4: Personal Website UI ⟶ Uses: Resume-API website features
│   ├── Website Generator UI
│   ├── Website Customization
│   └── Domain Management
│
├── MVP-5: AI Features UI ⟶ Uses: Resume-API AI features
│   ├── AI Content Suggestions UI
│   ├── Resume Analysis Dashboard
│   └── Smart Rewriting Interface
│
└── MVP-6: Collaboration & Analytics UI ⟶ Uses: Resume-API collaboration
    ├── Sharing Interface
    ├── Real-time Collaboration UI
    └── Analytics Dashboard
```

---

## MVP-1: Core Resume Builder (Foundation)

**Status**: MUST BUILD AFTER Resume-SSO and Resume-API (MVP-1) are ready
**Dependencies**: Resume-SSO (MVP-1.4: Login), Resume-API (MVP-1.3: Resume CRUD)
**Blocks**: None (end-user facing UI)

### Epic 1.1: Authentication Integration
**What**: Integrate with Resume-SSO for user authentication
**Complexity**: Medium
**Dependencies**: Resume-SSO (MVP-1.4: Login with JWT)

**Tasks**:
- [ ] Next.js authentication setup with JWT
- [ ] `/login` page with email/password form
- [ ] `/signup` page with registration form
- [ ] `/verify-email` page for email verification
- [ ] Session management (store JWT in httpOnly cookies or localStorage)
- [ ] Protected route middleware
- [ ] `/forgot-password` and `/reset-password` pages
- [ ] OAuth buttons (Google, LinkedIn)
- [ ] User context provider
- [ ] Logout functionality

**Acceptance Criteria**:
- [ ] Users can register and log in
- [ ] JWT tokens stored securely
- [ ] Protected routes redirect to login
- [ ] OAuth flows work correctly
- [ ] Session persists across page reloads

---

### Epic 1.2: User Dashboard
**What**: Main landing page showing user's resumes
**Complexity**: Medium
**Dependencies**: Epic 1.1 (Authentication), Resume-API (MVP-1.3: Resume CRUD)

**Tasks**:
- [ ] `/dashboard` page layout
- [ ] Fetch and display user's resumes from API
- [ ] Resume card component (title, last edited, template preview)
- [ ] "Create New Resume" button
- [ ] Duplicate resume functionality
- [ ] Delete resume with confirmation modal
- [ ] Empty state when no resumes
- [ ] Loading states and skeletons
- [ ] Error handling and retry

**Acceptance Criteria**:
- [ ] Dashboard shows all user resumes
- [ ] Users can create, duplicate, and delete resumes
- [ ] Last edited timestamp displayed
- [ ] Responsive design (mobile, tablet, desktop)
- [ ] Free tier limited to 2 resumes (show upgrade prompt)

---

### Epic 1.3: Resume Editor (WYSIWYG)
**What**: Real-time resume editing interface
**Complexity**: Large
**Dependencies**: Epic 1.2 (Dashboard), Resume-API (MVP-1.3: Resume CRUD)

**Tasks**:
- [ ] `/resumes/:id/edit` page layout
- [ ] Split-pane layout (editor left, preview right)
- [ ] WYSIWYG rich text editor (TipTap or Slate)
- [ ] Resume section components:
  - [ ] Contact Information form
  - [ ] Professional Summary editor
  - [ ] Work Experience (multiple entries, drag-to-reorder)
  - [ ] Education (multiple entries)
  - [ ] Skills (tags input)
  - [ ] Custom sections
- [ ] Drag-and-drop section reordering
- [ ] Rich text formatting toolbar (bold, italic, lists, links)
- [ ] Auto-save functionality (debounced to API)
- [ ] Saving indicator (saving, saved, error)
- [ ] Undo/redo functionality
- [ ] Keyboard shortcuts (Ctrl+S to save, Ctrl+Z undo)

**Acceptance Criteria**:
- [ ] Real-time editing with preview
- [ ] Auto-save works without user intervention
- [ ] All resume sections editable
- [ ] Drag-and-drop reordering works
- [ ] Rich text formatting preserved
- [ ] Responsive editor on tablet/desktop

---

### Epic 1.4: Template System
**What**: Template selection and preview
**Complexity**: Medium
**Dependencies**: Epic 1.3 (Editor), Resume-API (MVP-1.4: Templates)

**Tasks**:
- [ ] Template gallery page/modal
- [ ] Fetch templates from API
- [ ] Template preview cards with thumbnails
- [ ] Template categories (Modern, Classic, ATS-Friendly, etc.)
- [ ] One-click template switching (updates resume in editor)
- [ ] Template preview in resume editor
- [ ] Basic color customization (color picker)
- [ ] Font selection dropdown (3-5 font options)
- [ ] Premium template indicators (lock icon for free users)

**Acceptance Criteria**:
- [ ] Template gallery displays all templates
- [ ] Users can switch templates
- [ ] Template changes reflect in preview instantly
- [ ] Free users see only free templates (5 templates)
- [ ] Pro users see all templates
- [ ] Color and font customization works

---

### Epic 1.5: Basic PDF Export UI
**What**: UI for downloading resume as PDF
**Complexity**: Small
**Dependencies**: Epic 1.3 (Editor), Resume-API (MVP-1.5: PDF Export)

**Tasks**:
- [ ] "Export PDF" button in editor toolbar
- [ ] Call API endpoint to generate PDF
- [ ] Loading state during PDF generation
- [ ] Progress indicator (if using queue system)
- [ ] Download PDF automatically when ready
- [ ] Export limit tracking (10/month for free tier)
- [ ] Show remaining exports to user
- [ ] Upgrade prompt when limit reached

**Acceptance Criteria**:
- [ ] Users can export resume as PDF
- [ ] PDF downloads automatically
- [ ] Free tier export limit enforced (10/month)
- [ ] Loading states during generation
- [ ] Error handling for failed exports

---

### Epic 1.6: Responsive Design & UI Polish
**What**: Ensure application works on all devices
**Complexity**: Medium
**Dependencies**: All Epic 1 tasks

**Tasks**:
- [ ] Mobile-optimized layouts (< 768px)
- [ ] Tablet-optimized layouts (768px - 1024px)
- [ ] Desktop layouts (> 1024px)
- [ ] Component library setup (Radix UI or shadcn/ui)
- [ ] Dark mode support (toggle and persist preference)
- [ ] Accessibility audit (WCAG 2.1 AA)
- [ ] Loading skeletons for all async operations
- [ ] Error boundaries and error pages (404, 500)
- [ ] Consistent design tokens (colors, spacing, typography)

**Acceptance Criteria**:
- [ ] Application works on mobile, tablet, desktop
- [ ] Dark mode toggle works
- [ ] Accessibility score > 90 (Lighthouse)
- [ ] Consistent UI across all pages
- [ ] Error states handled gracefully

---

## MVP-2: Payment UI & Feature Gating (Premium Enablement)

**Status**: Required for monetization
**Dependencies**: MVP-1, Resume-API (MVP-2: Stripe Integration)
**Blocks**: All premium features in UI

### Epic 2.1: Pricing Page
**What**: Public pricing page with tier comparison
**Complexity**: Small
**Dependencies**: None (public page)

**Tasks**:
- [ ] `/pricing` page layout
- [ ] Pricing tier cards (Free, Pro, Premium)
- [ ] Feature comparison table
- [ ] "Get Started" / "Upgrade" CTAs
- [ ] Testimonials section
- [ ] FAQ section
- [ ] Responsive design

**Acceptance Criteria**:
- [ ] Pricing page displays all tiers
- [ ] Feature comparison clear and accurate
- [ ] CTAs link to signup or checkout
- [ ] Mobile-friendly layout

---

### Epic 2.2: Stripe Checkout Integration
**What**: Payment flow for subscriptions
**Complexity**: Large
**Dependencies**: Epic 2.1 (Pricing), Resume-API (MVP-2.1: Stripe Integration)

**Tasks**:
- [ ] "Upgrade" button in dashboard and editor
- [ ] Redirect to Stripe Checkout (via API)
- [ ] Handle Stripe checkout success callback
- [ ] Handle Stripe checkout cancel callback
- [ ] Update user subscription status in UI
- [ ] Show success message and redirect to dashboard
- [ ] Error handling for failed payments

**Acceptance Criteria**:
- [ ] Users can upgrade to Pro/Premium
- [ ] Stripe checkout flow works end-to-end
- [ ] Success and cancel callbacks handled
- [ ] Subscription status updates in UI
- [ ] Error messages for payment failures

---

### Epic 2.3: Subscription Management
**What**: UI for managing active subscriptions
**Complexity**: Medium
**Dependencies**: Epic 2.2 (Stripe Integration), Resume-API (MVP-2.1: Stripe)

**Tasks**:
- [ ] `/settings/billing` page
- [ ] Display current subscription tier
- [ ] Show billing cycle and next payment date
- [ ] "Cancel Subscription" button with confirmation
- [ ] "Update Payment Method" button (redirect to Stripe portal)
- [ ] Billing history table
- [ ] Download invoices

**Acceptance Criteria**:
- [ ] Users can view subscription status
- [ ] Users can cancel subscriptions
- [ ] Users can update payment methods
- [ ] Billing history displays correctly
- [ ] Invoices downloadable

---

### Epic 2.4: Feature Gating (Client-side)
**What**: Hide/disable premium features for free users
**Complexity**: Small
**Dependencies**: Epic 2.2 (Subscription Status)

**Tasks**:
- [ ] Check user subscription tier in React context
- [ ] Conditionally render premium features
- [ ] Show "Upgrade to Pro" prompts on premium features
- [ ] Disable premium template selection for free users
- [ ] Disable import buttons for free users
- [ ] Show feature locks with upgrade CTAs
- [ ] Toast notifications when attempting premium actions

**Acceptance Criteria**:
- [ ] Free users cannot access premium features
- [ ] Premium features show upgrade prompts
- [ ] Pro/Premium users have full access
- [ ] Feature locks visually clear

---

## MVP-3: Import & Advanced Editor (User Experience)

**Status**: Enhances user onboarding and productivity
**Dependencies**: MVP-2, Resume-API (MVP-3: Import Features)
**Blocks**: Advanced editing capabilities

### Epic 3.1: Import UI (PDF, DOCX, LinkedIn)
**What**: UI for importing resumes from various sources
**Complexity**: Large
**Dependencies**: Epic 2.4 (Feature Gating), Resume-API (MVP-3.1, 3.2, 3.3)

**Tasks**:
- [ ] Import modal/page with tabs (PDF, DOCX, LinkedIn)
- [ ] File upload component (drag-and-drop)
- [ ] Upload progress indicator
- [ ] Call API import endpoints
- [ ] Parse and populate resume from import data
- [ ] Handle import errors and validation
- [ ] Preview imported data before saving
- [ ] LinkedIn OAuth flow (button to connect)
- [ ] Import history/log

**Acceptance Criteria**:
- [ ] Users can upload PDF and DOCX files
- [ ] Files parsed and resume populated
- [ ] LinkedIn import works via OAuth
- [ ] Preview before saving
- [ ] Only available to Pro/Premium users
- [ ] Error handling for unsupported files

---

### Epic 3.2: Advanced Template Customization
**What**: Enhanced customization options for templates
**Complexity**: Medium
**Dependencies**: Epic 1.4 (Template System)

**Tasks**:
- [ ] 15+ premium templates added to gallery
- [ ] Advanced color scheme editor (primary, secondary, accent)
- [ ] Layout variants (1-column, 2-column, 3-column)
- [ ] Margin and spacing controls (sliders)
- [ ] Section visibility toggles
- [ ] Custom CSS input (Premium tier only)
- [ ] Template settings panel in editor
- [ ] Real-time preview of customizations

**Acceptance Criteria**:
- [ ] Pro users access 15+ templates
- [ ] Advanced color customization works
- [ ] Layout variants selectable
- [ ] Margin/spacing adjustable
- [ ] Custom CSS works (Premium only)
- [ ] Real-time preview updates

---

### Epic 3.3: Smart Content Suggestions
**What**: AI-powered content assistance in editor
**Complexity**: Medium
**Dependencies**: Epic 1.3 (Editor), Resume-API (MVP-5.1: AI Suggestions)

**Tasks**:
- [ ] "Suggest" button next to experience bullet points
- [ ] AI suggestions modal/sidebar
- [ ] Call API for AI bullet points
- [ ] Display multiple suggestions (5-10)
- [ ] One-click insert suggestion into editor
- [ ] Grammar and spell check integration
- [ ] Action verb recommendations
- [ ] Industry-specific examples library
- [ ] Usage tracking (100 suggestions/month Pro)

**Acceptance Criteria**:
- [ ] AI generates relevant bullet points
- [ ] Suggestions insertable with one click
- [ ] Grammar/spell check highlights errors
- [ ] Action verbs suggested
- [ ] Only available to Pro/Premium users
- [ ] Usage limit enforced (100/month Pro)

---

### Epic 3.4: Multi-format Export UI
**What**: UI for exporting in multiple formats
**Complexity**: Small
**Dependencies**: Epic 1.5 (PDF Export), Resume-API (MVP-3.4: Multi-format Export)

**Tasks**:
- [ ] Export dropdown menu (PDF, DOCX, HTML, JSON)
- [ ] Batch export option (all formats at once)
- [ ] Call API export endpoints
- [ ] Download individual files or ZIP
- [ ] Export format selection modal
- [ ] Loading states for each format
- [ ] Export history (show recent exports)

**Acceptance Criteria**:
- [ ] Users can export in DOCX, HTML, JSON
- [ ] Batch export downloads ZIP file
- [ ] All formats download correctly
- [ ] Only available to Pro/Premium users

---

## MVP-4: Personal Website UI (Premium Feature)

**Status**: Premium user value-add
**Dependencies**: MVP-3, Resume-API (MVP-4: Website Generation)
**Blocks**: Personal website features

### Epic 4.1: Website Generator UI
**What**: UI for creating personal websites from resumes
**Complexity**: Large
**Dependencies**: Epic 1.3 (Resume Editor), Resume-API (MVP-4.1: Site Generation)

**Tasks**:
- [ ] "Create Website" button in dashboard
- [ ] Website builder wizard/modal
- [ ] Select website template (gallery view)
- [ ] Preview website with resume data
- [ ] Custom subdomain input (username.resumebuilder.dev)
- [ ] Call API to generate and deploy website
- [ ] Progress indicator for deployment
- [ ] Success message with website URL
- [ ] "Visit Website" button

**Acceptance Criteria**:
- [ ] Users can create website from resume
- [ ] Website templates preview correctly
- [ ] Subdomain customizable
- [ ] Website deployed to Cloudflare Pages
- [ ] Pro users: 1 website, Premium users: 5 websites
- [ ] Success notification with live URL

---

### Epic 4.2: Website Customization
**What**: Edit and customize personal websites
**Complexity**: Medium
**Dependencies**: Epic 4.1 (Website Generator), Resume-API (MVP-4.2: Website Management)

**Tasks**:
- [ ] `/websites/:id/edit` page
- [ ] Website editor with sections:
  - [ ] Portfolio/projects
  - [ ] Blog posts
  - [ ] Contact form
  - [ ] Social media links
- [ ] Theme customization (colors, fonts)
- [ ] Layout options
- [ ] SEO settings (meta title, description, keywords)
- [ ] "Publish Changes" button
- [ ] Live preview in iframe
- [ ] Version history view

**Acceptance Criteria**:
- [ ] Website sections editable
- [ ] Theme customization works
- [ ] SEO settings configurable
- [ ] Changes publish correctly
- [ ] Live preview accurate
- [ ] Version history accessible

---

### Epic 4.3: Domain Management UI
**What**: UI for connecting custom domains
**Complexity**: Medium
**Dependencies**: Epic 4.2 (Website Customization), Resume-API (MVP-4.3: Custom Domain)

**Tasks**:
- [ ] "Custom Domain" settings page
- [ ] Domain input field with validation
- [ ] DNS configuration instructions
- [ ] Domain verification status indicator
- [ ] SSL certificate status
- [ ] "Verify Domain" button
- [ ] Error messages for failed verification
- [ ] Remove custom domain option

**Acceptance Criteria**:
- [ ] Users can add custom domain
- [ ] DNS instructions clear
- [ ] Domain verification works
- [ ] SSL status displayed
- [ ] Only available to Premium users

---

## MVP-5: AI Features UI (Premium Enhancement)

**Status**: AI-powered resume enhancement
**Dependencies**: MVP-4, Resume-API (MVP-5: AI Features)
**Blocks**: AI-assisted features

### Epic 5.1: AI Content Suggestions UI
**What**: Enhanced AI content generation interface
**Complexity**: Medium
**Dependencies**: Epic 3.3 (Basic AI Suggestions), Resume-API (MVP-5.1)

**Tasks**:
- [ ] AI sidebar panel in editor
- [ ] Context-aware suggestions based on job title
- [ ] Industry selector for tailored content
- [ ] Multiple suggestion variants (5-10)
- [ ] Rating system for suggestions (thumbs up/down)
- [ ] Suggestion history
- [ ] "Regenerate" button for new suggestions
- [ ] Usage meter (100/month Pro, unlimited Premium)

**Acceptance Criteria**:
- [ ] AI panel accessible in editor
- [ ] Suggestions contextually relevant
- [ ] Industry-specific content works
- [ ] Users can rate suggestions
- [ ] Usage limits enforced
- [ ] Only available to Pro/Premium users

---

### Epic 5.2: Resume Analysis Dashboard
**What**: ATS compatibility and optimization scoring
**Complexity**: Large
**Dependencies**: Epic 1.3 (Editor), Resume-API (MVP-5.2: Resume Analysis)

**Tasks**:
- [ ] "Analyze Resume" button in editor
- [ ] Analysis dashboard modal/page
- [ ] ATS score display (0-100 with gauge)
- [ ] Keyword extraction and matching
- [ ] Section completeness checklist
- [ ] Improvement suggestions with priority (high, medium, low)
- [ ] Industry benchmark comparison
- [ ] Re-analyze button after changes

**Acceptance Criteria**:
- [ ] ATS score calculated and displayed
- [ ] Keywords extracted and highlighted
- [ ] Suggestions actionable and clear
- [ ] Re-analysis reflects changes
- [ ] Only available to Pro/Premium users

---

### Epic 5.3: Smart Rewriting Interface
**What**: AI-powered content rewriting
**Complexity**: Medium
**Dependencies**: Epic 5.1 (AI UI), Resume-API (MVP-5.3: Smart Rewriting)

**Tasks**:
- [ ] "Rewrite" button in rich text editor
- [ ] Rewriting options modal:
  - [ ] Tone adjustment (professional, casual, formal)
  - [ ] Length control (shorten, expand)
  - [ ] Grammar correction only
- [ ] Call API rewrite endpoint
- [ ] Display rewritten content
- [ ] Accept/reject rewrite options
- [ ] Compare original vs rewritten side-by-side

**Acceptance Criteria**:
- [ ] Content rewritten maintaining meaning
- [ ] Tone adjustment works correctly
- [ ] Length optimization functional
- [ ] Users can accept or reject rewrites
- [ ] Only available to Premium users

---

## MVP-6: Collaboration & Analytics UI (Advanced Features)

**Status**: Advanced user features
**Dependencies**: MVP-5, Resume-API (MVP-6: Collaboration & Analytics)
**Blocks**: Collaboration and analytics features

### Epic 6.1: Sharing Interface
**What**: Share resumes with others
**Complexity**: Medium
**Dependencies**: Epic 1.3 (Editor), Resume-API (MVP-6.1: Sharing)

**Tasks**:
- [ ] "Share" button in editor toolbar
- [ ] Share settings modal:
  - [ ] Permission levels (view, comment, edit)
  - [ ] Link expiration dropdown (1 day, 7 days, 30 days, never)
  - [ ] Generate share link button
- [ ] Copy link to clipboard
- [ ] Share link QR code
- [ ] Revoke share link option
- [ ] View share access log

**Acceptance Criteria**:
- [ ] Share links generated correctly
- [ ] Permission levels enforced
- [ ] Links expire as configured
- [ ] Users can revoke access
- [ ] Access log displays views

---

### Epic 6.2: Real-time Collaboration UI
**What**: Multiple users editing same resume
**Complexity**: Large
**Dependencies**: Epic 6.1 (Sharing), Resume-API (MVP-6.2: Real-time Collaboration)

**Tasks**:
- [ ] WebSocket connection to API (Durable Objects)
- [ ] Presence indicators (show active editors)
- [ ] Cursor position sharing (show other users' cursors)
- [ ] Real-time text synchronization
- [ ] Change highlighting (different colors per user)
- [ ] Comment threads on sections
- [ ] Notification system for changes
- [ ] Conflict resolution UI

**Acceptance Criteria**:
- [ ] Multiple users can edit simultaneously
- [ ] Changes sync in real-time
- [ ] Presence indicators work correctly
- [ ] Cursors visible for all users
- [ ] Comments threaded correctly
- [ ] Only available to Premium users

---

### Epic 6.3: Analytics Dashboard
**What**: Resume and website usage analytics
**Complexity**: Medium
**Dependencies**: Epic 1.2 (Dashboard), Resume-API (MVP-6.3: Analytics)

**Tasks**:
- [ ] `/analytics` page or tab in dashboard
- [ ] Resume analytics cards:
  - [ ] Total views
  - [ ] Download count
  - [ ] Share count
- [ ] Website analytics cards:
  - [ ] Unique visitors
  - [ ] Page views
  - [ ] Top pages
- [ ] Time-series charts (views over time)
- [ ] Geographic distribution map
- [ ] Device breakdown chart
- [ ] Export analytics report (CSV)

**Acceptance Criteria**:
- [ ] Resume analytics display correctly
- [ ] Website analytics integrated
- [ ] Charts and graphs render properly
- [ ] Data updates in real-time
- [ ] Only available to Pro/Premium users

---

## Critical Path (Build Order)

1. **MVP-1.1**: Authentication Integration ⟶ Foundation (blocks all user features)
2. **MVP-1.2**: User Dashboard ⟶ Landing page after login
3. **MVP-1.3**: Resume Editor ⟶ Core functionality
4. **MVP-1.4**: Template System ⟶ Resume styling
5. **MVP-1.5**: PDF Export UI ⟶ Essential user feature
6. **MVP-1.6**: Responsive Design ⟶ Mobile/tablet support
7. **MVP-2.1**: Pricing Page ⟶ Monetization awareness
8. **MVP-2.2**: Stripe Checkout ⟶ Enable payments
9. **MVP-2.3**: Subscription Management ⟶ User control
10. **MVP-2.4**: Feature Gating ⟶ Enforce tier limits
11. **MVP-3.1**: Import UI ⟶ User onboarding
12. **MVP-3.2**: Advanced Customization ⟶ Template flexibility
13. **MVP-3.3**: Smart Content ⟶ AI assistance
14. **MVP-3.4**: Multi-format Export ⟶ Export flexibility
15. **MVP-4.1**: Website Generator ⟶ Premium feature
16. **MVP-4.2**: Website Customization ⟶ Website editing
17. **MVP-4.3**: Domain Management ⟶ Premium enhancement
18. **MVP-5.1**: AI Content UI ⟶ AI value-add
19. **MVP-5.2**: Resume Analysis ⟶ ATS optimization
20. **MVP-5.3**: Smart Rewriting ⟶ Premium AI feature
21. **MVP-6.1**: Sharing Interface ⟶ Collaboration foundation
22. **MVP-6.2**: Real-time Collaboration ⟶ Advanced collaboration
23. **MVP-6.3**: Analytics Dashboard ⟶ User insights

---

## What Blocks What

Note: Resume-UI is an end-user facing application and doesn't block other services. It depends on backend services.

| This Epic | Depends On These Services |
|-----------|--------------------------|
| MVP-1.1 (Authentication) | Resume-SSO (MVP-1.4: Login) |
| MVP-1.2 (Dashboard) | Resume-API (MVP-1.3: Resume CRUD) |
| MVP-1.3 (Editor) | Resume-API (MVP-1.3: Resume CRUD) |
| MVP-1.4 (Templates) | Resume-API (MVP-1.4: Templates) |
| MVP-1.5 (PDF Export UI) | Resume-API (MVP-1.5: PDF Export) |
| MVP-2.2 (Stripe Checkout) | Resume-Account-API (MVP-2: Billing) |
| MVP-2.3 (Subscription) | Resume-Account-API (MVP-2.2: Subscriptions) |
| MVP-2.4 (Feature Gating) | Resume-Account-API (MVP-2.2: Subscriptions) |
| MVP-3.1 (Import UI) | Resume-API (MVP-3: Import Features) |
| MVP-3.3 (Smart Content) | Resume-API (MVP-5.1: AI Suggestions) |
| MVP-4.1 (Website Generator) | Resume-API (MVP-4: Website Generation) |
| MVP-5.1 (AI Content UI) | Resume-API (MVP-5: AI Features) |
| MVP-5.2 (Analysis UI) | Resume-API (MVP-5.2: ATS Analysis) |
| MVP-6.1 (Sharing) | Resume-API (MVP-6.1: Sharing) |
| MVP-6.2 (Collaboration) | Resume-API (MVP-6.2: Real-time) |
| MVP-6.3 (Analytics) | Resume-API (MVP-6.3: Analytics) |

**External Dependencies Required**:
- Resume-SSO (MVP-1.4: Login) ⟶ Required for Epic 1.1 (Authentication Integration)
- Resume-SSO (MVP-3: OAuth) ⟶ Required for OAuth login buttons
- Resume-API (MVP-1: Core) ⟶ Required for all resume operations
- Resume-Account-API (MVP-2: Billing) ⟶ Required for subscription features
- Resume-Processor (indirect via Resume-API) ⟶ Handles background jobs

---

## Testing Strategy

### Unit Tests
- React component rendering
- Form validation
- State management (Zustand stores)
- Utility functions

### Integration Tests
- Authentication flow (login, signup, logout)
- Resume CRUD operations
- Template switching
- PDF export flow
- Payment flow

### E2E Tests (Playwright)
- Full user journey: signup → create resume → export PDF
- Payment flow: upgrade → checkout → success
- Import flow: upload PDF → parse → populate resume
- Collaboration flow: share → edit together → sync changes

### Performance Tests
- Lighthouse audits (Performance, Accessibility, SEO)
- Bundle size analysis
- Load time metrics
- Real-user monitoring (RUM)

### Accessibility Tests
- WCAG 2.1 AA compliance
- Screen reader testing
- Keyboard navigation
- Color contrast ratios

---

## Performance Targets

- First Contentful Paint (FCP): < 1.5s
- Time to Interactive (TTI): < 3s
- Largest Contentful Paint (LCP): < 2.5s
- Cumulative Layout Shift (CLS): < 0.1
- Lighthouse Performance Score: > 90
- Initial bundle size: < 200KB (gzipped)

---

## Environment Variables

```bash
# API Configuration
NEXT_PUBLIC_API_URL=https://api.resumebuilder.com
NEXT_PUBLIC_SSO_URL=https://sso.resumebuilder.com

# Stripe Configuration
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=<stripe-publishable-key>

# Feature Flags
NEXT_PUBLIC_ENABLE_AI_FEATURES=true
NEXT_PUBLIC_ENABLE_COLLABORATION=true
NEXT_PUBLIC_ENABLE_ANALYTICS=true

# Analytics
NEXT_PUBLIC_GA_TRACKING_ID=<google-analytics-id>
```

---

## Success Metrics

- [ ] 100% test coverage for critical paths
- [ ] Lighthouse Performance Score > 90
- [ ] Accessibility Score > 90
- [ ] < 3s page load time (p95)
- [ ] < 5% error rate
- [ ] > 95% user satisfaction (NPS)
- [ ] Zero critical security vulnerabilities
- [ ] Mobile usage > 40% of total
