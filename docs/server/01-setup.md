---
layout: default
title: Server Setup Guide
parent: Server
---

# Server Setup Guide

## Overview

The Pro-Hort Server is a Node.js/Express-based backend API that powers the Dashboard and Admin Panel. It handles data management, authentication, and business logic.

## Prerequisites

- Node.js 18+
- npm or yarn
- PostgreSQL 12+
- Git
- Redis (optional, for caching)

## Installation

### 1. Clone Repository

```bash
cd /Users/matt/Work/Pro-Hort\ Dashboard/Project
git clone <server-repo-url> pro-hort_server
cd pro-hort_server
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Database Setup

#### PostgreSQL Installation

**macOS (Homebrew):**
```bash
brew install postgresql
brew services start postgresql
```

**Ubuntu/Debian:**
```bash
sudo apt-get install postgresql postgresql-contrib
sudo systemctl start postgresql
```

**Windows:**
Download and install from [postgresql.org](https://www.postgresql.org)

#### Create Database

```bash
createuser pro_hort_user
psql -U postgres -c "ALTER USER pro_hort_user WITH PASSWORD 'secure_password';"
createdb -O pro_hort_user pro_hort_db
```

#### Run Migrations

```bash
npm run migrate
```

### 4. Environment Configuration

Create `.env` file:

```env
# Server Configuration
NODE_ENV=development
PORT=3000
LOG_LEVEL=debug

# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=pro_hort_db
DB_USER=pro_hort_user
DB_PASSWORD=secure_password

# Authentication
JWT_SECRET=your-super-secret-jwt-key-change-in-production
JWT_EXPIRY=7d

# Email Configuration
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASSWORD=your-app-password
SMTP_FROM=noreply@pro-hort.com

# CORS
CORS_ORIGIN=http://localhost:5173,http://localhost:5174

# Redis (optional)
REDIS_URL=redis://localhost:6379
```

### 5. Start Development Server

```bash
npm run dev
```

Server will run on `http://localhost:3000`

## Project Structure

```
src/
├── config/
│   ├── agentDb.js      # Agent database configuration
│   ├── db.js           # Primary database configuration
│   └── email.js        # Email service configuration
├── controllers/        # Route handlers
├── email/             # Email templates
├── middlewares/       # Express middlewares
├── models/            # Data models
├── public/            # Static files
├── routes/            # API route definitions
├── services/          # Business logic
├── tests/             # Test files
├── utils/             # Utility functions
├── views/             # Email/template views
└── index.js           # Application entry point
```

## Database Schema

### Key Tables

- **users** - User accounts and authentication
- **cultivars** - Plant cultivar information
- **breeders** - Breeder/organization data
- **tasting_events** - Event records
- **reports** - Generated reports
- **audit_logs** - System activity logs
- **ip_restrictions** - IP whitelist management
- **rootstock_reports** - Root stock test data

### View Migrations

```bash
ls sql/migrations/
```

### Creating Migrations

```bash
npm run migration:create -- --name your_migration_name
```

## API Structure

### Base URL
```
http://localhost:3000/api
```

### Authentication

```bash
# Get JWT token
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"password"}'

# Use token in headers
Authorization: Bearer <token>
```

### Example Routes

- `GET /api/cultivars` - List cultivars
- `POST /api/cultivars` - Create cultivar
- `GET /api/breeders` - List breeders
- `GET /api/reports` - List reports
- `POST /api/auth/login` - User login

## Testing

### Unit Tests

```bash
npm run test:unit
```

### Integration Tests

```bash
npm run test:integration
```

### Run All Tests

```bash
npm test
```

### Test Coverage

```bash
npm run test:coverage
```

## Development Tools

### Seed Database

Populate database with test data:

```bash
npm run seed
```

### Database Queries

Test queries with:

```bash
node test_query.cjs
```

### Reset Database

```bash
npm run db:reset
```

## Logging

### Log Levels

- `error` - Error messages
- `warn` - Warning messages
- `info` - Informational messages
- `debug` - Debug messages
- `trace` - Detailed trace messages

### Configure Logging

In `.env`:
```env
LOG_LEVEL=debug
```

### View Logs

```bash
tail -f logs/app.log
```

## Email Configuration

### Using Gmail

1. Enable 2FA on Gmail account
2. Create App Password
3. Set in `.env`:
```env
SMTP_USER=your-email@gmail.com
SMTP_PASSWORD=your-app-password
```

### Using Other Providers

Update `config/email.js` with your SMTP settings.

## Security

### API Keys

Generate secure API keys for third-party integrations:

```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

### Password Hashing

Passwords are hashed using bcrypt with 10 salt rounds.

### CORS Configuration

Configure allowed origins in `.env`:
```env
CORS_ORIGIN=http://localhost:5173,https://yourdomain.com
```

## Troubleshooting

### Database Connection Error

```
Check:
- PostgreSQL is running
- Database credentials in .env are correct
- Database exists
- User has proper permissions
```

### Port Already in Use

```bash
# Kill process on port 3000
lsof -ti:3000 | xargs kill -9
```

### Migration Failed

```bash
# Rollback last migration
npm run migrate:rollback

# Retry
npm run migrate
```

### Module Not Found

```bash
rm -rf node_modules package-lock.json
npm install
```

## Development Workflow

1. Create feature branch
2. Make changes
3. Run tests: `npm test`
4. Create migration if needed: `npm run migration:create`
5. Commit with descriptive message
6. Push and create pull request

## Performance Optimization

### Database Indexing

Common indexes are created during migrations. Check `sql/migrations/` for index definitions.

### Query Optimization

Use `.explain()` in migrations to identify slow queries:

```sql
EXPLAIN ANALYZE SELECT * FROM cultivars WHERE name LIKE '%test%';
```

### Caching

Enable Redis caching (optional):

```env
REDIS_URL=redis://localhost:6379
CACHE_TTL=3600
```

## Next Steps

- Review [API Reference](02-api-reference.md)
- Explore [Architecture Guide](03-architecture.md)
- Configure for [Deployment](../../dashboard/03-deployment.md)
- Check sample routes and middleware
