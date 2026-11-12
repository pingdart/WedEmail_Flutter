# PingDart Webmail – Multi-Tenant SaaS & White-Label Email Platform (UI/Frontend PRD)

## Product Overview

PingDart Webmail is a multi-tenant SaaS and white-label email platform built on top of the existing PingDart workspace and MailForge infrastructure. One central frontend architecture supports multiple reseller brands, business organizations, and individual mailboxes without maintaining separate frontend codebases for each customer.

> **Phase Scope:** This PRD covers the complete **UI/frontend scope** using mock data and typed service interfaces. Backend architecture, database schemas, SMTP/IMAP/POP3 integration, live authentication, and payment processing are intentionally excluded for the next integration phase. **Keep the existing MailForge (`Email/`) backend untouched during this frontend phase.**

---

## 1. Product Vision

Create a modern email platform comparable in usability to Gmail, Outlook, and Zoho Mail, paired with a multi-tenant SaaS and white-label reseller business model.

The platform supports:
- Individual email users
- Small and large organizations
- Companies managing multiple domains
- Direct SaaS customers
- White-label partners and resellers with custom branding
- Subscription-based email plans and storage add-ons

---

## 2. Frontend Applications (5 Experiences)

| # | Application | Workspace Location | Target Users | Primary Responsibility |
|---|---|---|---|---|
| 1 | **Webmail Application** | `App/` (Next.js) | End users | Inbox, compose, threads, search, templates, snoozed, contacts, calendar, settings |
| 2 | **Flutter Mobile Application** | `FlutterEmail/` (Flutter) | End users on Android & iOS | Mobile inbox, folder drawer, tablet split view, offline viewing, push UI, biometric lock |
| 3 | **SaaS Customer Portal** | `App/` (Next.js) | Business org admins | Organization dashboard, domains, DNS status, mailboxes, team roles, billing |
| 4 | **White-Label Reseller Portal** | `App/` (Next.js) | Reseller partners | Brand customization, custom domains, customer management, reseller pricing & plans |
| 5 | **PingDart Master Admin Portal** | `App/` (Next.js) | PingDart platform owner | Global dashboard, tenant/reseller oversight, master plans, feature flags, audit logs, system status |

---

## 3. Workspace Architecture

Use the existing PingDart workspace. Do **not** create a duplicate Next.js monorepo.

| Directory | Responsibility |
|---|---|
| `App/` | Next.js webmail, SaaS customer portal, white-label reseller portal, and master admin portal |
| `FlutterEmail/` | Flutter Android and iOS mobile application |
| `Apis/` | Existing backend APIs (future integration target) |
| `Email/` | MailForge email infrastructure (untouched during this frontend phase) |

### Recommended Internal Structure

```text
PingDart/
├── App/                              # Existing Next.js workspace (Webmail + Portals)
│   └── src/
│       ├── features/
│       │   ├── webmail/              # Inbox, threads, compose, search, templates, snoozed, contacts, calendar, settings
│       │   ├── auth/                 # Branded login, forgot password, OTP, 2FA
│       │   ├── saas-admin/           # Org dashboard, domains, mailboxes, billing
│       │   ├── reseller-portal/      # Reseller dashboard, branding, customers, pricing, domains
│       │   └── master-admin/         # Global dashboard, tenants, resellers, audit logs, system status
│       ├── components/ui/            # Shared React design system components
│       ├── theme/                    # BrandTheme engine & light/dark tokens
│       ├── services/                 # Typed API service interfaces + mock implementations
│       └── types/                    # Shared TypeScript contracts
│
├── FlutterEmail/                     # Flutter Android & iOS application
│   ├── doc/
│   │   └── prd.md
│   ├── lib/
│   │   ├── core/                     # Constants, utils, service interfaces, mock data
│   │   ├── theme/                    # Dynamic BrandTheme & light/dark ColorScheme
│   │   ├── navigation/               # App router, bottom nav, folder drawer, tablet rail
│   │   ├── features/
│   │   │   ├── auth/                 # Login, forgot password, OTP, 2FA, biometric lock
│   │   │   ├── mail/                 # Inbox, folders, thread view, compose, search, snoozed, templates
│   │   │   ├── contacts/             # Address book, groups, contact detail
│   │   │   ├── calendar/             # Agenda/day/week/month views, event editor
│   │   │   └── settings/             # Account switcher, preferences, theme, notifications
│   │   └── widgets/                  # Reusable Flutter UI components
│   └── test/
│
├── Apis/                             # Existing backend APIs (later integration)
└── Email/                            # MailForge infrastructure (untouched)
```

---

## 4. Technology Stack

| Component | Recommendation |
|---|---|
| Webmail & Portals | Next.js (in `App/`) |
| Mobile App | Flutter for Android & iOS (in `FlutterEmail/`) |
| Web Styling | Tailwind CSS |
| Web UI Components | Shared React design system |
| Web State Management | Zustand / React Query |
| Flutter State Management | Riverpod |
| Icons | Lucide (Web) / Material Symbols (Flutter) |
| Theme Engine | Dynamic `BrandTheme` with Light & Dark modes |
| Localization | English initially, structured for i18n expansion |

---

## 5. Authentication & Onboarding UI (Webmail & Mobile)

All authentication screens must render dynamically using the active tenant's `BrandTheme` (logo, primary color, company name, support links, and optional "Powered by PingDart" footer).

| Screen | Required UI Elements |
|---|---|
| **Login** | Branded header/logo, email input, password input with visibility toggle, "Remember me", "Forgot password?" link, SSO/OAuth placeholder buttons, inline validation & error states |
| **Forgot Password** | Email input, submit button, confirmation state with resend timer, back-to-login link |
| **OTP Verification** | 6-digit OTP input (auto-focus/paste support), countdown timer, resend OTP action, invalid/expired code error state |
| **2FA Setup & Challenge** | Authenticator QR code display, backup recovery codes with copy/download actions, 6-digit TOTP verification input, trust-device toggle |
| **Mobile Splash & Onboarding** | Branded splash screen, 3-step onboarding carousel (mobile only), skip/next/get-started actions |
| **Mobile Biometric Lock** | Face ID / Touch ID / Fingerprint prompt screen with fallback to PIN/password |

---

## 6. Webmail UI Modules (`App/`)

### 6.1 Main Navigation (Left Sidebar)
- **Primary CTA**: `Compose` button
- **Mail Folders**: `Inbox`, `Starred`, `Snoozed`, `Sent`, `Drafts`, `Archive`, `Spam`, `Trash`
- **Productivity**: `Templates`, `Contacts`, `Calendar`
- **System**: `Settings`, active account/storage quota indicator

### 6.2 Inbox & Folder List Screen
- Top search bar with advanced search trigger
- Quick filter chips: `All`, `Unread`, `Read`, `Has Attachments`, `Important`
- Bulk selection checkbox with contextual action bar (`Mark Read/Unread`, `Archive`, `Snooze`, `Move to`, `Delete`, `Report Spam`)
- Email row items: sender avatar, sender name, subject, snippet preview, received timestamp, unread indicator, star toggle, snooze badge, attachment badge, thread message count badge
- Pagination or infinite scrolling, plus empty/loading/error states

### 6.3 Advanced Email Search & Filters
- Instant search bar with recent search suggestions
- Expandable filter panel:
  - `From`, `To`, `Subject`, `Includes words`, `Excludes words`
  - `Folder / Scope` (`All Mail`, `Inbox`, `Sent`, `Drafts`, `Archive`, `Trash`)
  - `Date Range` (presets + custom start/end date)
  - `Has Attachment` toggle, `Unread Only` toggle, `Starred Only` toggle
- Clear filters action and result count summary

### 6.4 Thread / Conversation Reading Screen
- Thread header: subject, folder/label badges, thread actions (`Reply`, `Reply All`, `Forward`, `Snooze`, `Archive`, `Delete`, `Report Spam`, `Print`, `Expand/Collapse All`)
- Collapsible message stack:
  - Collapsed messages show sender avatar, name, snippet, and timestamp
  - Expanded messages show full sender, To/CC/BCC details (expandable), timestamp, sanitized HTML email body, inline images, and attachment cards with preview/download actions
- Inline quick-reply box at the bottom of the thread and per-message `Reply` / `Reply All` / `Forward` actions

### 6.5 Compose Email (Modal / Docked / Full-Screen)
- `From` account/alias selector
- `To`, `CC`, `BCC` token/chip inputs with contact autocomplete
- `Subject` input
- Rich-text editor: font family/size, bold, italic, underline, strikethrough, text color, bulleted/numbered lists, alignment, blockquote, hyperlinks, inline image insertion, clear formatting
- `Insert Template` picker
- `Signature` selector
- File attachment uploader with progress/remove states
- Actions: `Send`, `Schedule Send` (UI date/time picker), `Save Draft` (with auto-save indicator), `Discard Draft`

### 6.6 Snoozed Emails & Snooze Picker
- Snooze action available from inbox hover, bulk bar, and thread view
- Snooze popover presets: `Later today`, `Tomorrow`, `This weekend`, `Next week`, `Pick date & time`
- Dedicated `Snoozed` folder view showing scheduled return timestamp and `Unsnooze` action

### 6.7 Email Templates
- Template list view with search and category/folder filter
- Create/Edit template modal or view: template name, subject preset, rich-text body, placeholder variable chips (e.g., `{{first_name}}`, `{{company}}`)
- Duplicate, delete, and `Use in Compose` actions

### 6.8 Contacts (Address Book)
- Left sub-nav: `All Contacts`, `Frequently Contacted`, `Groups / Labels`, `Import / Export`
- Contact list with search, A–Z quick jump, bulk select, and avatar/name/email/phone/company columns
- Contact detail drawer/panel: full profile, email/phone/address/notes, recent email history preview, `Send Email` quick action
- Create/Edit contact modal and CSV/vCard import/export UI flows

### 6.9 Calendar
- Views: `Month`, `Week`, `Day`, `Agenda`
- Left mini-calendar + "My Calendars" / "Shared Calendars" visibility toggles
- Create/Edit Event modal: title, date/time, all-day toggle, timezone, recurrence presets, location/meeting link, attendee email chips with RSVP status (`Accepted`, `Declined`, `Tentative`), reminder intervals, description
- Event detail popover with `Edit`, `Delete`, and `RSVP` actions

### 6.10 Email Settings
- Profile and account information
- Multiple email accounts & aliases
- Email signatures (rich-text editor + default per account)
- Vacation / Out-of-Office auto-responder (date range, subject, message)
- Inbox preferences (density, reading pane position: off/right/bottom, conversation view toggle)
- Theme settings (Light / Dark / System)
- Notification settings (desktop, sound, per-folder)
- Language and Time zone
- Security settings (password change, 2FA management, active session list with revoke action)

---

## 7. Flutter Mobile Application (`FlutterEmail/`)

### 7.1 Mobile Navigation
- **Bottom Navigation Bar** (Phones): `1. Mail`, `2. Search`, `3. Calendar`, `4. Contacts`, `5. Settings`
- **Slide-Out Folder Drawer** (accessible from Mail app bar):
  - Active account header with multi-account switcher & storage indicator
  - Folders: `Inbox`, `Starred`, `Snoozed`, `Sent`, `Drafts`, `Archive`, `Spam`, `Trash`, `Templates`
  - Quick link to `Notifications` & `Settings`

### 7.2 Tablet & Foldable Master-Detail Layout
- Adaptive layout at tablet breakpoints (`>= 600dp` / `>= 840dp`):
  - **Navigation Rail** replacing bottom navigation
  - **Split-Pane Master-Detail View** for Mail (message list on left, thread reading pane on right), Contacts, and Settings
  - Resizable or proportional panes (`35% / 65%` default)

### 7.3 Mobile Screen Inventory

| Screen | Key Features |
|---|---|
| `Splash` | Branded loading screen using active `BrandTheme` |
| `Onboarding` | 3-step feature introduction for first launch |
| `Login / Forgot Password / OTP / 2FA` | Branded auth flow matching Section 5 |
| `Biometric Lock` | Face ID / Fingerprint gate with passcode fallback |
| `Account Setup` | Add/switch multiple email accounts |
| `Inbox & Folders` | Folder drawer, pull-to-refresh, swipe-to-archive/delete, unread/star/attachment badges, FAB compose |
| `Thread / Message` | Collapsible conversation stack, HTML rendering, inline reply/reply-all/forward, snooze, attachment viewer/download |
| `Compose` | From selector, To/CC/BCC chips, rich-text toolbar, template/signature picker, file attachments |
| `Search` | Recent searches + filter chips (sender, folder, date, attachments, unread) |
| `Attachments` | Dedicated attachment preview/download manager |
| `Contacts` | Searchable list, groups, contact detail, create/edit |
| `Calendar` | Agenda, day, and month views, event detail, create/edit event, RSVP |
| `Notifications` | Notification history & deep-link routing |
| `Settings & Profile` | Theme (Light/Dark/System), biometric toggle, signatures, notifications, account info |

### 7.4 Flutter-Specific Requirements
- Android and iOS support
- Offline email viewing UI (cached inbox/threads indicator + offline banner)
- Push notification UI & deep-link handling
- Touch-friendly controls, swipe gestures, and pull-to-refresh

---

## 8. SaaS Customer Portal (`App/`)

For organizations purchasing and managing PingDart or reseller-branded email services.

- **Main Dashboard**: Total domains, total mailboxes, active mailboxes, storage usage gauge, current plan, subscription renewal/expiry, recent activity, account notifications
- **Organization Management**: Company profile, organization settings, team members list, user invitations, roles & permissions assignment, organization preferences
- **Domain Management**: Add domain wizard, domain list, domain details & verification status, DNS records display (`MX`, `SPF`, `DKIM`, `DMARC` copyable values), mailbox-to-domain association
- **Mailbox Management**: Create/edit mailbox, mailbox list, storage quota allocation, status (`Active`/`Suspended`), password reset, suspend/delete mailbox, alias management, forwarding rules
- **Subscription & Billing UI**: Available plans comparison, current subscription card, upgrade/downgrade flow, billing history, downloadable invoice previews, payment method UI, storage & seat add-ons

---

## 9. White-Label Reseller Portal (`App/`)

For partners selling email services under their own brand.

### 9.1 White-Label Hierarchy
1. **PingDart Platform Owner** — Master administration
2. **White-Label Reseller** — Own brand, custom domain, pricing, and customers
3. **Business Customers** — Organizations, domains, and mailboxes
4. **Email Users** — Individual mailboxes

### 9.2 Reseller Dashboard
- Total customers, total domains, total mailboxes, active subscriptions, aggregate storage consumption, revenue overview, upcoming renewals, recent customer activity

### 9.3 Brand Customization
Resellers configure:
- Company name, Logo (light & dark variants), Favicon
- Primary color, Secondary color, Background/Surface theme presets
- Branded Login page preview, Branded Webmail preview, Mobile app branding config
- Support email, Support URL, Website URL, Terms of Service & Privacy Policy URLs/content
- Toggle for `showPoweredBy` ("Powered by PingDart")

### 9.4 Custom Domain UI
- Custom portal/webmail hostnames (e.g., `mail.company.com`, `webmail.partner.com`)
- CNAME/SSL configuration instructions and verification status indicators

### 9.5 Reseller Customer & Pricing Management
- **Customer Management**: Add customer, customer list, profile, domains, mailboxes, subscriptions, usage, billing history, suspend/reactivate customer
- **Pricing Management**: Configure custom selling plans (e.g., Starter: 5 mailboxes / 10 GB, Business: 25 mailboxes / 50 GB, Professional: 100 mailboxes / 250 GB, Enterprise: Custom) — *example UI values only*

---

## 10. PingDart Master Administration Portal (`App/`)

Separate administration interface for the PingDart platform owner:
1. Global dashboard (platform-wide KPIs)
2. Tenant management
3. Reseller management (approve, suspend, inspect)
4. Customer management
5. Domain overview
6. Mailbox overview
7. Subscription plans & pricing management
8. Billing overview
9. White-label configuration & defaults
10. Platform announcements (banner/notification composer)
11. Support management
12. Audit-log viewer (filterable by actor, tenant, action, date)
13. Feature configuration / feature flags per plan or tenant
14. System status interface (service health cards for MailForge, Webmail, APIs)

---

## 11. Role-Based Access Control (RBAC) Matrix

| Capability | Platform Owner | White-Label Reseller | Org Admin (Customer) | Org Member / End User |
|---|---|---|---|---|
| **Webmail & Mobile App** | Optional | Optional | Yes | Yes |
| **Own Mailbox Settings & Contacts/Calendar** | Yes | Yes | Yes | Yes |
| **Manage Org Domains & DNS** | View/Override | For own customers | Yes | No |
| **Create / Suspend / Delete Mailboxes** | View/Override | For own customers | Yes | No |
| **Org Billing & Subscriptions** | View/Override | Manage customer plans | Yes | No |
| **Reseller Brand & Custom Domain Config** | View/Override | Yes | No | No |
| **Reseller Customer & Pricing CRUD** | View/Override | Yes | No | No |
| **Master Tenant / Reseller / Feature Flags** | Yes | No | No | No |
| **Platform Audit Logs & System Status** | Yes | Scoped to own orgs | Scoped to own org | No |

---

## 12. Design System & White-Label Theme Architecture

### 12.1 PingDart Default Theme Palette
- **Primary**: `#01A8F2`
- **Secondary**: `#087FC1`
- **Dark Ink / Nav**: `#172B3A`
- **Light Background**: `#F5F8FA`
- **Light Surface**: `#FFFFFF`
- **Typography**: `Inter` (Web) / system sans-serif or `Inter` (Flutter), clear heading hierarchy, accessible contrast (WCAG AA)
- **Design Principles**: Clean professional interface, minimal unnecessary animations, responsive layouts, consistent iconography, Light and Dark mode parity

### 12.2 `BrandTheme` Model (Light/Dark + Semantic Tokens)

```ts
export interface ThemePalette {
  primary: string;
  secondary: string;
  background: string;
  surface: string;
  surfaceElevated: string;
  text: string;
  mutedText: string;
  border: string;
  success: string;
  warning: string;
  error: string;
  info: string;
}

export interface BrandTheme {
  tenantId: string;
  companyName: string;
  logoLight: string;
  logoDark: string;
  favicon: string;

  light: ThemePalette;
  dark: ThemePalette;

  branding: {
    showPoweredBy: boolean;
    supportEmail: string;
    supportUrl: string;
    websiteUrl: string;
    termsUrl: string;
    privacyUrl: string;
  };
}
```

### 12.3 Flutter White-Label Branding Strategy
Support two modes:
1. **Runtime Branding (Default):**
   - A single PingDart Flutter binary supports multiple reseller brands dynamically.
   - Brand configuration (`BrandTheme` JSON) is resolved at login/domain lookup and cached locally for offline/splash continuity.
2. **Build-Time Branding (Optional Premium Reseller Feature):**
   - Separate Flutter build flavors (`--flavor <partner>`) for partners requiring their own app name, launcher icon, bundle ID (`applicationId` / `CFBundleIdentifier`), and independent App Store / Google Play listing.

---

## 13. Demo Tenant Switcher & Mock Architecture

To demonstrate multi-tenancy and white-labeling before backend integration:
- Include a **Demo Tenant Switcher** (accessible in dev/demo mode via header pill or settings drawer) with at least 3 preset profiles:
  1. `PingDart Default` (Platform default theme)
  2. `Acme Cloud Mail` (Reseller brand A — custom colors/logo)
  3. `NovaCorp Enterprise` (Reseller brand B — dark-first or custom accent)
- Allow switching **Demo Role** (`Platform Owner`, `Reseller`, `Org Admin`, `End User`) to preview RBAC-gated navigation and screens.
- All data fetching must go through typed service interfaces (`MailService`, `AuthService`, `TenantService`, `BillingService`, `ContactsService`, `CalendarService`) backed by mock providers.

---

## 14. Complete Navigation & Screen Inventory Summary

| Experience | Total Core Modules / Screens |
|---|---|
| **Webmail (`App/`)** | Auth (4), Inbox/Folders (8 folders), Thread View, Compose, Search, Snoozed, Templates, Contacts, Calendar, Settings |
| **Flutter Mobile (`FlutterEmail/`)** | Splash, Onboarding, Auth (4), Biometric Lock, Account Setup, Folder Drawer, Inbox, Thread View, Compose, Search, Attachments, Contacts, Calendar, Notifications, Settings, Profile, Tablet Split-View |
| **SaaS Customer Portal (`App/`)** | Dashboard, Organization & Team/RBAC, Domains & DNS, Mailboxes & Aliases, Subscriptions & Billing |
| **White-Label Reseller Portal (`App/`)** | Dashboard, Brand Customization, Custom Domains, Customer Management, Pricing & Plans |
| **Master Admin Portal (`App/`)** | Global Dashboard, Tenants, Resellers, Customers, Domains, Mailboxes, Plans, Pricing, Billing, White-Label Defaults, Announcements, Support, Audit Logs, Feature Flags, System Status |

---

## 15. Frontend Development Sequence & Acceptance Criteria

### Development Sequence
1. Clean and consolidate `prd.md` *(Completed)*
2. Complete missing functional and UI requirements *(Completed)*
3. Define RBAC and white-label architecture *(Completed)*
4. Create complete screen-by-screen UI specifications (layouts, components, interactions, mock data)
5. Define reusable UI components and design system tokens
6. Prepare frontend implementation tasks for coding agents (`App/` and `FlutterEmail/`)

### Final Acceptance Criteria (Frontend Phase)
- [ ] All 5 frontend experiences work end-to-end with mock data behind typed service interfaces
- [ ] Webmail inbox, thread view, compose, search, snoozed, templates, contacts, calendar, and settings are complete
- [ ] Branded Auth (Login, Forgot Password, OTP, 2FA) works across Webmail and Flutter Mobile
- [ ] Flutter mobile screens, folder drawer, and tablet master-detail layout are implemented
- [ ] SaaS customer, Reseller, and Master Admin portals are complete with RBAC enforcement
- [ ] Dynamic Light/Dark `BrandTheme` and Demo Tenant Switcher work without redesigning UI
- [ ] Responsive desktop, tablet, and mobile layouts plus loading, empty, and error states are tested
- [ ] Existing `Email/` (MailForge) backend remains untouched
