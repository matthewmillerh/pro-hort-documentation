---
layout: default
title: Admin Usage Guide
parent: Admin
---

# Admin Panel Usage Guide

## Introduction

The Admin Panel provides comprehensive management capabilities for Pro-Hort operations. This guide covers administrative tasks and system management.

## Dashboard Overview

The admin dashboard displays:
- System status and health metrics
- Recent activities
- Quick action buttons
- User activity logs

## User Management

### Managing Admin Users

1. Navigate to **Users** → **Admin Users**
2. View list of administrators
3. Click on user to view details
4. Modify permissions and roles as needed

### User Roles and Permissions

**Role Types:**
- **Super Admin** - Full system access
- **Admin** - Most administrative functions
- **Manager** - Limited administrative access
- **Viewer** - Read-only access

### Creating New Admin User

1. Click **Add New User**
2. Enter email address
3. Assign role
4. Set permissions
5. Click **Create**
6. User receives invitation email

### Deactivating Users

1. Select user from list
2. Click **Deactivate**
3. Confirm action
4. User account is suspended

## System Settings

### Application Configuration

Navigate to **Settings** → **General**:
- Application name
- System URL
- Timezone
- Date format
- Language preferences

### Email Settings

Configure email for notifications:
1. Go to **Settings** → **Email**
2. Enter SMTP server details
3. Configure email templates
4. Test email delivery

### Database Maintenance

Scheduled tasks for database optimization:

1. Navigate to **Settings** → **Maintenance**
2. View backup schedule
3. Run manual backups
4. Clean up old logs

## Breeders Management

### Add New Breeder

1. Go to **Breeders**
2. Click **Add New**
3. Fill in breeder information:
   - Name (required)
   - Location
   - Contact information
   - Organization details
4. Click **Save**

### Edit Breeder Information

1. Click on breeder name
2. Update information
3. Click **Save Changes**

### Delete Breeder

1. Select breeder from list
2. Click **Delete**
3. Confirm deletion

## Cultivar Management

### Add Cultivar

1. Navigate to **Cultivars**
2. Click **Add New**
3. Enter:
   - Cultivar name
   - Parentage
   - Breeder
   - Release year
   - Description
4. Set status
5. Click **Save**

### Bulk Import Cultivars

1. Go to **Cultivars** → **Import**
2. Download template
3. Fill in cultivar data
4. Upload CSV file
5. Verify data
6. Click **Import**

## Reports and Analytics

### Viewing Reports

1. Navigate to **Reports**
2. Select report type:
   - Cultivar Performance
   - Breeder Summary
   - Tasting Events
   - Breeding Program Progress
3. Choose date range
4. Click **Generate**

### Exporting Data

**Export Formats:**
- CSV - Spreadsheet format
- Excel - Microsoft Excel format
- PDF - Formatted document
- JSON - Data interchange format

**Export Steps:**
1. Select items or entire table
2. Click **Export**
3. Choose format
4. Configure options
5. Click **Download**

### Creating Custom Reports

1. Go to **Reports** → **Custom Report**
2. Select data sources
3. Choose visualization type
4. Configure filters
5. Save report template

## IP Manager

### Managing IP Addresses

1. Navigate to **IP Manager**
2. View assigned IP addresses
3. Add new IP:
   - Enter IP address
   - Assign to user/location
   - Set permissions
   - Click **Add**

### IP Restrictions

Configure IP whitelisting for security:
1. Go to **Settings** → **IP Restrictions**
2. Add allowed IP addresses
3. Set restriction level
4. Enable/disable as needed

## Audit Logs

### Viewing Activity Logs

1. Go to **Audit** → **Activity Logs**
2. View all system activities
3. Filter by:
   - User
   - Action type
   - Date range
   - Resource type
4. Click entry for details

### Exporting Logs

1. Select log entries
2. Click **Export**
3. Choose format
4. Download for record-keeping

## Tasting Events

### Create Tasting Event

1. Navigate to **Tasting Events**
2. Click **New Event**
3. Enter:
   - Event name
   - Date and time
   - Location
   - Participants
   - Cultivars to be tasted
4. Click **Save**

### Manage Participants

1. Open tasting event
2. Go to **Participants** tab
3. Add participants by email
4. Set roles (Judge, Organizer, etc.)
5. Send invitations

### Tasting Results

1. Open completed tasting event
2. Go to **Results** tab
3. View scores and comments
4. Generate report

## Backup and Recovery

### Manual Backup

1. Go to **Settings** → **Maintenance**
2. Click **Create Backup**
3. Choose backup type:
   - Full backup
   - Database only
   - Files only
4. Wait for completion
5. Download if needed

### Restore from Backup

1. Go to **Settings** → **Maintenance**
2. Click **Restore**
3. Select backup file
4. Confirm restoration
5. System restarts

## Security

### Password Policy

Configure password requirements:
1. **Settings** → **Security**
2. Set minimum length
3. Require special characters
4. Set expiration period
5. Save

### Two-Factor Authentication

Enable 2FA for admin users:
1. Go to **Settings** → **2FA**
2. Enable globally or per-user
3. Choose method (TOTP, SMS)
4. Users must complete setup on next login

### API Keys

Manage API keys for third-party integrations:
1. Go to **Settings** → **API Keys**
2. Generate new key
3. Set permissions and expiration
4. Save and share securely

## Troubleshooting

### Access Denied

- Verify user role has required permissions
- Check IP restrictions
- Clear browser cookies

### Database Errors

- Check database connection
- Review error logs
- Run database maintenance

### Email Not Sending

- Verify SMTP configuration
- Check sender email address
- Review email logs

## Next Steps

- Configure system settings
- Create admin accounts
- Set up automated backups
- Review [API Reference](03-api-reference.md)
