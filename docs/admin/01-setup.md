---
layout: default
title: Admin Setup Guide
parent: Admin
---

# Admin Panel Setup Guide

## Overview

The Pro-Hort Admin panel is a Vue.js-based administrative interface for managing all Pro-Hort operations, users, and system settings.

## Prerequisites

- Node.js 18+
- npm or yarn
- Git
- Modern web browser
- Administrator access to the server

## Installation

### 1. Clone the Repository

```bash
cd /Users/matt/Work/Pro-Hort\ Dashboard/Project
git clone <admin-repo-url> pro-hort_admin
cd pro-hort_admin
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Environment Configuration

Create a `.env` file in the root directory:

```env
VITE_API_URL=http://localhost:3000/api
VITE_APP_NAME=Pro-Hort Admin
VITE_ADMIN_SECRET=your-admin-secret-key
```

### 4. Development Server

Start the development server:

```bash
npm run dev
```

The admin panel will be available at `http://localhost:5174`

## Technology Stack

- **Framework**: Vue 3 (Composition API)
- **Build Tool**: Vite
- **Styling**: Tailwind CSS with Provar color palette
- **UI Components**: PrimeVue
- **State Management**: Pinia
- **Testing**: Vitest, Cypress

## Project Structure

```
src/
├── components/
│   ├── BreederFormDialog.vue
│   ├── FiltersBar.vue
│   ├── IpManagerFormDialog.vue
│   ├── PageHeader.vue
│   ├── PaginatedTableContainer.vue
│   ├── TableContainer.vue
│   └── TastingEventField.vue
├── composables/
│   ├── createCrudComposable.js
│   ├── useBreeders.js
│   ├── useCultivars.js
│   ├── useIpManager.js
│   └── useTastingEvents.js
├── router/
├── stores/
├── utils/
├── views/
├── App.vue
└── main.js
```

## Key Components

### 1. CRUD Composable

The `createCrudComposable` provides standard CRUD operations:

```javascript
import { createCrudComposable } from '@/composables/createCrudComposable'

const { items, loading, create, read, update, delete: deleteItem } = 
  createCrudComposable('/api/endpoint')
```

### 2. Form Dialogs

Form dialogs for creating/editing items:
- `BreederFormDialog.vue` - Breeder management
- `IpManagerFormDialog.vue` - IP address management

### 3. Data Tables

Paginated table containers with:
- Sorting
- Filtering
- Bulk actions
- Export functionality

## Database Setup

The admin panel requires the Pro-Hort server database. See the [Server Setup Guide](../server/01-setup.md) for database configuration.

### Create Tables

```bash
# Run database migrations
npm run migrate
```

## Authentication

### Admin User Creation

```bash
# Create admin user (run from server)
node scripts/create-admin.js --email admin@pro-hort.com --password secure-password
```

### Login

1. Navigate to the admin panel
2. Enter credentials
3. Complete 2FA if enabled
4. Access the dashboard

## API Integration

### API Configuration

The admin panel communicates with the Pro-Hort server API. Configure the endpoint:

```javascript
// src/main.js
const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:3000/api'
```

### Authentication Token

Admin panel automatically handles JWT authentication:
1. Token is stored in localStorage
2. Token is included in all API requests
3. Token refresh on expiration is automatic

## Running Tests

### Unit Tests

```bash
npm run test:unit
```

### E2E Tests

```bash
npm run test:e2e
```

## Troubleshooting

### Login Issues

- Verify admin user exists
- Check server is running
- Clear browser cookies and cache
- Verify `VITE_API_URL` is correct

### Database Connection Errors

- Ensure database is running
- Check database credentials in `.env`
- Run migrations: `npm run migrate`

### API Connection Failed

- Check server is running on port 3000
- Verify `VITE_API_URL` environment variable
- Check CORS configuration on server

## Next Steps

- Review the [Usage Guide](02-usage.md)
- See [API Reference](03-api-reference.md) for API endpoints
- Start managing users and settings
