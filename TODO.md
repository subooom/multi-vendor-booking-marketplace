# Accommodation Marketplace: Project TODO

Stack: Laravel + TCG Voyager. Source: Developer RFP v1.0 (Sept 2026).
Legend: `[ ]` todo, `[x]` done. Section numbers in brackets (e.g. RFP §8) refer to the RFP.

---

## 0. Decisions and Research (do first)

- [ ] Confirm stack: Laravel + Voyager + MySQL/PostgreSQL, API-first where practical (RFP §29)
- [ ] Document native vs plugin/package vs third-party vs custom functionality (RFP §29)
- [ ] Payment gateway comparison: Razorpay, Cashfree, PhonePe PG, PayU (RFP §9)
  - [ ] Marketplace split/route support (Razorpay Route, Cashfree Easy Split, etc.)
  - [ ] Fees, settlement time, refund API, webhook reliability
  - [ ] Recommend one for MVP
- [ ] Map provider comparison: Google Maps, Mapbox, OpenStreetMap (RFP §15)
  - [ ] API pricing and expected monthly usage cost
  - [ ] Pick provider for: map picker, search map, geocoding
- [ ] SMS/OTP provider choice (India DLT registration if applicable)
- [ ] Email provider choice (SES, Mailgun, Postmark, SMTP)
- [ ] Hosting recommendation and monthly cost (server, DB, storage, CDN)
- [ ] List all third-party licences and recurring costs (RFP §30)
- [ ] Choose initial launch geography (one manageable market) (RFP §35)
- [ ] Define GST/tax handling for commission and stays
- [ ] Define original brand and visual system (do not copy Listeo) (RFP §25)

---

## 1. Project Setup

- [ ] Laravel project + Git repo + branching strategy
- [ ] Install and configure TCG Voyager
- [ ] Environments: local, staging, production
- [ ] `.env` management and secrets handling
- [ ] Queue driver (database/Redis) and scheduler (cron) configured
- [ ] Mail, storage disk (public + private), cache and session configured
- [ ] Code style / static analysis (Pint, PHPStan/Larastan)
- [ ] CI pipeline (tests on push)
- [ ] Error monitoring (Sentry/Flare) and logging
- [ ] Seed roles: `customer`, `vendor`, `staff` (plus Voyager `admin`)
- [ ] Morph map enforced in `AppServiceProvider`

---

## 2. Database: Models and Migrations

### Batch 1: Vendors and Catalogue (file delivered)
- [ ] `users` extra columns (phone, phone_verified_at, is_blocked)
- [ ] `vendors`
- [ ] `vendor_documents`
- [ ] `property_types`
- [ ] `destinations`
- [ ] `amenities`
- [ ] `properties`
- [ ] `amenity_property` (pivot)
- [ ] `room_types`
- [ ] `amenity_room_type` (pivot)
- [ ] `images` (polymorphic)
- [ ] `faqs`

### Batch 2: Inventory and Pricing
- [ ] `cancellation_policies`
- [ ] `cancellation_policy_rules`
- [ ] FK migration: `cancellation_policy_id` on `properties` and `room_types`
- [ ] `room_inventories` (unique on `room_type_id` + `date`)
- [ ] `price_rules`
- [ ] `tax_rules`

### Batch 3: Booking and Money
- [ ] `bookings` (booking_code, snapshots)
- [ ] `booking_rooms`
- [ ] `booking_status_logs`
- [ ] `payments`
- [ ] `refunds`
- [ ] `commission_rules`
- [ ] `vendor_ledgers`
- [ ] `payouts`

### Batch 4: Engagement and Trust
- [ ] `reviews`
- [ ] `reports`
- [ ] `coupons`
- [ ] `coupon_usages`
- [ ] `wishlists`
- [ ] `otp_codes`
- [ ] `activity_logs`

### Laravel built-ins
- [ ] `notifications` table (`php artisan notifications:table`)
- [ ] `jobs`, `failed_jobs`, `sessions` tables

### Seeders
- [ ] 12 property types (RFP §2)
- [ ] Amenities incl. suitability group (family, couple, pet, accessibility, work-friendly)
- [ ] Default cancellation policies: flexible, moderate, non-refundable
- [ ] Default global commission rule
- [ ] Default tax rules (GST slabs)
- [ ] Starter destinations (state, city, locality)
- [ ] Demo data for UAT (vendor, property, rooms, inventory)

---

## 3. Voyager Admin Configuration (RFP §20)

- [ ] BREAD: users, vendors, vendor_documents
- [ ] BREAD: property_types, destinations, amenities (admin edits without a developer, RFP §2)
- [ ] BREAD: properties, room_types
- [ ] BREAD: bookings, payments, refunds, payouts
- [ ] BREAD: commission_rules, tax_rules, cancellation_policies
- [ ] BREAD: reviews, reports, coupons, faqs
- [ ] CMS: pages, posts (blog and travel guides)
- [ ] Staff role permissions: bookings, verification, finance, content, support
- [ ] Hide admin access from `vendor` and `customer` roles
- [ ] Admin dashboard widgets and KPIs
- [ ] Custom admin actions: approve / reject / request changes / suspend (vendor, property, document)
- [ ] Admin settings (site name, commission default, support contact, currency)
- [ ] Admin 2FA (RFP §23)

---

## 4. Auth and Users (RFP §3, §10)

- [ ] Customer registration and login (email + phone)
- [ ] Vendor registration flow (separate entry point)
- [ ] Mobile OTP verification (rate limited, hashed codes, attempt limits)
- [ ] Email verification
- [ ] Forgot / reset password
- [ ] Secure password hashing and session handling
- [ ] CAPTCHA/anti-bot on register, login, OTP
- [ ] Role-based route middleware (customer, vendor, staff, admin)
- [ ] Blocked-user handling (`is_blocked`)

---

## 5. Vendor Onboarding and Verification (RFP §10)

- [ ] Vendor profile form: business name, PAN, GSTIN (where applicable)
- [ ] Bank details form (encrypted account number)
- [ ] Document upload (identity, property, local tourism docs) to private disk
- [ ] Secure file validation (type, size, virus scan if possible)
- [ ] Verification status shown to vendor with remarks
- [ ] Admin review screen: approve / reject / request changes / suspend
- [ ] Email notifications for each verification outcome
- [ ] Rule: only approved vendors and properties are publishable
- [ ] Verified vendor/property badge

---

## 6. Catalogue: Properties and Rooms (RFP §2, §5, §10)

- [ ] Vendor: add/edit property wizard (type, location, description, policies, amenities, photos)
- [ ] Map location picker for owners (RFP §15)
- [ ] Image upload, ordering, cover image, alt text
- [ ] Submit property for admin approval (draft → pending → approved)
- [ ] Vendor: add/edit room types (beds, size, capacity, extra rules, meal plan, amenities, photos, units)
- [ ] Cancellation policy selection per property/room
- [ ] Cached `starting_price` updated when rooms/prices change
- [ ] Duplicate/suspicious listing checks (same address/phone/coordinates) (RFP §14)
- [ ] Property status transitions logged in audit trail

---

## 7. Availability and Calendar (RFP §6)

- [ ] Inventory generation: create `room_inventories` rows for a rolling date window (scheduled job)
- [ ] Vendor calendar UI: month / week / list views
- [ ] States: Available, Booked, Blocked, Pending, Cancelled
- [ ] Open/close dates, block dates, bulk date-range edit
- [ ] Change inventory quantity per date
- [ ] Per-date price override and minimum stay
- [ ] Guard: cannot reduce units below already booked
- [ ] Architecture hook for future iCal/channel sync (no build yet)

---

## 8. Pricing Engine (RFP §7)

- [ ] Base nightly price
- [ ] Weekday / weekend rules
- [ ] Peak / off-season rules
- [ ] Holiday / event rules
- [ ] Long-stay discounts
- [ ] Extra adult / child / bed charges
- [ ] Meal-plan supplements
- [ ] Taxes and configurable fees (GST slabs by price)
- [ ] Promotional pricing
- [ ] Vendor/property-specific rules and rule priority
- [ ] Price quote service returning a full breakdown (per night, per room, taxes, fees, discount, total)
- [ ] Transparent price breakdown at checkout before payment
- [ ] Unit tests covering all rule combinations

---

## 9. Search and Discovery: Customer Website (RFP §4, §15)

- [ ] Homepage: large search (destination, check-in, check-out, guests)
- [ ] Homepage sections: popular destinations, featured, top-rated, new properties, offers
- [ ] Search by destination, city, locality, property name, landmark
- [ ] Availability-aware results (only properties with inventory for the dates)
- [ ] Grid view and list view
- [ ] Map view with markers
- [ ] Filters: property type, price, rating, amenities
- [ ] Filters: instant booking, pay-at-property
- [ ] Filters: family / couple / pet / accessibility / work-friendly
- [ ] Sorting (price, rating, newest, recommended)
- [ ] Property detail page: gallery, description, amenities, rooms, rates, policies, map, reviews, cancellation policy, booking CTA
- [ ] Room detail view
- [ ] Destination pages with FAQs
- [ ] Vendor profile page
- [ ] Wishlist / favourite toggle

---

## 10. Booking Engine (RFP §8)

- [ ] Flow: property → dates → room → guests → add-ons → guest details → review price/policy → payment → confirmation → voucher
- [ ] Modes: instant booking, request-to-book, pay-at-property, partial advance
- [ ] Availability lock: row locks on `room_inventories` inside a DB transaction
- [ ] Temporary hold with expiry for Payment Pending bookings (release via scheduled job)
- [ ] Statuses: Pending, Payment Pending, Confirmed, Checked-in, Completed, Cancelled, Rejected, Refunded
- [ ] Every status change logged in `booking_status_logs`
- [ ] Snapshots stored on booking: price breakdown, policy, commission
- [ ] Unique `booking_code` traceable across all records (RFP §21)
- [ ] Vendor accept/reject for request-to-book (with timeout)
- [ ] Vendor marks checked-in / completed
- [ ] Booking voucher (PDF) and invoice (PDF)
- [ ] Guest details capture and validation
- [ ] Double-booking and race-condition tests

---

## 11. Payments and Marketplace Finance (RFP §9, §19, §21)

- [ ] Gateway integration (order create, checkout, signature verify)
- [ ] Webhooks: idempotent handling, retry-safe
- [ ] Payment success / failed pages and recovery flow
- [ ] Partial advance payment and balance tracking
- [ ] Never store card data
- [ ] Commission rules: percent and/or fixed; global, category, vendor, promotion
- [ ] Commission calculated and snapshotted at booking time
- [ ] Vendor ledger entries: booking credit, commission, refund adjustment, payout
- [ ] Earnings view: gross, commission, refunds/adjustments, net, pending, paid
- [ ] Payout requests and admin approval (manual for MVP)
- [ ] Payout history and reference numbers
- [ ] Cancellation and refund engine: flexible / moderate / non-refundable
- [ ] Auto-calculated refund from policy rules; policy shown before payment
- [ ] Admin refund override for disputes (audit logged)
- [ ] Gateway refund API integration
- [ ] Daily payment vs booking reconciliation job and report
- [ ] GST invoice for commission (if applicable)

---

## 12. Reviews and Ratings (RFP §13)

- [ ] Review eligibility: completed bookings only, one review per booking
- [ ] Overall rating plus optional cleanliness, location, hospitality, facilities, value
- [ ] Review request email after checkout
- [ ] Vendor reply
- [ ] Admin moderation: hide, remove, investigate reports
- [ ] Cached `avg_rating` / `review_count` on property updated on review changes

---

## 13. Trust and Fraud Controls (RFP §14)

- [ ] Verified badges
- [ ] Phone/email verification enforced before booking/listing
- [ ] Report property / report review
- [ ] Admin report queue
- [ ] Duplicate/suspicious listing flags
- [ ] Audit trail (`activity_logs`) for admin and finance actions
- [ ] Secure document access (signed URLs or private disk)
- [ ] Payment/booking reconciliation checks

---

## 14. Coupons and Promotions (RFP §17)

- [ ] Percent or fixed discount
- [ ] Minimum booking value and max discount
- [ ] Validity dates and usage limits (total and per user)
- [ ] First-booking-only
- [ ] Restrictions: property / vendor / destination
- [ ] Apply and validate at checkout, record in `coupon_usages`
- [ ] Vendor coupon management (own properties only)
- [ ] Admin coupon management

---

## 15. Dashboards

### Customer dashboard (RFP §12)
- [ ] Profile
- [ ] Upcoming / completed / cancelled bookings
- [ ] Cancel booking (per policy) with refund preview
- [ ] Download voucher and invoice
- [ ] Payment history
- [ ] Wishlist
- [ ] Reviews (write and view)
- [ ] Notifications

### Vendor dashboard (RFP §11)
- [ ] KPIs (bookings, revenue, occupancy, pending actions)
- [ ] My properties / add property
- [ ] Rooms and inventory
- [ ] Availability calendar
- [ ] Bookings list and detail with actions
- [ ] Pricing rules
- [ ] Earnings and payouts
- [ ] Reviews and responses
- [ ] Coupons/promotions
- [ ] Documents / verification status
- [ ] Profile (incl. bank details) and support

### Messaging (RFP §16, if included in MVP scope)
- [ ] Decide: in MVP or Phase 2 (`conversations` + `messages`)
- [ ] Booking-linked customer/vendor messages
- [ ] Admin moderation and reporting

---

## 16. Notifications (RFP §16)

- [ ] Email: registration
- [ ] Email: verification (vendor and property outcomes)
- [ ] Email: booking (customer and vendor)
- [ ] Email: payment
- [ ] Email: cancellation
- [ ] Email: refund
- [ ] Email: check-in reminder
- [ ] Email: review request
- [ ] Email: payout events
- [ ] In-app notifications (Laravel `notifications` table)
- [ ] All sent via queues with retry

---

## 17. Admin Features (RFP §20)

- [ ] Dashboard/KPIs
- [ ] Users and customers management
- [ ] Vendors and verification queue
- [ ] Properties and rooms management
- [ ] Bookings management and manual status change (logged)
- [ ] Payments, commissions, payouts, refunds
- [ ] Reviews and reports moderation
- [ ] Coupons and promotions
- [ ] Destinations, categories, amenities
- [ ] CMS/blog, FAQs, support
- [ ] Settings and permissions

---

## 18. Reporting and Reconciliation (RFP §21)

- [ ] Booking volume
- [ ] Gross booking value
- [ ] Commission
- [ ] Vendor earnings
- [ ] Payouts
- [ ] Refunds
- [ ] Cancellations
- [ ] Destination / vendor / property performance
- [ ] Export CSV and Excel
- [ ] Export PDF (preferred)
- [ ] One Booking ID traces customer, vendor, property, room, payment, commission, refund and payout

---

## 19. SEO and Content (RFP §22)

- [ ] SEO-friendly URLs for properties and destinations
- [ ] Editable meta title and description
- [ ] Canonical tags
- [ ] Schema markup (Hotel/LodgingBusiness, Review, Breadcrumb, FAQ)
- [ ] Breadcrumbs
- [ ] XML sitemap (only indexable pages)
- [ ] Image alt text
- [ ] Indexable destination pages (flag-controlled); avoid thin/duplicate pages
- [ ] Blog, travel guides, FAQs through CMS
- [ ] robots.txt, Open Graph / Twitter cards

---

## 20. Static and Core Pages (RFP §26)

- [ ] About
- [ ] Contact
- [ ] FAQ
- [ ] Blog / article
- [ ] Terms, privacy, policies
- [ ] Cancellation/refund policy page
- [ ] Login / register / forgot password
- [ ] Vendor verification page
- [ ] Payment success / failed
- [ ] Booking voucher page

---

## 21. Security and Performance (RFP §23)

- [ ] HTTPS/SSL everywhere
- [ ] Rate limiting on OTP, login, search and booking endpoints
- [ ] CSRF, XSS and SQL injection review
- [ ] Policies / gates for ownership checks (vendor only touches own data)
- [ ] Secure file uploads (validation, private storage)
- [ ] Encrypted sensitive fields (bank account number)
- [ ] Audit logs
- [ ] Automated database and file backups with restore test
- [ ] Caching (config, routes, queries) and CDN
- [ ] Image optimisation (resize, WebP, lazy load)
- [ ] Core Web Vitals testing (mobile-first)
- [ ] Index review on search, inventory and booking queries

---

## 22. Responsive Design and UX (RFP §24, §25)

- [ ] Original visual system: premium, modern, trustworthy, travel-focused
- [ ] Fully responsive: Android, iPhone, tablet, desktop
- [ ] Dedicated mobile testing: search, filters, booking, checkout, payment, vendor dashboard
- [ ] Accessibility basics (contrast, focus, alt text, labels)

---

## 23. Testing and Acceptance (RFP §31)

- [ ] Unit tests: pricing, commission, refund, availability
- [ ] Feature tests: booking, payment webhook, cancellation, payout
- [ ] Concurrency test: two users booking the last unit
- [ ] Failure/recovery: payment failure, webhook retry, abandoned checkout, hold expiry
- [ ] Security, browser and mobile testing
- [ ] Performance / load testing
- [ ] Full UAT run:
  - [ ] Vendor registers
  - [ ] Submits property
  - [ ] Admin verifies
  - [ ] Vendor creates room
  - [ ] Vendor opens inventory
  - [ ] Customer searches
  - [ ] Selects dates and room
  - [ ] Price calculated
  - [ ] Customer pays
  - [ ] Booking confirmed
  - [ ] Vendor receives booking
  - [ ] Commission recorded
  - [ ] Voucher issued
  - [ ] Stay completed
  - [ ] Review request sent
  - [ ] Review submitted
  - [ ] Vendor responds
  - [ ] Payout reconciled

---

## 24. Launch, Handover and Support (RFP §30, §32, §34)

- [ ] Production deployment and monitoring
- [ ] Analytics setup (GA4 / Search Console)
- [ ] Data seeding for launch market
- [ ] Documentation: setup, architecture diagram, admin guide, vendor guide, API/payment/payout flows
- [ ] Handover: source code, database, design files, custom packages, accounts (domain, hosting, gateway, analytics)
- [ ] Warranty period (3 to 6 months bug fixes) defined
- [ ] Maintenance plan: updates, backups, server management, support SLA, future-dev rates
- [ ] Quotation breakdown (RFP §33) with GST shown separately
- [ ] Recurring annual costs listed separately

---

## 25. Phase 2 (after MVP)

- [ ] WhatsApp Business API: confirmations, vouchers, reminders (`whatsapp_logs`)
- [ ] Automated payouts via gateway
- [ ] iCal / channel synchronisation (`ical_feeds`)
- [ ] Advanced pricing (`rate_plans`)
- [ ] Featured and sponsored listings (`featured_listings`): duration, price, position, eligibility (RFP §18)
- [ ] Subscriptions
- [ ] Messaging (`conversations`, `messages`) if not in MVP
- [ ] Disputes (`disputes`)
- [ ] Advanced analytics
- [ ] CRM
- [ ] Loyalty points

---

## 26. Phase 3 (future)

- [ ] Android and iOS apps (customer and vendor)
- [ ] AI search and recommendations
- [ ] Loyalty programme
- [ ] Generalise `properties` into a `listings` pattern
- [ ] Experiences, activities, sightseeing, guides
- [ ] Airport transfers, taxi / car rental
- [ ] Dynamic packages (hotel + transport/activity)