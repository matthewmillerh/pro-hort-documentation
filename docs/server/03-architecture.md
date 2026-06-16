---
layout: default
title: Server Architecture Guide
parent: Server
---

# Server Architecture Guide

## System Overview

The Pro-Hort Server follows a layered architecture pattern with clear separation of concerns:

```
┌─────────────────────────────────────────┐
│         Client Applications             │
│    (Dashboard, Admin Panel, Mobile)     │
└──────────────┬──────────────────────────┘
               │ HTTP/REST
┌──────────────▼──────────────────────────┐
│         Express API Layer               │
│  (Routes, Controllers, Middlewares)     │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│      Business Logic Layer               │
│    (Services, Composables, Utils)       │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│      Data Access Layer                  │
│      (Models, Repositories)             │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│      Database Layer                     │
│      (PostgreSQL, Redis)                │
└─────────────────────────────────────────┘
```

## Directory Structure

```
src/
├── config/                 # Configuration files
│   ├── agentDb.js         # Agent database settings
│   ├── db.js              # Primary database settings
│   ├── email.js           # Email service configuration
│   └── index.js           # Centralized config
├── controllers/           # Request handlers
│   ├── authController.js
│   ├── cultivarController.js
│   ├── breederController.js
│   ├── reportController.js
│   └── ...
├── middlewares/           # Express middlewares
│   ├── authMiddleware.js  # JWT authentication
│   ├── errorHandler.js    # Error handling
│   ├── logger.js          # Request logging
│   └── ...
├── models/               # Data models
│   ├── Cultivar.js
│   ├── Breeder.js
│   ├── User.js
│   └── ...
├── routes/               # API routes
│   ├── auth.js
│   ├── cultivars.js
│   ├── breeders.js
│   └── index.js
├── services/             # Business logic
│   ├── cultivarService.js
│   ├── breederService.js
│   ├── reportService.js
│   └── ...
├── utils/                # Utilities
│   ├── validators.js
│   ├── helpers.js
│   ├── logger.js
│   └── ...
├── tests/                # Test files
│   ├── unit/
│   ├── integration/
│   └── e2e/
└── index.js              # Application entry point
```

## Request Flow

### 1. Client Request

Client sends HTTP request to API endpoint:

```
POST /api/cultivars
Authorization: Bearer token
Content-Type: application/json
{
  "name": "New Cultivar"
}
```

### 2. Middleware Pipeline

Request passes through middleware chain:

```
Request
  ↓
CORS Middleware
  ↓
Logger Middleware
  ↓
Auth Middleware (verify token)
  ↓
Validation Middleware
  ↓
Route Handler
```

### 3. Controller

Controller receives request and delegates to service:

```javascript
// cultivarController.js
async createCultivar(req, res) {
  try {
    const cultivar = await cultivarService.create(req.body)
    res.json({ success: true, data: cultivar })
  } catch (error) {
    next(error)
  }
}
```

### 4. Service Layer

Service implements business logic:

```javascript
// cultivarService.js
async create(data) {
  // Validation
  // Business logic
  // Call repository
  // Return result
}
```

### 5. Data Access Layer

Repository interacts with database:

```javascript
// cultivarRepository.js
async create(data) {
  return await db.query(
    'INSERT INTO cultivars (...) VALUES (...)',
    [data.name, data.parentage, ...]
  )
}
```

### 6. Response

Response sent back to client:

```json
{
  "success": true,
  "data": { "id": "...", "name": "New Cultivar" }
}
```

## Core Components

### Authentication Flow

```
1. User submits email/password to /auth/login
2. Controller validates credentials
3. JWT token generated if valid
4. Token returned to client
5. Client includes token in Authorization header for subsequent requests
6. Auth middleware verifies token on each request
```

**JWT Structure:**
```
Header: { alg: "HS256", type: "JWT" }
Payload: { userId, email, role, iat, exp }
Signature: HMAC-SHA256(secret)
```

### Database Schema

**Key Relationships:**

```
Users (1) ─────── (N) Cultivars
          ├─────────── (N) TastingEvents
          └─────────── (N) Reports

Breeders (1) ─── (N) Cultivars

TastingEvents (1) ── (N) TastingResults
                └─── (N) CultivarSelections

CultivarSelections (N) ─── (1) Cultivars
```

### Services Architecture

**CultivarService Example:**

```javascript
class CultivarService {
  // CRUD operations
  async create(data) { }
  async read(id) { }
  async update(id, data) { }
  async delete(id) { }
  
  // Business logic
  async getByBreeder(breederId) { }
  async search(query) { }
  async bulkImport(file) { }
  async generateReport(options) { }
  
  // Validation
  async validateCultivarData(data) { }
}
```

## Middleware Architecture

### Built-in Middlewares

1. **Authentication Middleware**
   - Verifies JWT token
   - Extracts user info
   - Passes to next middleware

2. **Authorization Middleware**
   - Checks user permissions
   - Validates role-based access
   - Denies if insufficient permissions

3. **Validation Middleware**
   - Validates request data
   - Checks required fields
   - Returns validation errors

4. **Error Handler Middleware**
   - Catches all errors
   - Formats error responses
   - Logs errors

5. **Logger Middleware**
   - Logs all requests
   - Captures response times
   - Records errors

## Caching Strategy

### Redis Caching

```javascript
// Caching layer
async getCultivar(id) {
  // Check cache first
  let cultivar = await cache.get(`cultivar:${id}`)
  
  if (!cultivar) {
    // Cache miss - fetch from DB
    cultivar = await db.getCultivar(id)
    // Store in cache with TTL
    await cache.set(`cultivar:${id}`, cultivar, 3600)
  }
  
  return cultivar
}

// Invalidate cache on update
async updateCultivar(id, data) {
  const result = await db.updateCultivar(id, data)
  // Clear cache
  await cache.delete(`cultivar:${id}`)
  return result
}
```

### Cache Keys

- `cultivar:${id}` - Individual cultivar (1 hour TTL)
- `cultivars:list:${page}` - Cultivar list (15 min TTL)
- `breeder:${id}` - Breeder info (2 hours TTL)
- `reports:${id}` - Report data (24 hours TTL)

## Error Handling

### Error Classes

```javascript
class ApiError extends Error {
  constructor(message, code, status) {
    super(message)
    this.code = code
    this.status = status
  }
}

class ValidationError extends ApiError {
  constructor(message, details) {
    super(message, 'VALIDATION_ERROR', 400)
    this.details = details
  }
}

class AuthError extends ApiError {
  constructor(message) {
    super(message, 'UNAUTHORIZED', 401)
  }
}
```

### Error Handler

```javascript
app.use((error, req, res, next) => {
  const status = error.status || 500
  const code = error.code || 'INTERNAL_ERROR'
  
  logger.error({
    message: error.message,
    status,
    code,
    url: req.url,
    method: req.method
  })
  
  res.status(status).json({
    success: false,
    error: {
      code,
      message: error.message,
      details: error.details
    }
  })
})
```

## Database Optimization

### Indexing

Key indexes created during migrations:

```sql
-- User lookups
CREATE INDEX idx_users_email ON users(email)

-- Cultivar searches
CREATE INDEX idx_cultivars_name ON cultivars(name)
CREATE INDEX idx_cultivars_breeder_id ON cultivars(breeder_id)

-- Event filtering
CREATE INDEX idx_tasting_events_date ON tasting_events(date)
```

### Query Optimization

- Use SELECT specific columns, not *
- Join only needed tables
- Use WHERE clauses to filter early
- Paginate large result sets

## Testing Strategy

### Unit Tests

Test individual functions/services in isolation:

```javascript
describe('CultivarService', () => {
  it('should create cultivar', async () => {
    const result = await cultivarService.create({...})
    expect(result).toHaveProperty('id')
  })
})
```

### Integration Tests

Test service interactions:

```javascript
describe('Cultivar API', () => {
  it('should create and retrieve cultivar', async () => {
    const created = await request.post('/api/cultivars')
    const retrieved = await request.get(`/api/cultivars/${created.id}`)
    expect(retrieved).toEqual(created)
  })
})
```

### E2E Tests

Test complete workflows:

```javascript
describe('Tasting Event Workflow', () => {
  it('should create event, add results, generate report', async () => {
    // Create event
    // Add participants
    // Add results
    // Generate report
  })
})
```

## Deployment Architecture

```
┌─────────────────┐
│  Load Balancer  │
│  (Nginx/HAProxy)│
└────────┬────────┘
         │
    ┌────┴────┐
    │    │    │
┌───▼──┐│   ││
│Srv#1 ││   ││  Multiple instances
│      ││   ││  for scalability
└──────┘│   │
    ┌───▼──┐
    │Srv#2 │
    │      │
    └──────┘
         │
    ┌────▼────┐
    │PostgreSQL│
    │(Primary) │
    └────────┘
         │
    ┌────▼────┐
    │PostgreSQL│
    │(Replica) │
    └────────┘
         │
    ┌────▼────┐
    │   Redis  │
    │ (Cache)  │
    └────────┘
```

## Performance Considerations

### API Response Times

Target response times:
- Simple queries: < 100ms
- Complex queries: < 500ms
- Report generation: < 2000ms

### Database Performance

- Monitor slow queries
- Use EXPLAIN ANALYZE for optimization
- Archive old data regularly
- Maintain statistics

### Scalability

- Horizontal scaling with load balancer
- Database replication for read scaling
- Caching layer for frequently accessed data
- Background jobs for heavy operations

## Security Architecture

### Authentication

- JWT tokens with 7-day expiration
- Refresh token mechanism
- Token blacklist for logout

### Authorization

- Role-based access control (RBAC)
- Resource-level permissions
- Audit logging of all changes

### Data Protection

- Password hashing with bcrypt
- CORS configuration
- Input validation and sanitization
- SQL injection prevention with parameterized queries

## Monitoring and Logging

### Application Logging

```javascript
logger.info('User logged in', { userId, timestamp })
logger.error('Database error', { error, query })
```

### Performance Metrics

- Request count
- Response times
- Error rates
- Database query times

### Health Checks

```javascript
GET /health
// Returns: { status: 'ok', database: 'connected', uptime: ... }
```

## Next Steps

- Review [API Reference](02-api-reference.md)
- Explore [Setup Guide](01-setup.md)
- Implement monitoring
- Set up automated deployment
