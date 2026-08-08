Flowline

A SaaS platform for barbershops and salons — combining appointment scheduling and walk-in queue management in a single real-time timeline.

Currently in active development. Built independently as a real-world software product, not a tutorial project.

What is Flowline?

Small salons and barbershops in Germany lose time and money every day to chaotic walk-in management, no-shows, and switching between paper calendars and WhatsApp. Flowline solves this by combining everything into one dashboard:

Scheduled appointments and spontaneous walk-ins appear together on a unified live timeline
Customers scan a QR code at the entrance, add themselves to the queue, and see their position and estimated wait time in real time on their phone
The system calculates wait times dynamically based on active appointments and queue length
Email and SMS reminders go out automatically 3 hours, 1 day, and 1 week before appointments to reduce no-shows
Every status change — a customer arriving, a service finishing, someone not showing up — updates instantly across all devices via WebSockets

The goal: less chaos, fewer no-shows, and a professional experience for both the salon owner and their customers.

Tech Stack
Layer	Technology
Backend	Python, Flask, Flask-Login, Flask-Bcrypt
Realtime	Flask-SocketIO (WebSockets)
Database	SQLite + SQLAlchemy ORM
Frontend	HTML, Tailwind CSS, JavaScript
Calendar	FullCalendar.js
Email	smtplib, EmailMessage (HTML emails)
Auth	Session-based authentication with bcrypt password hashing
Architecture	Multi-table relational schema (providers, credentials, services, appointments, queue entries)
Features
For the salon owner
Unified timeline — appointments and walk-ins on one FullCalendar view, drag-and-drop to reschedule
Live queue dashboard — see who is waiting, who is currently being served, and average wait time
Walk-in management — add customers manually or let them self-register via QR code
Appointment creation — with customer name, phone, email, service, and notes
Status tracking — pending → confirmed → in progress → completed / no-show, with automatic transitions based on time
No-show prevention — automated email reminders at 1 week, 1 day, and 3 hours before appointments
Settings — change email (with verification code), change password (with email confirmation), manage services, delete account
DSGVO-aligned data structure — credentials stored in a separate table from business data
For the customer
QR code check-in — scan at the entrance, enter name and service, join the queue instantly
Live queue position — see position in queue and estimated wait time on mobile, updated in real time
Planned (in development)
Customer reactivation (Premium tier)
Stripe deposit for no-show protection
SMS notifications via Twilio
Multi-staff support
Analytics dashboard (peak hours, most booked services, average wait time over time)
Development Process

I started Flowline with a simple Flask app and SQLite — basic appointment creation and a FullCalendar frontend. From there the project grew in complexity with every feature I added:

Phase 1 — Core scheduling Basic Flask routes, SQLite schema, FullCalendar integration with drag-and-drop (eventDrop, eventResize), conflict detection, and status management.

Phase 2 — Walk-in queue Designed and implemented a dynamic queue system where walk-ins are automatically slotted into free time gaps around existing appointments. Built build_timeline() and find_free_slot() — a scheduling algorithm that calculates the earliest available slot for each walk-in based on all active appointments and already-queued customers.

Phase 3 — Real-time updates Integrated Flask-SocketIO for live dashboard updates. Implemented room-based emit so each provider only receives their own updates. Solved race conditions between HTTP load and WebSocket connection on page refresh by switching initial data loading to HTTP/JSON and using SocketIO only for live updates.

Phase 4 — Authentication and security Registration with email verification (4-digit code, 5-minute expiry, async timer), bcrypt password hashing, Flask-Login session management, "remember me" functionality via persistent cookies, and secure email/password change flows with code confirmation.

Phase 5 — Database migration Migrated from raw SQLite with cursor.execute() to SQLAlchemy ORM with a fully relational schema. Split provider data into separate tables (providers, provider_credentials, provider_services) for better structure and DSGVO compliance. Learned to handle connection pooling, lazy loading, and the difference between naive and timezone-aware datetimes in SQLite.

Phase 6 — Settings and onboarding Built a Google Accounts-style settings page with tabbed navigation. Added an onboarding flow that requires new providers to set up at least one service before accessing the dashboard — ensuring the timeline always has accurate duration data.

Currently working on

QR code self-registration page for customers
Queue position display on mobile
Automated email reminders via SMTP
What I Learned
Full-stack architecture — designing a product end-to-end, from database schema to frontend UX, with every layer communicating correctly
Real-time systems — how WebSockets work, when to use them vs. HTTP, and how to handle connection lifecycle edge cases
Relational database design — normalization, foreign keys, one-to-many relationships, and why separating sensitive data (credentials) from business data matters for security and compliance
Timezone handling — one of the hardest practical problems in backend development: naive vs. aware datetimes, UTC as the canonical storage format, and how mixing them silently breaks comparisons
SQLAlchemy ORM — models, relationships, joinedload for eager loading, session lifecycle, and connection pool management
Authentication security — bcrypt hashing, session-based auth, email verification flows, and token expiry patterns
Product thinking — analyzing competitors (Fresha, Booksy, Shore), identifying gaps in the market, and designing features around real user pain points rather than technical possibilities
Mission

Barbershops in Germany still run on WhatsApp messages and paper calendars. They lose revenue to no-shows they could have prevented, and customers stand around not knowing how long they will wait.

Flowline's mission is to give small service businesses a tool that is simple enough to use on day one, powerful enough to replace every other system they are using, and affordable enough that a single-chair barbershop can justify the cost. Not another enterprise platform with a 60-page onboarding guide — a focused tool that solves exactly the problems these businesses actually have.

Project Status

Active development. Core scheduling, queue management, authentication, and settings are functional and being tested. QR code check-in, SMS notifications, and analytics are in progress.
