# PingDart FlutterEmail

Flutter Android and iOS mobile email client for **PingDart Webmail** — a multi-tenant SaaS and white-label email platform.

> **Current Phase:** Frontend UI & architecture specification phase. Uses mock data behind typed service interfaces. The existing MailForge backend (`../Email/`) remains untouched during this phase.

---

## Workspace Context

`FlutterEmail/` is part of the PingDart monorepo/workspace:

| Directory | Responsibility |
|---|---|
| `App/` | Next.js Webmail, SaaS Customer Portal, White-Label Reseller Portal, and Master Admin Portal |
| `FlutterEmail/` | Flutter Android & iOS mobile application *(this directory)* |
| `Apis/` | Existing backend APIs (future integration target) |
| `Email/` | MailForge email infrastructure (untouched in frontend phase) |

---

## Core Features

- **Authentication & Security**: Branded Login, Forgot Password, OTP verification, 2FA setup/challenge, Onboarding, and Biometric Lock (Face ID / Touch ID / Fingerprint)
- **Mail**: Inbox, slide-out Folder Drawer (`Inbox`, `Starred`, `Snoozed`, `Sent`, `Drafts`, `Archive`, `Spam`, `Trash`, `Templates`), Thread/Conversation view, Rich-text Compose, Swipe gestures, Pull-to-refresh, and Multi-account switching
- **Search & Attachments**: Instant search with filter chips (`From`, `Folder`, `Date`, `Attachments`, `Unread`) and attachment preview/download manager
- **Productivity**: Contacts (searchable list, groups, CRUD) and Calendar (`Agenda`, `Day`, `Month` views, event creation, RSVP)
- **Adaptive Layouts**: Bottom navigation on phones and Master-Detail split view with Navigation Rail on tablets/foldables
- **Offline & Notifications**: Cached offline email viewing UI, notification history, and deep-link handling

---

## White-Label Branding Strategy

1. **Runtime Branding (Default)**: A single Flutter binary dynamically loads and caches the active tenant's `BrandTheme` (logos, light/dark palettes, semantic colors, and support links) after login or domain lookup.
2. **Build-Time Branding (Optional Premium Reseller Feature)**: Separate Flutter build flavors (`--flavor <partner>`) for partners requiring a custom app name, launcher icon, bundle ID, and dedicated App Store / Google Play listing.

---

## Tech Stack

- **Framework**: Flutter (Android & iOS)
- **State Management**: Riverpod
- **Theming**: Dynamic `BrandTheme` with Light & Dark `ColorScheme` and semantic tokens (`success`, `warning`, `error`, `info`)
- **Icons**: Material Symbols

---

## Planned Directory Structure

```text
FlutterEmail/
├── doc/
│   └── prd.md                  # Master UI/Frontend PRD
├── lib/
│   ├── core/                   # Constants, utils, service interfaces, mock data
│   ├── theme/                  # Dynamic BrandTheme & light/dark ColorScheme
│   ├── navigation/             # Router, bottom nav, folder drawer, tablet rail
│   ├── features/
│   │   ├── auth/               # Login, forgot password, OTP, 2FA, biometric lock
│   │   ├── mail/               # Inbox, folders, thread view, compose, search, snoozed, templates
│   │   ├── contacts/           # Address book, groups, contact detail
│   │   ├── calendar/           # Agenda/day/week/month views, event editor
│   │   └── settings/           # Account switcher, preferences, theme, notifications
│   └── widgets/                # Reusable Flutter UI components
└── test/
```

---

## Documentation

- [Product Requirements Document (PRD)](./doc/prd.md)

