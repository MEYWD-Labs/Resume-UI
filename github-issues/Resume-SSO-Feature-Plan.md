# Resume-SSO Feature Plan

**Title:** Feature Plan: Resume-SSO - Authentication & Identity Service

## Overview
Centralized authentication and identity management service for the Resume Builder platform, built on Cloudflare Workers.

## Tech Stack
- **Platform**: Cloudflare Workers
- **Runtime**: TypeScript with Hono
- **Database**: Cloudflare D1
- **Sessions**: Cloudflare KV (for JWT blacklist)
- **Email**: Cloudflare Email Workers or Resend
- **Password Hashing**: bcrypt or @noble/hashes
- **JWT**: jose library

---

## Phase 1: MVP (FREE TIER) - Months 1-3

### Email/Password Authentication
- [ ] **Registration**
  - `POST /auth/register` - Create new account
  - Email validation
  - Password strength requirements (min 8 chars, uppercase, number, special)
  - Email verification flow
  - Verification token storage in D1
  - Send verification email via Cloudflare Email Workers

- [ ] **Email Verification**
  - `GET /auth/verify/:token` - Verify email
  - Token expiration (24 hours)
  - Account activation

- [ ] **Login**
  - `POST /auth/login` - Authenticate user
  - Email + password validation
  - Password hashing with bcrypt
  - JWT token generation (access + refresh)
  - Return user profile

- [ ] **Logout**
  - `POST /auth/logout` - Invalidate session
  - Blacklist JWT in Cloudflare KV
  - Clear client-side tokens

### Password Management
- [ ] **Forgot Password**
  - `POST /auth/forgot-password` - Request reset
  - Generate reset token
  - Send reset email
  - Token expiration (1 hour)

- [ ] **Reset Password**
  - `POST /auth/reset-password` - Set new password
  - Validate reset token
  - Update password hash
  - Invalidate all existing sessions

- [ ] **Change Password**
  - `POST /auth/change-password` - Update password (authenticated)
  - Verify current password
  - Update to new password
  - Maintain current session

### Social Authentication (OAuth)
- [ ] **Google OAuth**
  - `GET /auth/google` - Initiate OAuth flow
  - `GET /auth/google/callback` - Handle callback
  - User profile creation/linking
  - JWT generation

- [ ] **LinkedIn OAuth**
  - `GET /auth/linkedin` - Initiate OAuth flow
  - `GET /auth/linkedin/callback` - Handle callback
  - Extract profile data for resume
  - Auto-populate user info

### Session Management
- [ ] **JWT Implementation**
  - Access token (15 minutes expiry)
  - Refresh token (7 days expiry)
  - Token payload: user_id, email, subscription_tier, roles
  - RS256 signing algorithm

- [ ] **Token Refresh**
  - `POST /auth/refresh` - Refresh access token
  - Validate refresh token
  - Issue new access token
  - Rotate refresh token

- [ ] **Session Validation**
  - `GET /auth/me` - Get current user
  - Middleware for token validation
  - User context injection

### User Profile
- [ ] **Profile Management**
  - `GET /users/profile` - Get user profile
  - `PUT /users/profile` - Update profile
  - Fields: name, email, avatar, bio
  - `POST /users/avatar` - Upload avatar to R2

- [ ] **Account Settings**
  - Email preferences
  - Notification settings
  - Privacy settings

### Database Schema (D1)
```sql
-- Users table
CREATE TABLE users (
  id TEXT PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  password_hash TEXT, -- NULL for OAuth users
  name TEXT,
  avatar_url TEXT,
  email_verified BOOLEAN DEFAULT 0,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL,
  last_login INTEGER
);

-- OAuth accounts table
CREATE TABLE oauth_accounts (
  id TEXT PRIMARY KEY,
  user_id TEXT NOT NULL,
  provider TEXT NOT NULL, -- 'google', 'linkedin', etc.
  provider_user_id TEXT NOT NULL,
  access_token TEXT,
  refresh_token TEXT,
  created_at INTEGER NOT NULL,
  FOREIGN KEY (user_id) REFERENCES users(id),
  UNIQUE(provider, provider_user_id)
);

-- Verification tokens table
CREATE TABLE verification_tokens (
  token TEXT PRIMARY KEY,
  user_id TEXT NOT NULL,
  type TEXT NOT NULL, -- 'email', 'password_reset'
  expires_at INTEGER NOT NULL,
  created_at INTEGER NOT NULL,
  FOREIGN KEY (user_id) REFERENCES users(id)
);

-- Sessions table (for audit/security)
CREATE TABLE sessions (
  id TEXT PRIMARY KEY,
  user_id TEXT NOT NULL,
  ip_address TEXT,
  user_agent TEXT,
  created_at INTEGER NOT NULL,
  expires_at INTEGER NOT NULL,
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

---

## Phase 2: Security & Mobile - Months 4-6

### Enhanced Security
- [ ] **Rate Limiting**
  - Login attempts: 5 per 15 minutes
  - Registration: 3 per hour per IP
  - Password reset: 3 per hour
  - Use Cloudflare Rate Limiting

- [ ] **Account Lockout**
  - Lock after 5 failed login attempts
  - Unlock after 30 minutes or email verification
  - Email notification on lockout

- [ ] **IP-based Protection**
  - Track login IPs in D1
  - Suspicious activity detection
  - New device notifications
  - Geographic anomaly detection

### Multi-Factor Authentication (MFA)
- [ ] **TOTP (Time-based OTP)**
  - `POST /auth/mfa/enable` - Enable MFA
  - `POST /auth/mfa/verify` - Verify TOTP code
  - `POST /auth/mfa/disable` - Disable MFA
  - Generate QR code for authenticator apps
  - Backup codes (10 codes)

- [ ] **Email OTP**
  - Send 6-digit code via email
  - 10-minute expiration
  - Fallback when TOTP unavailable

- [ ] **MFA Login Flow**
  - Two-step verification
  - Remember device option (30 days)
  - Recovery codes

### Mobile App Support
- [ ] **Mobile OAuth Flow**
  - Deep linking support
  - Native app callbacks
  - Secure token storage

- [ ] **Biometric Authentication**
  - Support for Face ID / Touch ID
  - Device-specific refresh tokens
  - Quick re-authentication

- [ ] **Push Notifications**
  - Login notifications
  - Security alerts
  - Integration with mobile app

### Additional OAuth Providers
- [ ] **Microsoft OAuth**
  - Azure AD integration
  - Office 365 accounts

- [ ] **GitHub OAuth**
  - Developer audience
  - Profile import

---

## Phase 3: Enterprise & Advanced Features - Months 7-9

### Enterprise SSO
- [ ] **SAML 2.0**
  - Identity Provider integration
  - Service Provider configuration
  - Attribute mapping
  - Just-in-Time provisioning

- [ ] **OAuth 2.0 / OpenID Connect**
  - Custom IdP support
  - Authorization code flow
  - Client credentials flow

### Organization Management
- [ ] **Team Accounts**
  - Create organization
  - Invite team members
  - Role-based access control
  - Organization billing

- [ ] **User Roles**
  - Owner
  - Admin
  - Member
  - Guest (view-only)

### Advanced Security
- [ ] **Security Audit Log**
  - Track all authentication events
  - Login history
  - Permission changes
  - Export audit logs

- [ ] **Device Management**
  - List active sessions/devices
  - Remote session termination
  - Trusted device management

- [ ] **Account Security Dashboard**
  - Security score
  - Active sessions
  - Recent activities
  - Security recommendations

### Compliance Features
- [ ] **GDPR Compliance**
  - Data export request
  - Right to deletion
  - Consent management
  - Privacy policy acceptance tracking

- [ ] **Account Deletion**
  - `DELETE /users/account` - Soft delete
  - Data retention period (30 days)
  - Permanent deletion
  - Export data before deletion

---

## Phase 4: Advanced Features - Months 10-12

### API & Developer Features
- [ ] **API Keys**
  - Generate API keys for integrations
  - Scoped permissions
  - Key rotation
  - Usage tracking

- [ ] **OAuth Provider**
  - Allow third-party apps to authenticate via Resume Builder
  - OAuth 2.0 authorization server
  - Consent screen
  - Token management

### Advanced Analytics
- [ ] **Authentication Analytics**
  - Login patterns
  - Popular auth methods
  - Geographic distribution
  - Device breakdown

- [ ] **Security Metrics**
  - Failed login attempts
  - MFA adoption rate
  - Account lockouts
  - Suspicious activity reports

### Email Management
- [ ] **Email Verification Status**
  - Resend verification email
  - Change email (re-verification required)

- [ ] **Email Templates**
  - Welcome email
  - Password reset
  - Security alerts
  - MFA setup
  - Branded templates

### Account Recovery
- [ ] **Account Recovery Flow**
  - Multiple verification methods
  - Security questions (optional)
  - Recovery email
  - Support ticket integration

---

## Security Best Practices

### Password Security
- [ ] Bcrypt with cost factor 12
- [ ] Password breach detection (HaveIBeenPwned API)
- [ ] Password history (prevent reuse of last 5)
- [ ] Force password reset after X days (optional)

### JWT Security
- [ ] Short-lived access tokens (15 min)
- [ ] Refresh token rotation
- [ ] Token blacklisting in Cloudflare KV
- [ ] Signature verification
- [ ] Token expiration validation

### Data Protection
- [ ] Encrypt sensitive data at rest
- [ ] Secure password reset tokens
- [ ] HTTPS only
- [ ] Secure cookie flags (HttpOnly, Secure, SameSite)

---

## API Endpoints Summary

### Public Endpoints
- `POST /auth/register`
- `POST /auth/login`
- `POST /auth/forgot-password`
- `POST /auth/reset-password`
- `GET /auth/verify/:token`
- `GET /auth/:provider` (OAuth)
- `GET /auth/:provider/callback`

### Authenticated Endpoints
- `POST /auth/logout`
- `POST /auth/refresh`
- `GET /auth/me`
- `POST /auth/change-password`
- `GET /users/profile`
- `PUT /users/profile`
- `POST /users/avatar`
- `POST /auth/mfa/enable`
- `POST /auth/mfa/verify`
- `DELETE /users/account`

---

## Monitoring & Operations

### Logging
- [ ] Authentication events
- [ ] Failed login attempts
- [ ] Password changes
- [ ] MFA events
- [ ] Account deletions

### Monitoring
- [ ] Workers Analytics
- [ ] Error tracking
- [ ] Performance metrics
- [ ] Security alerts

### Alerts
- [ ] Unusual login patterns
- [ ] Mass failed logins
- [ ] Account lockouts spike
- [ ] MFA bypass attempts
