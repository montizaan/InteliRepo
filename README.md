# InteliRepo

InteliRepo is a multi-tenant policy and process management application.  
It is currently in a baseline stage with authentication and frontend scaffolding in place, plus initial domain design for change requests, approvals (four-eye principle), versioning, and audit logging.

## Tech Stack

- Backend: Laravel 12 (PHP 8.2+)
- Frontend: Vue 3 + Inertia.js
- Build Tooling: Vite + TypeScript
- Styling/UI: Tailwind CSS 4
- Database: Laravel-supported SQL databases (configured via `.env`)

## Getting Started

### Prerequisites

- PHP 8.2+
- Composer
- Node.js + npm
- A SQL database instance (or SQLite for local development)

### Installation

```bash
composer install
cp .env.example .env
php artisan key:generate
php artisan migrate
npm install
```

### Run the Application (Development)

```bash
composer run dev
```

This starts:
- Laravel development server
- Queue listener
- Log watcher
- Vite dev server

## Testing & Code Quality

```bash
composer run test
```

## Project Documentation

- [Architecture Docs](./docs/README.md)
- [Domain Data Model](./docs/data-model.md)
- [State Machines](./docs/state-machines.md)
- [Application Workflows](./docs/workflows.md)

## Next Steps

- Implement the domain data model in Laravel migrations and Eloquent models.
- Add business logic and services for:
  - policy/process versioning
  - change request lifecycle
  - two-person approval enforcement (four-eye principle)
  - audit logging
- Add feature tests that validate RBAC and approval workflows.

