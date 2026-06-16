---
layout: default
title: Admin API Reference
parent: Admin
---

# Admin API Reference

## Overview

The Admin Panel API provides endpoints for administrative operations. All requests require authentication with an admin JWT token.

## Authentication

All requests must include the Authorization header:

```bash
Authorization: Bearer <jwt-token>
```

## Base URL

```
http://localhost:3000/api/admin
```

## Response Format

All responses follow this format:

```json
{
  "success": true,
  "data": {...},
  "message": "Success message",
  "timestamp": "2024-06-16T10:30:00Z"
}
```

## Endpoints

### Users

#### List Admin Users

```http
GET /users
```

**Query Parameters:**
- `page` (optional) - Page number, default 1
- `limit` (optional) - Items per page, default 20
- `role` (optional) - Filter by role
- `search` (optional) - Search by email or name

**Response:**
```json
{
  "success": true,
  "data": {
    "users": [
      {
        "id": "user_123",
        "email": "admin@pro-hort.com",
        "role": "super_admin",
        "createdAt": "2024-01-01T00:00:00Z",
        "lastLogin": "2024-06-16T10:00:00Z"
      }
    ],
    "total": 10,
    "page": 1,
    "pages": 1
  }
}
```

#### Create User

```http
POST /users
```

**Request Body:**
```json
{
  "email": "newadmin@pro-hort.com",
  "role": "admin",
  "permissions": ["read", "write", "delete"]
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "user_124",
    "email": "newadmin@pro-hort.com",
    "role": "admin",
    "createdAt": "2024-06-16T10:30:00Z"
  }
}
```

#### Update User

```http
PUT /users/:userId
```

**Request Body:**
```json
{
  "role": "manager",
  "permissions": ["read", "write"]
}
```

#### Delete User

```http
DELETE /users/:userId
```

### Breeders

#### List Breeders

```http
GET /breeders
```

**Query Parameters:**
- `page` - Page number
- `limit` - Items per page
- `search` - Search by name
- `status` - Filter by status (active/inactive)

#### Create Breeder

```http
POST /breeders
```

**Request Body:**
```json
{
  "name": "John Smith",
  "location": "California, USA",
  "email": "john@prohort.com",
  "phone": "+1-555-0123",
  "organization": "Pro-Hort Farm"
}
```

#### Update Breeder

```http
PUT /breeders/:breederId
```

**Request Body:**
```json
{
  "name": "John Smith Jr.",
  "email": "john.jr@prohort.com"
}
```

#### Delete Breeder

```http
DELETE /breeders/:breederId
```

### Cultivars

#### List Cultivars

```http
GET /cultivars
```

**Query Parameters:**
- `page` - Page number
- `limit` - Items per page
- `breeder` - Filter by breeder ID
- `search` - Search by name

#### Create Cultivar

```http
POST /cultivars
```

**Request Body:**
```json
{
  "name": "Cultivar Name",
  "parentage": "Parent1 x Parent2",
  "breederId": "breeder_123",
  "releaseYear": 2024,
  "description": "Cultivar description",
  "status": "active"
}
```

#### Bulk Import Cultivars

```http
POST /cultivars/import
```

**Request Body:** (multipart/form-data)
```
file: CSV file with cultivar data
```

CSV Format:
```
name,parentage,breeder_id,release_year,description
Cultivar1,Parent1 x Parent2,breeder_123,2024,Description
```

### Reports

#### Generate Report

```http
POST /reports/generate
```

**Request Body:**
```json
{
  "type": "cultivar_performance",
  "startDate": "2024-01-01",
  "endDate": "2024-06-16",
  "format": "pdf"
}
```

**Supported Types:**
- `cultivar_performance` - Cultivar performance data
- `breeder_summary` - Breeder activity summary
- `tasting_events` - Tasting event results
- `breeding_progress` - Breeding program progress

**Supported Formats:**
- `pdf` - PDF document
- `csv` - CSV spreadsheet
- `excel` - Excel workbook
- `json` - JSON data

#### List Reports

```http
GET /reports
```

#### Export Report

```http
GET /reports/:reportId/export
```

**Query Parameters:**
- `format` - Export format (pdf, csv, excel)

### Audit Logs

#### List Activity Logs

```http
GET /audit/logs
```

**Query Parameters:**
- `page` - Page number
- `limit` - Items per page
- `user` - Filter by user ID
- `action` - Filter by action type
- `resource` - Filter by resource type
- `startDate` - Start date filter
- `endDate` - End date filter

**Response:**
```json
{
  "success": true,
  "data": {
    "logs": [
      {
        "id": "log_123",
        "userId": "user_123",
        "action": "create",
        "resource": "cultivar",
        "resourceId": "cultivar_456",
        "timestamp": "2024-06-16T10:30:00Z",
        "details": {}
      }
    ],
    "total": 100,
    "page": 1
  }
}
```

### IP Manager

#### List IPs

```http
GET /ip-manager
```

#### Add IP Address

```http
POST /ip-manager
```

**Request Body:**
```json
{
  "ipAddress": "192.168.1.100",
  "description": "Office network",
  "userId": "user_123",
  "active": true
}
```

#### Update IP

```http
PUT /ip-manager/:ipId
```

#### Delete IP

```http
DELETE /ip-manager/:ipId
```

### Settings

#### Get Settings

```http
GET /settings
```

#### Update Settings

```http
PUT /settings
```

**Request Body:**
```json
{
  "appName": "Pro-Hort Dashboard",
  "timezone": "UTC",
  "dateFormat": "YYYY-MM-DD",
  "emailFrom": "noreply@pro-hort.com"
}
```

### Backups

#### Create Backup

```http
POST /backups/create
```

**Request Body:**
```json
{
  "type": "full",
  "description": "Manual backup"
}
```

#### List Backups

```http
GET /backups
```

#### Restore Backup

```http
POST /backups/:backupId/restore
```

## Error Handling

Error responses follow this format:

```json
{
  "success": false,
  "error": {
    "code": "INVALID_REQUEST",
    "message": "Detailed error message",
    "details": {}
  }
}
```

### Common Error Codes

- `UNAUTHORIZED` - Missing or invalid authentication
- `FORBIDDEN` - Insufficient permissions
- `NOT_FOUND` - Resource not found
- `INVALID_REQUEST` - Invalid request data
- `CONFLICT` - Resource already exists
- `INTERNAL_ERROR` - Server error

## Rate Limiting

Admin API has rate limiting:
- 100 requests per minute per user
- 10,000 requests per hour per user

Headers in response:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1718616600
```

## Examples

### Create Admin User

```bash
curl -X POST http://localhost:3000/api/admin/users \
  -H "Authorization: Bearer jwt_token" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@pro-hort.com",
    "role": "admin",
    "permissions": ["read", "write"]
  }'
```

### Generate Report

```bash
curl -X POST http://localhost:3000/api/admin/reports/generate \
  -H "Authorization: Bearer jwt_token" \
  -H "Content-Type: application/json" \
  -d '{
    "type": "cultivar_performance",
    "startDate": "2024-01-01",
    "endDate": "2024-06-16",
    "format": "pdf"
  }'
```

## WebSocket Events

Real-time events via WebSocket:

```javascript
const ws = new WebSocket('ws://localhost:3000/api/admin/events');

ws.addEventListener('message', (event) => {
  const data = JSON.parse(event.data);
  console.log(data);
});
```

**Event Types:**
- `user_created` - New user created
- `user_updated` - User updated
- `cultivar_imported` - Cultivars imported
- `report_generated` - Report generated
- `backup_completed` - Backup completed

## Next Steps

- Implement API integration in your application
- Review [Admin Usage Guide](02-usage.md)
- Check [Setup Guide](01-setup.md) for configuration
