---
name: architecture-design
description: Design technical architecture and system components
agent: planner
---

# Architecture Design Skill

## Purpose
Design scalable, maintainable technical architecture for features and systems.

## Process

### 1. Requirement Review
- Understand functional requirements
- Identify non-functional requirements
- Note constraints and assumptions

### 2. High-Level Design
- Define system components
- Design component interactions
- Choose technology stack
- Plan data flow

### 3. Detailed Design
- Define APIs and interfaces
- Design data models
- Plan error handling
- Consider security

### 4. Design Validation
- Review against requirements
- Assess scalability
- Evaluate maintainability
- Consider trade-offs

## Input
- Requirements specification
- System constraints
- Technology preferences
- Existing architecture

## Output
```markdown
## Architecture Design

### High-Level Architecture
[Component diagram or description]

### Components
**Component A**: [Purpose]
- Responsibilities: [What it does]
- Interfaces: [How it's accessed]
- Dependencies: [What it needs]

### Data Model
[Entity relationships]

### API Design
Endpoint: [Method] [Path]
Request: [Schema]
Response: [Schema]

### Technology Stack
- Language: [Choice and why]
- Framework: [Choice and why]
- Database: [Choice and why]
- Libraries: [List with justification]

### Design Decisions
**Decision**: [What was decided]
- Options: [A, B, C]
- Chosen: [A]
- Rationale: [Why]

### Non-Functional Considerations
- Performance: [How addressed]
- Security: [How addressed]
- Scalability: [How addressed]
- Maintainability: [How addressed]
```

## Example

**Input**: User authentication requirements

**Output**:
```markdown
### High-Level Architecture
```
┌─────────────┐
│   Client    │
└──────┬──────┘
       │
       ↓
┌─────────────┐
│  Auth API   │
└──────┬──────┘
       │
       ├──→ ┌──────────────┐
       │    │ User Service │
       │    └──────────────┘
       │
       └──→ ┌──────────────┐
            │ Email Service│
            └──────────────┘
```

### Components

**Auth API**
- Responsibilities: Handle authentication requests
- Interfaces: REST API (POST /auth/register, /auth/login, /auth/logout)
- Dependencies: User Service, Email Service

**User Service**
- Responsibilities: User CRUD, password management
- Interfaces: Internal service calls
- Dependencies: User Database

**Email Service**
- Responsibilities: Send emails (verification, password reset)
- Interfaces: Internal service calls
- Dependencies: Email provider API

### Data Model
```sql
Table: users
- id (UUID, PK)
- email (VARCHAR, UNIQUE, NOT NULL)
- password_hash (VARCHAR, NOT NULL)
- created_at (TIMESTAMP)
- last_login (TIMESTAMP)

Table: sessions
- id (UUID, PK)
- user_id (UUID, FK -> users.id)
- token (VARCHAR, UNIQUE)
- expires_at (TIMESTAMP)
- created_at (TIMESTAMP)
```

### API Design

**Register**
```
POST /api/auth/register
Request: {
  "email": "user@example.com",
  "password": "SecurePass123!"
}
Response: {
  "userId": "uuid",
  "email": "user@example.com",
  "message": "Registration successful"
}
```

**Login**
```
POST /api/auth/login
Request: {
  "email": "user@example.com",
  "password": "SecurePass123!"
}
Response: {
  "token": "jwt-token",
  "expiresIn": 3600
}
```

### Technology Stack
- **Language**: Java 17 (project standard)
- **Framework**: Spring Boot 3.2 (existing stack)
- **Security**: Spring Security + JWT (industry standard)
- **Database**: PostgreSQL (existing infrastructure)
- **Password Hashing**: BCrypt (security best practice)

### Design Decisions

**Decision**: Use JWT for session management
- Options: Server-side sessions, JWT, OAuth2
- Chosen: JWT
- Rationale: Stateless, scalable, mobile-friendly

**Decision**: Separate User Service
- Options: Monolithic controller, Separate service
- Chosen: Separate service
- Rationale: Better separation of concerns, reusable

### Non-Functional Considerations
- **Performance**: JWT validation is fast (< 10ms)
- **Security**: HTTPS required, passwords hashed with BCrypt (cost 12)
- **Scalability**: Stateless design allows horizontal scaling
- **Maintainability**: Clear component boundaries, standard patterns
```

## Related Skills
- `/requirement-analysis` - Analyze requirements first
- `/task-planning` - Create implementation tasks
