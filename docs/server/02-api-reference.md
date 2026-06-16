---
layout: default
title: Server API Reference
parent: Server
---

# Server API Reference

## Overview

The Pro-Hort Server API provides RESTful endpoints for managing all application data. All endpoints use JSON for request/response bodies.

## Base URL

```
http://localhost:3000/api
```

## Authentication

All endpoints (except `/auth/login` and `/auth/register`) require a JWT token in the Authorization header:

```
Authorization: Bearer <your_jwt_token>
```

## Response Format

All responses follow this format:

```json
{
  "success": true,
  "data": {...},
  "message": "Success message"
}
```

## Endpoints

### Authentication

#### Login

```http
POST /auth/login
```

**Request:**
```json
{
  "email": "user@example.com",
  "password": "password"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "token": "eyJhbGc...",
    "user": {
      "id": "user_123",
      "email": "user@example.com",
      "role": "user"
    }
  }
}
```

#### Register

```http
POST /auth/register
```

**Request:**
```json
{
  "email": "newuser@example.com",
  "password": "secure_password",
  "firstName": "John",
  "lastName": "Doe"
}
```

#### Verify Token

```http
GET /auth/verify
```

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "success": true,
  "data": {
    "valid": true,
    "user": {...}
  }
}
```

### Cultivars

#### List Cultivars

```http
GET /cultivars?page=1&limit=20&search=term
```

**Query Parameters:**
- `page` - Page number (default: 1)
- `limit` - Items per page (default: 20)
- `search` - Search by name
- `breeder` - Filter by breeder ID
- `status` - Filter by status (active/inactive)
- `sort` - Sort field (default: name)
- `order` - Sort order (asc/desc)

**Response:**
```json
{
  "success": true,
  "data": {
    "cultivars": [
      {
        "id": "cultivar_123",
        "name": "Cultivar Name",
        "parentage": "Parent1 x Parent2",
        "breederId": "breeder_123",
        "releaseYear": 2024,
        "status": "active",
        "createdAt": "2024-01-01T00:00:00Z"
      }
    ],
    "total": 150,
    "page": 1,
    "pages": 8,
    "limit": 20
  }
}
```

#### Get Single Cultivar

```http
GET /cultivars/:id
```

#### Create Cultivar

```http
POST /cultivars
```

**Request:**
```json
{
  "name": "New Cultivar",
  "parentage": "Parent1 x Parent2",
  "breederId": "breeder_123",
  "releaseYear": 2024,
  "description": "Description",
  "status": "active"
}
```

#### Update Cultivar

```http
PUT /cultivars/:id
```

**Request:** (partial update)
```json
{
  "name": "Updated Name",
  "status": "inactive"
}
```

#### Delete Cultivar

```http
DELETE /cultivars/:id
```

#### Bulk Import

```http
POST /cultivars/import
```

**Content-Type:** `multipart/form-data`

**Body:**
- `file` - CSV file with cultivar data

### Breeders

#### List Breeders

```http
GET /breeders?page=1&limit=20
```

**Response:**
```json
{
  "success": true,
  "data": {
    "breeders": [
      {
        "id": "breeder_123",
        "name": "John Smith",
        "location": "California, USA",
        "email": "john@example.com",
        "phone": "+1-555-0123",
        "organization": "Pro-Hort Farm",
        "cultivarCount": 15,
        "createdAt": "2024-01-01T00:00:00Z"
      }
    ],
    "total": 50
  }
}
```

#### Get Breeder

```http
GET /breeders/:id
```

#### Create Breeder

```http
POST /breeders
```

**Request:**
```json
{
  "name": "Jane Doe",
  "location": "Oregon, USA",
  "email": "jane@example.com",
  "phone": "+1-555-9876",
  "organization": "Green Valley Farms"
}
```

#### Update Breeder

```http
PUT /breeders/:id
```

#### Delete Breeder

```http
DELETE /breeders/:id
```

### Tasting Events

#### List Events

```http
GET /tasting-events?page=1&limit=20&status=upcoming
```

**Query Parameters:**
- `status` - Filter by status (upcoming/completed/cancelled)
- `startDate` - Filter by date range start
- `endDate` - Filter by date range end
- `location` - Filter by location

#### Get Event

```http
GET /tasting-events/:id
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "event_123",
    "name": "Spring Tasting 2024",
    "date": "2024-03-15",
    "location": "Main Hall",
    "participants": 25,
    "cultivars": ["cultivar_1", "cultivar_2"],
    "status": "upcoming",
    "createdAt": "2024-01-01T00:00:00Z"
  }
}
```

#### Create Event

```http
POST /tasting-events
```

**Request:**
```json
{
  "name": "Summer Tasting 2024",
  "date": "2024-06-20",
  "location": "Outdoor Pavilion",
  "cultivarIds": ["cultivar_1", "cultivar_2", "cultivar_3"],
  "participants": ["user_1@example.com", "user_2@example.com"],
  "notes": "Event notes"
}
```

#### Update Event

```http
PUT /tasting-events/:id
```

#### Delete Event

```http
DELETE /tasting-events/:id
```

#### Add Tasting Results

```http
POST /tasting-events/:id/results
```

**Request:**
```json
{
  "participantId": "user_123",
  "cultivarId": "cultivar_123",
  "score": 8.5,
  "comments": "Excellent flavor profile",
  "flavor": "fruity",
  "texture": "smooth"
}
```

### Reports

#### Get Available Reports

```http
GET /reports/available
```

**Response:**
```json
{
  "success": true,
  "data": {
    "reportTypes": [
      {
        "id": "cultivar_performance",
        "name": "Cultivar Performance Report",
        "description": "Performance metrics for cultivars"
      }
    ]
  }
}
```

#### Generate Report

```http
POST /reports/generate
```

**Request:**
```json
{
  "type": "cultivar_performance",
  "startDate": "2024-01-01",
  "endDate": "2024-06-16",
  "filters": {
    "breederId": "breeder_123"
  }
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "reportId": "report_123",
    "type": "cultivar_performance",
    "generatedAt": "2024-06-16T10:30:00Z",
    "downloadUrl": "/api/reports/report_123/download"
  }
}
```

#### List Reports

```http
GET /reports?page=1&limit=20
```

#### Download Report

```http
GET /reports/:id/download?format=pdf
```

**Query Parameters:**
- `format` - pdf, csv, excel, json

### Users

#### Get Current User

```http
GET /users/me
```

#### Update Profile

```http
PUT /users/me
```

**Request:**
```json
{
  "firstName": "John",
  "lastName": "Doe",
  "phone": "+1-555-0123",
  "preferences": {
    "theme": "dark",
    "notifications": true
  }
}
```

#### Change Password

```http
POST /users/change-password
```

**Request:**
```json
{
  "currentPassword": "old_password",
  "newPassword": "new_password"
}
```

#### Get Users (Admin Only)

```http
GET /users?page=1&limit=20&role=user
```

### Health & Status

#### Server Status

```http
GET /health
```

**Response:**
```json
{
  "success": true,
  "data": {
    "status": "ok",
    "database": "connected",
    "uptime": 3600,
    "timestamp": "2024-06-16T10:30:00Z"
  }
}
```

## Error Responses

### Error Format

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed",
    "details": {
      "field": ["error message"]
    }
  }
}
```

### HTTP Status Codes

- `200` - OK
- `201` - Created
- `400` - Bad Request (validation error)
- `401` - Unauthorized (missing/invalid token)
- `403` - Forbidden (insufficient permissions)
- `404` - Not Found
- `409` - Conflict (duplicate entry)
- `422` - Unprocessable Entity
- `500` - Internal Server Error

## Rate Limiting

API endpoints are rate limited:
- 100 requests per minute per user
- 1000 requests per hour per user

**Headers:**
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1718616600
```

## Pagination

All list endpoints support pagination:

```
GET /cultivars?page=2&limit=50
```

**Response includes:**
- `total` - Total number of items
- `page` - Current page
- `pages` - Total number of pages
- `limit` - Items per page

## Filtering

Use query parameters for filtering:

```
GET /cultivars?status=active&breeder=breeder_123&search=term
```

## Sorting

Sort with `sort` and `order` parameters:

```
GET /cultivars?sort=name&order=asc
```

## Examples

### Create and List Cultivars

```bash
# Create cultivar
curl -X POST http://localhost:3000/api/cultivars \
  -H "Authorization: Bearer token" \
  -H "Content-Type: application/json" \
  -d '{"name":"Red Apple","breederId":"breeder_1"}'

# List cultivars
curl -X GET http://localhost:3000/api/cultivars?limit=10 \
  -H "Authorization: Bearer token"
```

### Generate Report

```bash
curl -X POST http://localhost:3000/api/reports/generate \
  -H "Authorization: Bearer token" \
  -H "Content-Type: application/json" \
  -d '{
    "type":"cultivar_performance",
    "startDate":"2024-01-01",
    "endDate":"2024-06-16"
  }'
```

## Next Steps

- Review [Setup Guide](01-setup.md)
- Explore [Architecture Guide](03-architecture.md)
- Implement client integration
