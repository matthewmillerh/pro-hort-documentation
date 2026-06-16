---
layout: default
title: Dashboard Setup Guide
parent: Dashboard
---

# Dashboard Setup Guide

## Overview
The Pro-Hort Dashboard is a Vue.js-based web application that provides users with a comprehensive interface to manage Pro-Hort operations.

## Prerequisites

- Node.js 18+ 
- npm or yarn
- Git
- Modern web browser

## Installation

### 1. Clone the Repository

```bash
cd /Users/matt/Work/Pro-Hort\ Dashboard/Project
git clone <dashboard-repo-url> pro-hort_dashboard
cd pro-hort_dashboard
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Environment Configuration

Create a `.env` file in the root directory:

```env
VITE_API_URL=http://localhost:3000/api
VITE_APP_NAME=Pro-Hort Dashboard
```

### 4. Development Server

Start the development server:

```bash
npm run dev
```

The dashboard will be available at `http://localhost:5173`

## Technology Stack

- **Framework**: Vue 3 (Composition API)
- **Build Tool**: Vite
- **Styling**: Tailwind CSS
- **UI Components**: PrimeVue
- **State Management**: Pinia
- **Testing**: Vitest, Cypress

## Project Structure

```
src/
├── components/       # Reusable Vue components
├── composables/      # Vue composables (reusable logic)
├── router/          # Route definitions
├── stores/          # Pinia stores (state management)
├── utils/           # Utility functions
├── views/           # Page components
├── App.vue          # Root component
└── main.js          # Application entry point
```

## Configuration

### Tailwind CSS Integration

The dashboard uses Tailwind CSS with the Provar color palette. Available utility classes:
- `bg-provar-grey-100` through `bg-provar-grey-900`
- `text-provar-*`
- `border-provar-*`

### PrimeVue Components

All PrimeVue components are pre-configured in `main.js`. Import components as needed:

```javascript
import { Button, DataTable, Dialog } from 'primevue/components'
```

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

### Port Already in Use
If port 5173 is in use, Vite will automatically use the next available port.

### Module Not Found
Clear node_modules and reinstall:
```bash
rm -rf node_modules package-lock.json
npm install
```

### API Connection Issues
Check that the server is running and `VITE_API_URL` is correctly configured.

## Next Steps

- Review the [Usage Guide](02-usage.md)
- Explore [Deployment Guide](03-deployment.md) for production setup
