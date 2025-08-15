# Havoc C2 User Management System

## Overview

The Havoc2.0 C2 User Management System provides comprehensive role-based access control (RBAC), command restrictions, audit logging, and security monitoring capabilities. This system allows administrators to manage operator permissions, restrict command execution, and maintain detailed logs of all user activities and security violations.

## Features

### 1. Role-Based Access Control (RBAC)

The system implements four distinct user roles with hierarchical permissions:

- **Admin**: Full system access, can manage all aspects of the framework
- **Operation Lead**: Can manage operations, blocklists, and team members
- **Operator**: Can execute commands and interact with agents (with restrictions)
- **Spectator**: Read-only access for monitoring and observation

### 2. Operator Management

#### Create New Operators
- Username and password authentication
- Role assignment during creation
- Optional comments for operator identification
- Password strength requirements (minimum 8 characters)

#### Operator Table Display
- Username, Role, and Comment columns
- Real-time status updates
- Assigned operations visibility
- Restricted commands display
- BOF blacklists assignment tracking

### 3. Operations Management

Operations provide a way to group operators and apply collective restrictions:

- **Create Operations**: Define operational boundaries
- **Assign Operators**: Associate operators with specific operations
- **Operation-Level Restrictions**: Apply blocklists to entire operations
- **Multi-Operation Support**: Operators can belong to multiple operations

### 4. BOF (Beacon Object File) Blocklists

The blocklist system allows granular control over command execution:

#### Blocklist Types
- **Operator-Specific**: Restrictions applied to individual operators
- **Operation-Level**: Restrictions applied to all operators in an operation
- **Cumulative Effect**: Both restriction types are combined for comprehensive control

#### Blocklist Management
- Create new blocklists with descriptive names
- Add/remove commands from existing blocklists
- Assign blocklists to operators or operations
- View blocklist details and affected users

### 5. Command Restriction System

The command restriction system prevents unauthorized command execution:

- **Real-time Validation**: Commands are checked before execution
- **Comprehensive Blocking**: Supports BOF commands and regular commands
- **Clear Error Messages**: Users receive specific feedback when commands are blocked
- **Restriction Sources**: Shows whether restriction comes from operator or operation level

### 6. Comprehensive Audit Logging

All user management activities are logged to `/havoc/data/user_management.log`:

#### Logged Events
- Operator creation/removal
- Role changes
- Operation assignments
- Blocklist creation/modification
- Password changes
- Command execution attempts
- Permission changes

#### Log Format
```
[2024-12-29 15:30:45 UTC] EVENT=OPERATOR_CREATED USER=admin ACTION=CREATE_OPERATOR MSG="Created new operator 'analyst' with role 'operator'" DETAILS={new_user="analyst", role="operator"}
```

### 7. Security Offense Logging

Security violations are logged separately to both client and server:

#### Client-Side Logging
- Location: `/data/log/user_management_offenses.log`
- Real-time violation tracking
- Local security monitoring

#### Server-Side Logging
- Location: `/data/log/user_management_offenses.log`
- Centralized violation repository
- Comprehensive security oversight

#### Offense Types Tracked
- Unauthorized operator creation attempts
- Unauthorized role change attempts
- Blocked command execution
- Privilege escalation attempts
- Unauthorized blocklist modifications

### 8. Permission Checking System

The system implements proactive permission checking:

- **Client-Side Validation**: Immediate feedback before server communication
- **Server-Side Enforcement**: Secondary validation for security
- **Security Messages**: Clear "You are not authorized" messages
- **No Silent Failures**: All denials are explicitly communicated

## User Interface

### Operators Tab
- Table view of all operators with comprehensive information
- Quick actions: Add, Remove, Edit, Refresh
- Search and filter capabilities
- Real-time updates

### Create New Operator Tab
- User-friendly form interface
- Password confirmation
- Role selection dropdown
- Permission preview based on selected role

### Operations Tab
- Operations management interface
- Operator assignment tools
- Operation-level blocklist assignment
- Visual operation hierarchy

### BOF Blocklists Tab
- Blocklist creation and management
- Command list editor with add/remove functionality
- Assignment interface for operators and operations
- Blocklist statistics and usage tracking

## Security Features

### Multi-Layer Security
1. **Authentication**: Username/password based access
2. **Authorization**: Role-based permission system
3. **Audit Trail**: Comprehensive activity logging
4. **Offense Tracking**: Security violation monitoring
5. **Real-time Enforcement**: Immediate command blocking

### Security Best Practices
- Passwords are transmitted securely to the teamserver
- All actions require appropriate permissions
- Failed attempts are logged and monitored
- No privilege escalation vulnerabilities
- Clear separation of duties between roles

## Technical Implementation

### Architecture
- **Client**: Qt5-based UI with permission checking
- **Server**: Go-based teamserver with enforcement
- **Database**: SQLite for persistent storage
- **Communication**: Encrypted packager protocol

### Key Components
- `UserManagement.cc/hpp`: Main UI implementation
- `PermissionChecker.cc/hpp`: Client-side permission validation
- `AuditLogger.cc/hpp`: Audit trail implementation
- `OffenseLogger.cc/hpp`: Security violation tracking
- `operators.go`: Server-side enforcement logic
- `db/operators.go`: Database operations

## Usage Examples

### Creating an Operator
1. Navigate to "Create New Operator" tab
2. Enter username and password
3. Select appropriate role
4. Add optional comment
5. Click "Create Operator"

### Assigning Blocklists
1. Go to "BOF Blocklists" tab
2. Select or create a blocklist
3. Click "Assign Blocklist"
4. Choose target operator and operation
5. Confirm assignment

### Monitoring Security
1. Check audit logs: `/havoc/data/user_management.log`
2. Review offense logs: `/data/log/user_management_offenses.log`
3. Monitor operator activities in real-time
4. Investigate security violations promptly

## Best Practices

1. **Principle of Least Privilege**: Assign minimum necessary permissions
2. **Regular Audits**: Review logs and permissions periodically
3. **Strong Passwords**: Enforce complex password requirements
4. **Operation Segregation**: Use operations to separate different teams
5. **Blocklist Management**: Regularly update command restrictions
6. **Monitoring**: Actively monitor offense logs for security violations

## Troubleshooting

### Common Issues

**Issue**: Operator cannot execute commands
- Check operator's role and permissions
- Verify operation assignments
- Review applied blocklists
- Check audit logs for denial reasons

**Issue**: Cannot create new operators
- Verify you have admin role
- Check offense logs for permission denials
- Ensure all required fields are filled

**Issue**: Blocklists not applying
- Confirm blocklist assignment
- Check operation membership
- Verify command names match exactly
- Review teamserver logs

## Summary

The Havoc2.0 C2 User Management System provides enterprise-grade access control and security monitoring for red team operations. With its comprehensive RBAC implementation, detailed audit trails, and proactive security measures, teams can maintain operational security while enabling effective collaboration.

The system ensures that all activities are tracked, unauthorized actions are prevented, and security violations are immediately identified and logged. This creates a secure, auditable environment for conducting authorized security assessments.