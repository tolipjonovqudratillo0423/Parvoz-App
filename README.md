# Parvoz — Restaurant Management System

A Django-based restaurant management system with role-based dashboards for waiters, chefs, cashiers, and admins. Built with a multi-app architecture and deployed via Docker.

## Status
In development / deployed via Docker (Railway config included)

## Tech Stack
- Python / Django
- PostgreSQL
- Docker & docker-compose
- Server-side rendering (Django templates)
- Jazzmin admin theme
- Railway (deployment config: `railway.json`)

## Architecture

The project is split into role-specific Django apps:

```
apps/
├── accounts/      # Auth, Organization model, user management
├── admin_panel/   # Admin dashboard
├── waiter/        # Waiter dashboard, orders, tables, menu, notifications
├── chef/          # Chef order queue and status views
├── cashier/       # Cashier dashboard, payments, reports
└── orders/        # Core domain models: Table, Food, Order, OrderItem, Notification
```

## Domain Model

- **Organization** — top-level tenant; tables and orders are scoped to an organization
- **Table** — indoor/outdoor, tracks `free`/`occupied` status
- **Food** — categorized as main / drink / dessert
- **Order** — linked to organization, user, and table; status flow: `pending → ready → delivered → paid` (or `cancelled`); computed `total` property from order items
- **OrderItem** — links an order to a food item with quantity
- **Notification** — per-user, viewed/unviewed

All core models inherit from a shared `BaseModel` (`is_active`, `created_at`, `updated_at`).

## Role-Based Access

Each role (waiter, chef, cashier, admin) has its own dashboard, views, and URL namespace, with view-level mixins controlling access per role.

## Key Design Notes

- Order cancellation is handled via status change, not row deletion — preserves order history
- Composite DB indexes on `Order` (`table`, `user`, `status`) for dashboard query performance
- Notification system supports polling for live updates on waiter dashboards

## Running Locally

```bash
git clone <repository_url>
cd parvoz

python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

pip install -r requirements.txt

python manage.py migrate
python manage.py runserver
```

Or with Docker:

```bash
docker-compose up --build
```

## Roadmap
- [ ] Public deployment / live demo link
- [ ] Automated test coverage across all apps
- [ ] API layer (currently server-rendered only)