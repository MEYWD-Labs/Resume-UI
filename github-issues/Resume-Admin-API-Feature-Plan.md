# Resume-Admin-API Feature Plan

**Title:** Feature Plan: Resume-Admin-API - Admin Backend Service

## Overview
Backend service for administrative operations, analytics, user management, and system monitoring built on Cloudflare Workers.

## Tech Stack
- **Platform**: Cloudflare Workers
- **Runtime**: TypeScript with Hono
- **Database**: Cloudflare D1 (shared with Resume-API)
- **Analytics**: Cloudflare Analytics Engine
- **Storage**: Cloudflare R2
- **Cache**: Cloudflare KV
- **Email**: Cloudflare Email Workers

---

## Phase 1: MVP - Months 1-3

### Admin Authentication & Authorization
- [ ] **Admin Auth Middleware**
  - Validate admin JWT from Resume-SSO
  - Check admin role (Super Admin, Admin, Support)
  - Permission-based route access
  - Admin action logging

- [ ] **Admin Management**
  - `GET /admin/admins` - List all admins
  - `POST /admin/admins` - Create new admin
  - `PUT /admin/admins/:id` - Update admin role
  - `DELETE /admin/admins/:id` - Remove admin access

### Dashboard Analytics
- [ ] **Platform Metrics API**
  - `GET /admin/metrics/overview` - Key platform metrics
  - Total users
  - Active users (DAU/MAU)
  - Total resumes
  - Total exports
  - Conversion rate

- [ ] **Time-series Data**
  - `GET /admin/metrics/users` - User growth over time
  - `GET /admin/metrics/resumes` - Resume creation over time
  - `GET /admin/metrics/exports` - Exports over time
  - Granularity: hourly, daily, weekly, monthly

- [ ] **Aggregations**
  - Use Cloudflare Analytics Engine
  - Pre-compute common aggregations
  - Cache results in KV

### User Management APIs
- [ ] **User CRUD Operations**
  - `GET /admin/users` - List users (paginated)
  - `GET /admin/users/:id` - Get user details
  - `PUT /admin/users/:id` - Update user
  - `DELETE /admin/users/:id` - Delete user (soft delete)
  - `POST /admin/users/:id/suspend` - Suspend account
  - `POST /admin/users/:id/unsuspend` - Unsuspend account

- [ ] **User Search & Filtering**
  - Search by email, name, ID
  - Filter by subscription tier
  - Filter by status
  - Filter by date range
  - Sort by various fields

- [ ] **User Details**
  - `GET /admin/users/:id/resumes` - User's resumes
  - `GET /admin/users/:id/activity` - Activity log
  - `GET /admin/users/:id/exports` - Export history

- [ ] **User Actions**
  - `POST /admin/users/:id/reset-password` - Send reset email
  - `POST /admin/users/:id/verify-email` - Manually verify
  - `POST /admin/users/:id/impersonate` - Generate impersonation token

### Template Management
- [ ] **Template CRUD**
  - `GET /admin/templates` - List all templates
  - `GET /admin/templates/:id` - Get template details
  - `POST /admin/templates` - Create new template
  - `PUT /admin/templates/:id` - Update template
  - `DELETE /admin/templates/:id` - Delete template
  - `POST /admin/templates/:id/feature` - Mark as featured

- [ ] **Template Storage**
  - Store HTML templates in D1
  - Store preview images in R2
  - Version control for templates

- [ ] **Template Analytics**
  - `GET /admin/templates/:id/stats` - Usage statistics
  - Track template selections
  - Track resume completions per template

### Audit Logging
- [ ] **Activity Logging**
  - Log all admin actions to D1
  - Fields: admin_id, action, resource, timestamp, IP, changes
  - `GET /admin/audit-log` - Retrieve audit logs
  - Filter by admin, action, date range

### Database Schema Extensions
```sql
-- Admin users table
CREATE TABLE admin_users (
  id TEXT PRIMARY KEY,
  user_id TEXT UNIQUE NOT NULL,
  role TEXT NOT NULL, -- 'super_admin', 'admin', 'support'
  created_at INTEGER NOT NULL,
  created_by TEXT,
  FOREIGN KEY (user_id) REFERENCES users(id)
);

-- Audit log table
CREATE TABLE audit_log (
  id TEXT PRIMARY KEY,
  admin_id TEXT NOT NULL,
  action TEXT NOT NULL,
  resource_type TEXT NOT NULL,
  resource_id TEXT,
  changes TEXT, -- JSON
  ip_address TEXT,
  created_at INTEGER NOT NULL,
  FOREIGN KEY (admin_id) REFERENCES admin_users(id)
);

-- Feature flags table
CREATE TABLE feature_flags (
  id TEXT PRIMARY KEY,
  name TEXT UNIQUE NOT NULL,
  enabled BOOLEAN DEFAULT 0,
  rollout_percentage INTEGER DEFAULT 0,
  targeting_rules TEXT, -- JSON
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL
);
```

---

## Phase 2: Payment & Advanced Features - Months 4-6

### Financial Operations
- [ ] **Subscription Management**
  - `GET /admin/subscriptions` - List all subscriptions
  - `GET /admin/subscriptions/:id` - Subscription details
  - `POST /admin/subscriptions/:id/cancel` - Cancel subscription
  - `POST /admin/subscriptions/:id/refund` - Process refund

- [ ] **Revenue Analytics**
  - `GET /admin/metrics/revenue` - Revenue metrics
  - MRR (Monthly Recurring Revenue)
  - ARR (Annual Recurring Revenue)
  - Churn rate
  - ARPU (Average Revenue Per User)
  - LTV (Lifetime Value)

- [ ] **Transaction Management**
  - `GET /admin/transactions` - List transactions
  - `GET /admin/transactions/:id` - Transaction details
  - `POST /admin/transactions/:id/refund` - Refund transaction
  - Failed payment handling

- [ ] **Stripe Webhook Handler**
  - Handle subscription events
  - Update D1 with payment status
  - Send notifications on events

### Coupon Management
- [ ] **Coupon CRUD**
  - `POST /admin/coupons` - Create coupon
  - `GET /admin/coupons` - List coupons
  - `GET /admin/coupons/:id` - Coupon details
  - `PUT /admin/coupons/:id` - Update coupon
  - `DELETE /admin/coupons/:id` - Delete coupon

- [ ] **Coupon Analytics**
  - `GET /admin/coupons/:id/usage` - Usage statistics
  - Track redemptions
  - Calculate revenue impact

- [ ] **Coupon Database**
```sql
CREATE TABLE coupons (
  id TEXT PRIMARY KEY,
  code TEXT UNIQUE NOT NULL,
  type TEXT NOT NULL, -- 'percentage', 'fixed'
  value REAL NOT NULL,
  max_uses INTEGER,
  current_uses INTEGER DEFAULT 0,
  expires_at INTEGER,
  applicable_plans TEXT, -- JSON array
  created_at INTEGER NOT NULL,
  created_by TEXT,
  FOREIGN KEY (created_by) REFERENCES admin_users(id)
);

CREATE TABLE coupon_redemptions (
  id TEXT PRIMARY KEY,
  coupon_id TEXT NOT NULL,
  user_id TEXT NOT NULL,
  redeemed_at INTEGER NOT NULL,
  FOREIGN KEY (coupon_id) REFERENCES coupons(id),
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

### Advanced User Analytics
- [ ] **User Behavior Analytics**
  - `GET /admin/analytics/user-behavior` - Behavior metrics
  - Feature usage statistics
  - Session duration
  - User journey tracking

- [ ] **Cohort Analysis**
  - `GET /admin/analytics/cohorts` - Cohort data
  - Group users by signup month
  - Track retention rates
  - Engagement metrics per cohort

- [ ] **Funnel Analysis**
  - `GET /admin/analytics/funnels/:id` - Funnel metrics
  - Conversion rates
  - Drop-off points
  - Time between steps

### Support System
- [ ] **Ticket Management**
  - `GET /admin/tickets` - List support tickets
  - `GET /admin/tickets/:id` - Ticket details
  - `POST /admin/tickets` - Create ticket (manual)
  - `PUT /admin/tickets/:id` - Update ticket
  - `POST /admin/tickets/:id/reply` - Reply to ticket
  - `POST /admin/tickets/:id/close` - Close ticket

- [ ] **Ticket Database**
```sql
CREATE TABLE support_tickets (
  id TEXT PRIMARY KEY,
  user_id TEXT NOT NULL,
  subject TEXT NOT NULL,
  status TEXT NOT NULL, -- 'open', 'in_progress', 'resolved', 'closed'
  priority TEXT NOT NULL, -- 'low', 'medium', 'high', 'urgent'
  assigned_to TEXT,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL,
  FOREIGN KEY (user_id) REFERENCES users(id),
  FOREIGN KEY (assigned_to) REFERENCES admin_users(id)
);

CREATE TABLE ticket_messages (
  id TEXT PRIMARY KEY,
  ticket_id TEXT NOT NULL,
  sender_type TEXT NOT NULL, -- 'user', 'admin'
  sender_id TEXT NOT NULL,
  message TEXT NOT NULL,
  created_at INTEGER NOT NULL,
  FOREIGN KEY (ticket_id) REFERENCES support_tickets(id)
);
```

---

## Phase 3: Advanced Administration - Months 7-9

### Content Management
- [ ] **Announcement System**
  - `POST /admin/announcements` - Create announcement
  - `GET /admin/announcements` - List announcements
  - `PUT /admin/announcements/:id` - Update announcement
  - `DELETE /admin/announcements/:id` - Delete announcement
  - `POST /admin/announcements/:id/publish` - Publish announcement

- [ ] **Email Campaigns**
  - `POST /admin/campaigns` - Create campaign
  - `GET /admin/campaigns` - List campaigns
  - `POST /admin/campaigns/:id/send` - Send campaign
  - User segmentation for targeting
  - Schedule campaign sending
  - Track open rates and clicks

- [ ] **Content Library Management**
  - `GET /admin/content/examples` - List example content
  - `POST /admin/content/examples` - Add example
  - `PUT /admin/content/examples/:id` - Update example
  - Manage skill taxonomies
  - Job title database

### Website Management
- [ ] **Personal Website APIs**
  - `GET /admin/websites` - List all user websites
  - `GET /admin/websites/:id` - Website details
  - `POST /admin/websites/:id/suspend` - Suspend website (abuse)
  - `GET /admin/websites/stats` - Aggregate statistics

- [ ] **Custom Domain Management**
  - `GET /admin/domains` - List custom domains
  - `POST /admin/domains/:id/verify` - Verify domain
  - `POST /admin/domains/:id/approve` - Approve domain
  - DNS management via Cloudflare API

### Feature Flag Management
- [ ] **Feature Flag APIs**
  - `GET /admin/flags` - List all flags
  - `POST /admin/flags` - Create flag
  - `PUT /admin/flags/:id` - Update flag
  - `DELETE /admin/flags/:id` - Delete flag
  - `POST /admin/flags/:id/toggle` - Enable/disable flag

- [ ] **Gradual Rollout**
  - Percentage-based rollout
  - User segment targeting
  - A/B test configuration
  - Flag evaluation API

- [ ] **Flag Analytics**
  - `GET /admin/flags/:id/usage` - Usage statistics
  - Feature adoption metrics
  - A/B test results

### System Health Monitoring
- [ ] **Health Check APIs**
  - `GET /admin/health` - Overall system health
  - `GET /admin/health/workers` - Worker status
  - `GET /admin/health/database` - D1 status
  - `GET /admin/health/storage` - R2 usage
  - `GET /admin/health/queues` - Queue backlog

- [ ] **Performance Metrics**
  - `GET /admin/metrics/performance` - API performance
  - Response time percentiles (p50, p95, p99)
  - Error rates
  - Request volume

- [ ] **Error Tracking**
  - `GET /admin/errors` - Recent errors
  - `GET /admin/errors/:id` - Error details
  - Error frequency and patterns
  - Integration with Sentry

### Content Moderation
- [ ] **Flagged Content APIs**
  - `GET /admin/moderation/queue` - Moderation queue
  - `GET /admin/moderation/:id` - Content details
  - `POST /admin/moderation/:id/approve` - Approve content
  - `POST /admin/moderation/:id/reject` - Reject content

- [ ] **Abuse Detection**
  - Auto-flag spam content
  - Pattern detection
  - Account suspension workflow
  - `GET /admin/moderation/reports` - User reports

---

## Phase 4: Advanced Analytics & Automation - Months 10-12

### Advanced Analytics
- [ ] **Custom Report Builder**
  - `POST /admin/reports/custom` - Generate custom report
  - Flexible metric selection
  - Custom date ranges and filters
  - Export to CSV/PDF

- [ ] **Scheduled Reports**
  - `POST /admin/reports/schedule` - Schedule report
  - Daily/weekly/monthly reports
  - Email delivery
  - Store in R2

- [ ] **A/B Testing Analytics**
  - `GET /admin/experiments` - List experiments
  - `GET /admin/experiments/:id/results` - Test results
  - Statistical significance calculation
  - Variant performance comparison

### AI & ML Insights
- [ ] **AI Usage Analytics**
  - `GET /admin/ai/usage` - AI feature usage
  - Credits consumed per user
  - Most popular AI features
  - Cost analysis

- [ ] **Predictive Analytics**
  - Churn prediction
  - Upsell opportunities
  - Template recommendations
  - User lifetime value prediction

### Automation & Workflows
- [ ] **Automated Actions**
  - Auto-suspend abusive accounts
  - Auto-send payment reminders
  - Auto-expire coupons
  - Auto-archive tickets

- [ ] **Workflow Engine**
  - Define trigger conditions
  - Action sequences
  - Conditional logic
  - Schedule actions

### Data Export & Compliance
- [ ] **GDPR Compliance**
  - `POST /admin/gdpr/export/:userId` - Export user data
  - `POST /admin/gdpr/delete/:userId` - Delete user data
  - Data anonymization
  - Compliance audit trail

- [ ] **Bulk Operations**
  - `POST /admin/bulk/users` - Bulk user operations
  - `POST /admin/bulk/emails` - Bulk email sending
  - `POST /admin/bulk/refunds` - Bulk refunds
  - Background job processing with Cloudflare Queues

### Database Management
- [ ] **Database Operations**
  - `POST /admin/db/backup` - Trigger backup
  - `GET /admin/db/backups` - List backups
  - `POST /admin/db/restore` - Restore from backup
  - `GET /admin/db/stats` - Database statistics

- [ ] **Data Cleanup**
  - Soft delete cleanup
  - Old session cleanup
  - Expired token cleanup
  - Orphaned data cleanup

---

## Infrastructure & Operations

### Cloudflare Workers Configuration
- [ ] **Worker Bindings**
  - D1 database binding
  - R2 bucket binding
  - KV namespace binding
  - Analytics Engine binding
  - Queue binding

- [ ] **Environment Variables**
  - Admin JWT secret
  - Stripe API keys
  - Email service credentials
  - Feature flag overrides

### Performance & Scalability
- [ ] **Caching Strategy**
  - Cache frequently accessed data in KV
  - Analytics aggregations cache
  - Template metadata cache
  - TTL configuration

- [ ] **Rate Limiting**
  - Admin-specific rate limits
  - Higher limits for admin users
  - Protect sensitive operations

- [ ] **Background Jobs**
  - Use Cloudflare Queues
  - Bulk operation processing
  - Report generation
  - Data export jobs
  - Email sending

### Security
- [ ] **Access Control**
  - Role-based permissions
  - Resource-level authorization
  - Action-based permissions
  - Admin session timeout (30 min)

- [ ] **Audit & Compliance**
  - Log all admin operations
  - Tamper-proof audit log
  - Retention policies
  - Compliance reporting

- [ ] **Data Security**
  - Encrypt sensitive data
  - PII masking in logs
  - Secure file handling
  - Input validation and sanitization

### Monitoring & Logging
- [ ] **Logging**
  - Structured logging
  - Request/response logging
  - Error logging
  - Performance logging

- [ ] **Monitoring**
  - Cloudflare Workers Analytics
  - Custom metrics to Analytics Engine
  - Error tracking (Sentry integration)
  - Uptime monitoring

- [ ] **Alerts**
  - High error rate alerts
  - Performance degradation
  - Database issues
  - Queue backlog alerts

---

## API Documentation
- [ ] OpenAPI/Swagger specification
- [ ] Interactive API docs (Scalar or Swagger UI)
- [ ] Authentication guide
- [ ] Rate limits documentation
- [ ] Example requests/responses
- [ ] Error code reference

---

## Admin Permission Matrix

### Super Admin
- All user operations
- All financial operations
- System configuration
- Admin user management
- Feature flag management
- Database operations

### Admin
- User management (view, edit, suspend)
- Content management
- Template management
- Support tickets
- Analytics (read-only)
- Campaigns (create, send)

### Support
- User lookup (read-only)
- Support tickets (full access)
- Basic user actions (password reset, email verify)
- No financial operations
- Limited analytics
