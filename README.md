# Thali — Restaurant Operating System

> *Poora restaurant, ek Thali mein.* The whole restaurant on one plate.

**Thali** is a modern point-of-sale and operations platform for Indian restaurants, cafés and cloud kitchens. It brings billing, kitchen tickets, table service, QR ordering, inventory, customers and reports together in one fast app. That app keeps working when the internet doesn't.

It runs on a single computer inside the restaurant. Every phone, tablet and kitchen screen on the same Wi-Fi connects to it through the browser, with nothing to install on those devices.

---

## Why Thali

Most small restaurants juggle a billing machine, paper KOTs, a separate aggregator tablet and a notebook for stock. Thali replaces all of them with one system:

- **Fast at the counter:** touch- or keyboard-driven billing in a few taps.
- **Live everywhere:** an order placed at a table shows up instantly in the kitchen, the captain's phone and the owner's dashboard.
- **Offline-first:** if the internet drops, the restaurant keeps billing. Actions sync automatically when the connection returns.
- **Built for India:** GST, UPI QR on bills, area-wise pricing, Zomato/Swiggy orders, WhatsApp eBills.

---

## Features

### Front of house
- **POS & billing:** dine-in, takeaway and delivery; discounts, split payments, GST, round-off, thermal-printer bills and KOTs.
- **Captain app:** waiters take orders at the table on their phone, with dish photos and one-tap "Send KOT".
- **Table management:** floor plan by area, with live table status (free, running, billed).
- **Guest QR ordering:** guests scan the table QR, browse a photo menu, order, track their food live, and call the waiter, ask for the bill or request water with one tap.
- **Digital menu & eBill:** a shareable menu link, and bills with a UPI pay link.

### Kitchen
- **Kitchen display (KDS):** colour-coded tickets (new, cooking, ready, served) with dish photos and timers.
- **Live orders board:** every running order as a card, with items, kitchen status and actions (View, Add items, Serve, Bill).

### Back office
- **Menu management:** categories, food photos, area-wise and online prices, stock toggles.
- **Inventory:** ingredients, purchases, wastage, transfers between outlets, recipe costing.
- **Customers & loyalty:** visit history, reward points, WhatsApp contact.
- **Expenses & payments:** daily expense tracking and payment-mode breakdowns.
- **Reports:** item-wise, category, payment, staff and outlet reports, with CSV export and print.
- **Online orders:** a single screen for Zomato, Swiggy and website orders via webhook integration.
- **Multi-outlet:** run several branches from one system, with outlet-wise menus, stock and sales.
- **Roles & permissions:** owner, manager, cashier, captain and kitchen, each with adjustable privileges.

---

## How it works

```
   Phones · Tablets · Kitchen screens · Guest phones (QR)
                        │  Wi-Fi (browser)
                        ▼
   ┌───────────────────────────────────────────────┐
   │  Frontend  — Astro + JavaScript (PWA)         │
   │  staff app · captain · KDS · QR menu · eBill  │
   ├───────────────────────────────────────────────┤
   │  Backend   — Rust (Axum, Tokio)               │
   │  REST API · real-time events · business rules │
   ├───────────────────────────────────────────────┤
   │  Database  — MongoDB                          │
   └───────────────────────────────────────────────┘
```

- **In-memory core, durable storage.** The backend works from memory for instant responses and writes only the changed records to MongoDB in the background.
- **Real-time updates.** Every change is pushed to all connected screens over Server-Sent Events, so nobody has to refresh.
- **Offline queue.** A service worker caches the app. Actions taken offline are queued and replayed safely when the connection returns.

A full write-up of the architecture, design decisions and trade-offs is in **[docs/TECHNICAL_PAPER.md](docs/TECHNICAL_PAPER.md)**.

---

## Tech stack

| Layer | Technology |
|---|---|
| Backend | Rust · Axum · Tokio · Serde |
| Database | MongoDB (a single JSON file for small setups) |
| Frontend | Astro · vanilla JavaScript · CSS design system · Inter font |
| Offline | Service Worker · local action queue |
| Real-time | Server-Sent Events |

---

## Security

- Staff sign in with a personal PIN. PINs are stored only as keyed hashes, never in readable form.
- Wrong-PIN lockout, automatic session expiry, and per-device sign-out from the admin panel.
- Role-based permissions on every action, with protected owner accounts.
- Strict browser security headers, and uploads are verified by their content.
- Optional auto-lock for shared counter screens.

---

## Getting started

Requirements: **Rust**, **Node.js 18+** and **MongoDB**.

```bash
npm run setup     # install frontend dependencies (first time)
npm run build     # build the web app and the Rust server
npm start         # start the server
```

Then open the address printed in the terminal on any device on the same network. On first start, the server creates the database and a server secret. Copy `.env.example` to `.env` to change the port, database or session settings.

Detailed installation, backup and maintenance steps are in **[docs/SETUP.md](docs/SETUP.md)**.

---

## Project structure

```
backend/    Rust server: API, business logic, real-time events, storage
frontend/   Astro web app: staff screens, QR menu, eBill, offline support
scripts/    Database helper scripts (start, stop, backup, restore)
docs/       Technical paper and setup guide
```

---

## Roadmap

- HttpOnly-cookie sessions and a stricter content security policy
- Native thermal-printer bridge
- Table reservations and waitlist
- Deeper aggregator integrations and menu sync
- Owner mobile dashboard

---

© Thali. All rights reserved.
