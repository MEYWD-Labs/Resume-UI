# Resume-UI

**User-Facing Frontend Application** for the Resume Builder SaaS platform.

## Overview

Resume-UI is the primary user interface built with Next.js 14+ (App Router) and React 19. It provides a modern, responsive, and intuitive experience for creating, managing, and sharing professional resumes.

## Technology Stack

- **Framework**: Next.js 14+ (App Router)
- **React**: React 19 with Server Components
- **Language**: TypeScript
- **Styling**: Tailwind CSS
- **UI Components**: shadcn/ui
- **Forms**: React Hook Form + Zod validation
- **State Management**: Zustand + TanStack Query
- **Editor**: TipTap or Slate (WYSIWYG)
- **Real-time**: WebSockets (Durable Objects)
- **Payments**: Stripe Checkout
- **Deployment**: Cloudflare Pages

## Key Features

### MVP-1: Core Resume Builder
- User authentication (JWT-based sessions)
- Dashboard with resume management
- WYSIWYG resume editor
- Template selection and preview
- Section management (drag-and-drop)
- Real-time auto-save
- Multi-resume support

### MVP-2: Payment UI & Feature Gating
- Subscription tier display
- Stripe Checkout integration
- Feature upgrade prompts
- Billing management portal
- Usage tracking display

### MVP-3: Import & Advanced Editor
- PDF/DOCX file upload
- LinkedIn OAuth import
- Advanced formatting toolbar
- Custom sections and fields
- Rich text editing
- Image upload and management

### MVP-4: Personal Website UI
- Website generator interface
- Theme selection and customization
- Custom domain setup
- Website preview
- Publish/unpublish controls

### MVP-5: AI Features UI
- AI content suggestion panel
- Resume analysis dashboard (ATS scoring)
- Grammar and style checker
- Job description tailoring interface
- Keyword optimization recommendations

### MVP-6: Collaboration & Analytics UI
- Share modal with permission controls
- Real-time collaboration indicators
- Version history timeline
- Analytics dashboard (views, downloads)
- Activity feed

## Service Dependencies

**Depends On**:
- **Resume-SSO** (MVP-1.4: Login) - For user authentication and session management
- **Resume-API** (MVP-1.3: Resume CRUD) - For all backend operations

**No services depend on Resume-UI** - This is an end-user application.

## Application Routes

### Public Routes
- `/` - Landing page
- `/pricing` - Pricing plans
- `/features` - Feature showcase
- `/templates` - Template gallery
- `/login` - Sign in page
- `/register` - Sign up page
- `/forgot-password` - Password reset
- `/verify-email` - Email verification

### Protected Routes (Authenticated)
- `/dashboard` - User dashboard
- `/resumes` - Resume list
- `/resumes/new` - Create new resume
- `/resumes/:id/edit` - Resume editor
- `/resumes/:id/preview` - Resume preview
- `/templates` - Template browser
- `/account` - Account settings
- `/account/billing` - Subscription management
- `/account/security` - Security settings

### Premium Routes
- `/websites/:resumeId` - Website generator (Pro+)
- `/ai/analyze` - AI resume analysis (Pro+)
- `/collaborate/:resumeId` - Collaboration (Premium)

## Development Setup

### Prerequisites
- Node.js 18+
- npm or pnpm
- Access to Resume-SSO and Resume-API services

### Environment Variables

Create a `.env.local` file:

```bash
# API Configuration
NEXT_PUBLIC_SSO_URL=http://localhost:8787
NEXT_PUBLIC_API_URL=http://localhost:8788

# Stripe
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_test_...

# Feature Flags
NEXT_PUBLIC_ENABLE_AI_FEATURES=true
NEXT_PUBLIC_ENABLE_COLLABORATION=true

# Analytics
NEXT_PUBLIC_ANALYTICS_ID=<cloudflare-analytics-id>
```

### Quick Start

```bash
# Install dependencies
npm install

# Run development server
npm run dev

# Build for production
npm run build

# Start production server
npm start

# Run tests
npm test

# Run E2E tests
npm run test:e2e
```

## Project Structure

```
src/
├── app/                  # Next.js 14 App Router
│   ├── (auth)/          # Auth routes (login, register)
│   ├── (dashboard)/     # Protected dashboard routes
│   ├── (marketing)/     # Public marketing pages
│   └── api/             # API routes (proxy to backend)
├── components/          # React components
│   ├── ui/             # shadcn/ui components
│   ├── editor/         # Resume editor components
│   ├── dashboard/      # Dashboard components
│   └── shared/         # Shared components
├── lib/                # Utility functions
│   ├── api.ts          # API client
│   ├── auth.ts         # Auth helpers
│   └── utils.ts        # General utilities
├── hooks/              # Custom React hooks
├── stores/             # Zustand stores
└── types/              # TypeScript types
```

## State Management

### Zustand Stores
- `useAuthStore` - Authentication state (user, token)
- `useResumeStore` - Resume editing state
- `useTemplateStore` - Template selection
- `useCollaborationStore` - Real-time collaboration

### TanStack Query
- Server state caching and synchronization
- Automatic background refetching
- Optimistic updates for auto-save
- Infinite scroll for resume list

## Authentication Flow

1. User logs in via Resume-SSO
2. JWT tokens stored in httpOnly cookies
3. Middleware validates tokens on protected routes
4. Token refresh handled automatically
5. User context available via `useAuth()` hook

## Auto-Save Implementation

```typescript
// Debounced auto-save every 2 seconds
const { mutate: saveResume } = useMutation({
  mutationFn: (data) => api.updateResume(resumeId, data),
  onSuccess: () => toast.success('Saved'),
  onError: () => toast.error('Save failed')
});

const debouncedSave = useDebouncedCallback(saveResume, 2000);
```

## Real-time Collaboration

- WebSocket connection to Durable Objects
- Operational Transform (OT) for conflict resolution
- User presence indicators
- Live cursor positions
- Activity notifications

## Performance Optimizations

- Server Components for static content
- Streaming SSR for dynamic data
- Image optimization with Next.js Image
- Route prefetching
- Component code splitting
- Bundle analysis and optimization

## Performance Targets

- Initial page load: **< 2s** (LCP)
- Editor load time: **< 1s**
- Auto-save latency: **< 200ms**
- Template preview: **< 500ms**
- First Contentful Paint (FCP): **< 1s**

## Accessibility

- WCAG 2.1 Level AA compliance
- Keyboard navigation support
- Screen reader friendly
- Color contrast ratio: 4.5:1 minimum
- Focus indicators on all interactive elements
- ARIA labels and roles

## Testing

```bash
# Run unit tests
npm test

# Run E2E tests (Playwright)
npm run test:e2e

# Run accessibility tests
npm run test:a11y

# Visual regression tests
npm run test:visual

# Test coverage
npm run test:coverage
```

## Deployment

```bash
# Build and deploy to Cloudflare Pages
npm run deploy

# Preview deployment
npm run deploy:preview

# Production deployment
npm run deploy:production
```

## Development Plan

See [HIGH_LEVEL_PLAN.md](./HIGH_LEVEL_PLAN.md) for the complete dependency-tree based development plan with MVPs, epics, and critical path.

## GitHub Issues

Track development progress in [GitHub Issues](https://github.com/MEYWD-Labs/Resume-UI/issues):
- [MVP-1 Epics](https://github.com/MEYWD-Labs/Resume-UI/issues?q=is%3Aissue+is%3Aopen+MVP-1) - Core Resume Builder
- [MVP-2 Epics](https://github.com/MEYWD-Labs/Resume-UI/issues?q=is%3Aissue+is%3Aopen+MVP-2) - Payment UI & Feature Gating
- [MVP-3 Epics](https://github.com/MEYWD-Labs/Resume-UI/issues?q=is%3Aissue+is%3Aopen+MVP-3) - Import & Advanced Editor
- [MVP-4 Epics](https://github.com/MEYWD-Labs/Resume-UI/issues?q=is%3Aissue+is%3Aopen+MVP-4) - Personal Website UI
- [MVP-5 Epics](https://github.com/MEYWD-Labs/Resume-UI/issues?q=is%3Aissue+is%3Aopen+MVP-5) - AI Features UI
- [MVP-6 Epics](https://github.com/MEYWD-Labs/Resume-UI/issues?q=is%3Aissue+is%3Aopen+MVP-6) - Collaboration & Analytics UI

## Browser Support

- Chrome/Edge: Latest 2 versions
- Firefox: Latest 2 versions
- Safari: Latest 2 versions
- Mobile browsers: iOS Safari 14+, Chrome Android 90+

## Support

For issues or questions, create a GitHub issue in this repository.

## License

Proprietary - All rights reserved
