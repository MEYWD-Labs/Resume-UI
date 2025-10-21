# Resume-Admin Feature Plan

**Title:** Feature Plan: Resume-Admin - Admin Dashboard Frontend

## Overview
Administrative dashboard for platform management, user support, analytics, and content moderation built with modern web technologies.

## Tech Stack
- **Framework**: Next.js 14+ (App Router) with TypeScript
- **Styling**: Tailwind CSS + shadcn/ui
- **Charts**: Recharts or Chart.js
- **Tables**: TanStack Table (React Table v8)
- **State Management**: Zustand
- **Deployment**: Cloudflare Pages
- **API**: Resume-Admin-API (Cloudflare Workers)

---

## Phase 1: MVP - Months 1-3

### Admin Authentication
- [ ] **Login System**
  - Admin login page
  - Integration with Resume-SSO
  - Role-based access (Super Admin, Admin, Support)
  - Session management
  - MFA required for admins

- [ ] **Admin User Management**
  - List admin users
  - Create new admin
  - Role assignment
  - Activity logging

### Dashboard Overview
- [ ] **Key Metrics Cards**
  - Total users (all time)
  - Active users (last 30 days)
  - Total resumes created
  - PDF exports (last 30 days)
  - Conversion rate (free to paid)

- [ ] **Quick Stats**
  - New signups today/week/month
  - Revenue today/week/month
  - Active subscriptions
  - Support tickets open

- [ ] **Activity Timeline**
  - Recent user registrations
  - New subscriptions
  - Cancellations
  - Support tickets

### User Management
- [ ] **User List**
  - Paginated table (50 per page)
  - Search by email, name, ID
  - Filter by:
    - Subscription tier (Free, Pro, Premium)
    - Status (Active, Suspended, Deleted)
    - Signup date range
    - Last active date
  - Sort by various columns

- [ ] **User Details Page**
  - User profile information
  - Account created date
  - Last login
  - Subscription status
  - Resume count
  - Export history
  - Activity log
  - Email verification status

- [ ] **User Actions**
  - View user resumes (read-only)
  - Suspend account
  - Unsuspend account
  - Reset password (send email)
  - Verify email manually
  - Delete account
  - Impersonate user (for support)

### Template Management
- [ ] **Template Library**
  - Grid view of all templates
  - Template preview
  - Template metadata (name, category, premium status)
  - Usage statistics per template

- [ ] **Template Editor**
  - Upload new template
  - Edit template HTML/CSS
  - Set template as premium
  - Feature/unfeature template
  - Delete template
  - Template versioning

### Basic Analytics
- [ ] **User Growth Chart**
  - Line chart showing user signups over time
  - Daily/Weekly/Monthly views
  - Date range selector

- [ ] **Subscription Distribution**
  - Pie chart: Free vs Pro vs Premium
  - Number and percentage breakdown

- [ ] **Popular Templates**
  - Bar chart of template usage
  - Top 10 templates

---

## Phase 2: Payment & Advanced Features - Months 4-6

### Financial Dashboard
- [ ] **Revenue Metrics**
  - Monthly Recurring Revenue (MRR)
  - Annual Recurring Revenue (ARR)
  - Revenue growth rate
  - Average Revenue Per User (ARPU)
  - Customer Lifetime Value (LTV)

- [ ] **Revenue Charts**
  - Revenue over time (line chart)
  - Revenue by plan (stacked bar chart)
  - Churn rate visualization
  - New vs lost revenue

- [ ] **Subscription Management**
  - All active subscriptions table
  - Filter by plan, status, date
  - Upcoming renewals
  - Failed payments
  - Cancellation requests

### Payment Management
- [ ] **Transaction History**
  - All transactions table
  - Search by user, amount, date
  - Transaction status
  - Payment method
  - Refund status

- [ ] **Refund Management**
  - Process refunds
  - Partial refunds
  - Refund history
  - Refund analytics

- [ ] **Failed Payments**
  - List of failed payments
  - Retry payment
  - Contact user
  - Dunning management

### Coupon & Discount Management
- [ ] **Coupon Creation**
  - Create discount codes
  - Percentage or fixed amount
  - Expiration date
  - Usage limits
  - Applicable plans

- [ ] **Coupon Analytics**
  - Coupon usage statistics
  - Revenue impact
  - Most effective coupons
  - Active vs expired coupons

### Advanced User Analytics
- [ ] **User Behavior**
  - Feature usage heatmap
  - Most used features
  - Average session duration
  - User journey flow
  - Drop-off analysis

- [ ] **Cohort Analysis**
  - User cohorts by signup month
  - Retention curves
  - Engagement metrics
  - Conversion rates by cohort

### Support System
- [ ] **Ticket Management**
  - Support ticket list
  - Ticket status (Open, In Progress, Resolved, Closed)
  - Priority levels
  - Assign to admin
  - Internal notes

- [ ] **Ticket Details**
  - Conversation history
  - User information sidebar
  - Quick actions (view user, suspend, refund)
  - Canned responses
  - File attachments

---

## Phase 3: Advanced Administration - Months 7-9

### Content Management
- [ ] **Announcement System**
  - Create platform announcements
  - Schedule announcements
  - Target specific user segments
  - Preview before publish
  - Track announcement views

- [ ] **Email Campaigns**
  - Compose email campaigns
  - Template selection
  - User segmentation
  - A/B testing
  - Schedule send
  - Campaign analytics

- [ ] **Content Library**
  - Manage example bullet points
  - Industry-specific content
  - Skill taxonomies
  - Job titles database
  - Bulk import/export

### Website Analytics
- [ ] **Personal Website Stats**
  - Total websites created
  - Active websites
  - Custom domain usage
  - Website traffic (aggregated)
  - Most popular website templates

- [ ] **Website Management**
  - List all user websites
  - View website details
  - Suspend website (abuse)
  - Custom domain approval

### Feature Flag Management
- [ ] **Feature Flags Dashboard**
  - List all feature flags
  - Enable/disable features
  - Gradual rollout (percentage)
  - User segment targeting
  - A/B test configuration

- [ ] **Feature Usage**
  - Track feature adoption
  - Feature usage analytics
  - Flag history and changes

### System Health Monitoring
- [ ] **System Status Dashboard**
  - API health status
  - Database status
  - Queue status
  - R2 storage usage
  - Error rates

- [ ] **Performance Metrics**
  - API response times
  - Cloudflare Workers CPU usage
  - Database query performance
  - Slow query log

- [ ] **Error Tracking**
  - Recent errors table
  - Error frequency
  - Error details and stack traces
  - Error resolution status

### Content Moderation
- [ ] **Flagged Content**
  - User-reported content
  - Auto-flagged content (AI)
  - Review queue
  - Approve/reject actions

- [ ] **Abuse Detection**
  - Spam resume detection
  - Inappropriate content
  - Terms of Service violations
  - Account suspension workflow

---

## Phase 4: Advanced Analytics & Automation - Months 10-12

### Advanced Analytics
- [ ] **Custom Reports**
  - Report builder interface
  - Custom metrics selection
  - Date range and filters
  - Export to CSV/PDF
  - Scheduled reports

- [ ] **A/B Testing Dashboard**
  - Active experiments
  - Test results visualization
  - Statistical significance
  - Winner selection

- [ ] **Funnel Analysis**
  - Conversion funnels
  - Drop-off points
  - Funnel optimization suggestions
  - Compare funnels over time

### AI & ML Insights
- [ ] **AI Usage Analytics**
  - AI feature usage
  - Credits consumed
  - Most used AI features
  - AI performance metrics

- [ ] **Template Recommendations**
  - AI-suggested template improvements
  - Template performance predictions
  - User preference analysis

### Automation
- [ ] **Automated Actions**
  - Auto-suspend abusive accounts
  - Auto-send payment reminders
  - Auto-expire old coupons
  - Auto-archive old tickets

- [ ] **Workflow Automation**
  - Trigger-based actions
  - Email automation rules
  - User lifecycle automations

### Data Export & Reporting
- [ ] **GDPR Data Requests**
  - User data export requests
  - Queue management
  - Generate ZIP file
  - Audit trail

- [ ] **Bulk Operations**
  - Bulk user actions
  - Bulk email sending
  - Bulk refunds
  - CSV import/export

### Admin Analytics
- [ ] **Admin Activity Log**
  - All admin actions logged
  - Filter by admin, action type, date
  - Audit trail for compliance
  - Export logs

- [ ] **Role & Permission Management**
  - Define custom roles
  - Granular permissions
  - Role assignment
  - Permission matrix view

---

## UI/UX Features (All Phases)

### Design System
- [ ] **Component Library**
  - shadcn/ui components
  - Custom admin components
  - Consistent styling
  - Dark mode support

- [ ] **Data Visualization**
  - Interactive charts
  - Real-time updates
  - Export chart data
  - Responsive charts

### User Experience
- [ ] **Search & Filters**
  - Global search
  - Advanced filtering
  - Saved filters
  - Quick filters

- [ ] **Bulk Actions**
  - Select multiple items
  - Bulk edit
  - Bulk delete
  - Bulk export

- [ ] **Notifications**
  - In-app notifications
  - Badge counters
  - Real-time alerts
  - Notification center

### Performance
- [ ] **Optimistic Updates**
- [ ] **Lazy Loading**
- [ ] **Pagination**
- [ ] **Infinite Scroll (where appropriate)**
- [ ] **Caching Strategy**

---

## Security & Access Control

### Authentication & Authorization
- [ ] MFA enforced for all admins
- [ ] Role-based access control (RBAC)
- [ ] IP whitelisting (optional)
- [ ] Session timeout (30 minutes)
- [ ] Audit all admin actions

### Data Security
- [ ] Sensitive data masking (emails, payment info)
- [ ] Secure file uploads
- [ ] XSS protection
- [ ] CSRF protection

---

## Admin Roles & Permissions

### Super Admin
- Full access to all features
- User management
- Financial operations
- System configuration

### Admin
- User management (view, suspend)
- Content management
- Support tickets
- Analytics (read-only)

### Support
- View users
- Manage support tickets
- Basic user actions (reset password)
- No financial access

---

## Performance Targets
- Dashboard load time: < 2s
- Search results: < 500ms
- Chart rendering: < 1s
- Table pagination: < 300ms
