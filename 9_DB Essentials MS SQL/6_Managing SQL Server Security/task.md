# Managing SQL Server Security - Task

## Goals

- Learn to configure SQL Server security for different user roles
- Understand Windows Authentication vs SQL Authentication
- Create and manage logins, users, and roles
- Implement granular permissions for specific database objects
- Configure contained database users for portability
- Migrate databases with contained users between instances

## Prerequisites

- 1 VM with SQL Server 2019 (Instance1)
- Second SQL Server instance (Instance2) - can be named instance
- AdventureWorks database restored (plays role of "User DB")
- Administrative access to Windows for creating local accounts

## Tasks

### Part 1: Security Configuration for Project Team

Create security configuration according to this schema:

| User Type | Permissions Required |
|-----------|---------------------|
| 5 Developers (Human users - SQL Authentication) | Read and write database data of User DB (AdventureWorks) |
| 1 Application Service Account (Non-human, Windows Authentication) | Read/write/update data in a specific table (create your own) of User DB |
| 1 Maintenance Service Account (Non-application, Windows Authentication) | Modify User DB, create backups, but CANNOT delete DB |
| 1 Backup User (Human user - SQL Authentication) | Make backups of ALL DBs, but CANNOT read data from User DB |
| 2 QA Users (Human users - SQL Authentication) | Can ONLY read data from User DB |

**Notes:**
- AdventureWorks database = "User DB"
- Service accounts: Create Windows Local accounts, use Windows Authentication
- Human accounts: Use SQL Server Authentication

### Part 2: Verify Access

1. Log on under each created user
2. Verify they have correct permissions:
   - Can perform allowed actions
   - Cannot perform restricted actions

### Part 3: Contained Database Setup

1. Create a new database named "LabDB" on Instance1
2. Enable contained database authentication
3. Create a contained database user "LabUser" in LabDB
4. Grant appropriate permissions to LabUser

### Part 4: Database Migration with Contained User

1. Move/Copy LabDB database to Instance2 (any method)
2. Configure containment settings on Instance2 (if required)
3. Verify you can connect to LabDB on Instance2 using LabUser
4. Confirm contained user works without server-level login

## Requirements

1. All user types must be created with correct authentication method
2. Windows local accounts must be created for service accounts
3. Permissions must match the requirements table exactly
4. Each user's access must be tested and verified
5. LabDB must be a contained database
6. LabUser must be a contained database user (not linked to server login)
7. LabDB migration must preserve LabUser functionality
8. LabUser must work on Instance2 without creating server login

## Self-Review

- [ ] 5 developer logins created with SQL Authentication
- [ ] Developers can read/write AdventureWorks data
- [ ] Application service Windows account created
- [ ] Application service can only access specific table
- [ ] Maintenance service Windows account created
- [ ] Maintenance service can modify DB and backup, cannot delete
- [ ] Backup user can backup all DBs but cannot read User DB data
- [ ] 2 QA users can only read AdventureWorks data
- [ ] All permissions verified by logging in as each user
- [ ] LabDB created as contained database
- [ ] LabUser created as contained user
- [ ] LabDB migrated to Instance2
- [ ] LabUser works on Instance2 without server login

## Helpful Materials

- https://docs.microsoft.com/en-us/sql/relational-databases/security/authentication-access/create-a-login
- https://docs.microsoft.com/en-us/sql/relational-databases/security/authentication-access/create-a-database-user
- https://docs.microsoft.com/en-us/sql/relational-databases/databases/contained-databases
- https://docs.microsoft.com/en-us/sql/relational-databases/databases/contained-database-users-making-your-database-portable
- https://docs.microsoft.com/en-us/sql/relational-databases/security/permissions-database-engine
- https://docs.microsoft.com/en-us/sql/t-sql/statements/grant-transact-sql
