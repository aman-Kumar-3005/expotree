# Exhibition listings and stall enquiries

## Overview

An exhibition portal for browsing events, requesting stalls, and registering shopper passes. PHP pages and JSON endpoints share a PDO connection, while an admin area manages event content and submissions. Static HTML versions are also retained in the repository.

## System architecture and modules

| Path | Responsibility |
| --- | --- |
| `index.php / events.php / event.php` | Database-backed event browsing and event detail pages. |
| `book-a-stall.php / free-shopper-pass.php` | Stall enquiry and shopper-pass forms. |
| `list-your-event.php` | Event submission workflow. |
| `api/events.php / api/calculate.php` | Event responses and date/stall/add-on pricing calculation. |
| `admin/` | Login, event CRUD, bookings, passes, imports, submissions, and settings. |
| `includes/` | Shared configuration, PDO connection, helpers, header, and footer. |
| `database/schema.sql` | Initial admin, event, booking, and shopper-pass tables. |
| `database/upgrade_schema.php` | Additional event fields, event_stalls, and site_settings. |

## Tech stack

PHP, PDO MySQL, MySQL/MariaDB, HTML, CSS, and vanilla JavaScript. No Laravel or Composer application is present.

## Local setup

Use PHP 8.x with PDO MySQL and a local MySQL/MariaDB server. `schema.sql` creates and selects `expotree_db`; use a disposable local server/database.

```bash
git clone https://github.com/shreyasingh3005/expotree.git
cd expotree
mysql -u root -p < database/schema.sql
```

Create `includes/config.local.php` with local `DB_HOST`, `DB_PORT`, `DB_USER`, `DB_PASS`, and `DB_NAME` constants; `includes/config.php` loads it before applying defaults. Keep this file out of commits. Alternatively supply all five settings through environment variables; use `DB_NAME=expotree_db` for the schema above.

```bash
php database/upgrade_schema.php
php -S 127.0.0.1:8000
```

Open `http://127.0.0.1:8000/index.php`. The clean schema contains no application records. Inspect `database/seed.php` before using it on a disposable database: it contains fixed seed accounts and sample event content. Create your own hashed admin credential for any non-demo installation. Do not reuse the deployment dump or the browser-accessible deployment utility as a routine installer.

## Implementation notes and limits

- The main tables are `admin_users`, `events`, `bookings`, `shopper_passes`, `event_stalls`, and `site_settings` after the upgrade.
- `api/calculate.php` reads an event, selects a stall rate, counts inclusive dates, and adds the fan charge. Pricing output is not a payment or a confirmed allocation.
- Configuration contains hosting-specific defaults. Replace credentials and audit historical database content before deployment.
- Review access control around import/setup scripts, CSRF handling, and server-side validation before exposing the admin or booking workflows. These are source-review notes; a complete authenticated security or deployment test has not been performed.
