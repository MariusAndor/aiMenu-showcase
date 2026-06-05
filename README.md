# aiMenu

Multi-tenant SaaS that turns one deployment into a hosted menu + ordering system for any number of restaurants. Each tenant gets an isolated admin panel, a public QR-accessible menu, and a real-time shared cart that lets a whole table order together. Built solo, end-to-end — schema to UI.

---

## Screenshots


**Admin dashboard** — full operations view: live tables, sessions, orders

**Public menu** — what a customer sees after scanning the QR code

**Shared cart** — same cart, multiple devices, one table

**Design presets** — classic / modern / minimalist, swappable per tenant

**Table sessions** — admin activating a table so customers can order

---

## Features

- **Per-tenant admin panel** — every restaurant manages its own menu, categories, tables, staff, and design independently
- **QR-code ordering** — customers scan, browse, order; no app install
- **Shared real-time cart** — every device on the same table sees the same cart, updated via SWR polling
- **Session-gated ordering** — admin flips a table to "active" before guests can submit; closes the loop when the bill is paid
- **Design preset system** — classic, modern, and minimalist themes, hot-swappable per restaurant without touching code
- **Multi-language** — Romanian and English out of the box
- **Group consensus checkout** — order is locked in only after every guest at the table confirms they're ready
- **Live orders dashboard** — staff watches every active table from one screen
- **Bilingual allergen + modifier tracking** — required/optional groups, single/multi-select, priced add-ons

---

## Tech stack

![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=node.js&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=for-the-badge&logo=postgresql&logoColor=white)
![SWR](https://img.shields.io/badge/SWR-000000?style=for-the-badge&logo=vercel&logoColor=white)

**Frontend:** React, TypeScript, SWR for cache + polling
**Backend:** Node.js, REST API, JWT/session auth
**Data:** PostgreSQL with strict tenant scoping at the query layer
**Realtime:** SWR revalidation on a 1-3s cadence — pragmatic, no socket infra needed

---

## Architecture

One codebase, one database, many tenants.

```
        ┌─────────────────────────────────────────────────┐
        │              Single deployment                  │
        │                                                 │
        │   Public menu (/[slug])      Admin (/[slug]/*)  │
        │   QR landing  (/[slug]/      Live orders        │
        │                 [tableId])   Menu CMS           │
        │                                                 │
        └─────────────────────────────────────────────────┘
                              │
                              ▼
        ┌─────────────────────────────────────────────────┐
        │           PostgreSQL — single DB                │
        │  Every row scoped by restaurantId at the API    │
        │  layer. No tenant ever sees another tenant's    │
        │  data — enforced in every query, not just UI.   │
        └─────────────────────────────────────────────────┘
```

**Isolation model:**
- Each restaurant identified by `restaurantId` (slug-routed)
- Every API handler resolves the tenant from the session/URL and scopes all reads + writes to that ID
- Admin accounts are bound to one restaurant; superadmin is the only cross-tenant role
- Public menu routes are read-only and filter by slug

**Session model:**
- Admin opens a table → creates a `TableSession`
- Customers scan QR → join the session with a device token
- Cart items belong to the session, not the device — so any guest sees the running total
- Order is committed when every guest marks themselves ready

---

## Source

Source code available upon request — reach out if you'd like a walkthrough or access.
