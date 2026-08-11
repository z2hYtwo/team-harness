---
name: task-planning
description: Break down features into actionable implementation tasks
agent: planner
---

# Task Planning Skill

## Purpose
Decompose features into concrete, manageable tasks with clear dependencies and estimates.

## Process

### 1. Feature Analysis
- Review requirements and architecture
- Identify major work areas
- Spot technical challenges
- Note dependencies

### 2. Task Identification
- Break down into atomic tasks
- Define task scope clearly
- Estimate effort realistically
- Assign skills needed

### 3. Dependency Mapping
- Identify task dependencies
- Plan execution order
- Group parallel work
- Identify critical path

### 4. Risk Planning
- Identify technical risks
- Plan for unknowns
- Buffer complex tasks
- Prepare alternatives

## Input
- Requirements specification
- Architecture design
- Team capabilities
- Timeline constraints

## Output
```markdown
## Task Plan

### Overview
- Feature: [Name]
- Total Effort: [X hours]
- Timeline: [Y days/weeks]
- Team: [Members/Agents]

### Phase 1: [Phase Name]
**Task 1.1**: [Task name]
- Description: [What to do]
- Effort: [Hours]
- Skills: [Required expertise]
- Dependencies: [Task IDs]
- Deliverables: [Concrete outputs]

### Dependency Graph
```
Task 1.1 (Foundation)
    ↓
Task 1.2 (Core logic) ← Task 1.3 (Data model)
    ↓
Task 1.4 (API layer)
    ↓
Task 1.5 (Tests)
```

### Critical Path
Task 1.1 → Task 1.2 → Task 1.4 → Task 1.5 (Total: X hours)

### Risks
- **Risk**: [Description]
  - Impact: High/Medium/Low
  - Mitigation: [Strategy]
```

## Task Guidelines

### Good Task Characteristics
- **Atomic**: Single, well-defined objective
- **Estimable**: Can estimate effort confidently
- **Testable**: Clear success criteria
- **Independent** (when possible): Minimal dependencies
- **Small**: Completable in 4-8 hours ideally

### Task Categories
- **Foundation**: Setup, infrastructure, scaffolding
- **Core**: Business logic, algorithms
- **Integration**: Connect components
- **UI/API**: Interface layer
- **Testing**: Test writing
- **Documentation**: Docs and comments

## Example

**Input**: User authentication architecture

**Output**:
```markdown
### Overview
- Feature: User Authentication
- Total Effort: 48 hours
- Timeline: 1 week
- Team: Backend developer + Tester

### Phase 1: Foundation (12 hours)

**Task 1.1**: Setup database schema
- Description: Create users and sessions tables with indexes
- Effort: 3 hours
- Skills: Database design, SQL
- Dependencies: None
- Deliverables: Migration scripts, schema documentation

**Task 1.2**: Create User entity and repository
- Description: JPA entities and repository interfaces
- Effort: 4 hours
- Skills: Java, Spring Data JPA
- Dependencies: Task 1.1
- Deliverables: User.java, UserRepository.java, unit tests

**Task 1.3**: Setup Spring Security configuration
- Description: Configure security filters, JWT utilities
- Effort: 5 hours
- Skills: Spring Security, JWT
- Dependencies: None
- Deliverables: SecurityConfig.java, JwtUtil.java

### Phase 2: Core Implementation (20 hours)

**Task 2.1**: Implement user registration
- Description: Registration endpoint with validation and email check
- Effort: 6 hours
- Skills: Java, Spring Boot, validation
- Dependencies: Task 1.2, Task 1.3
- Deliverables: AuthController.register(), service layer, tests

**Task 2.2**: Implement login endpoint
- Description: Authenticate user and generate JWT
- Effort: 5 hours
- Skills: Java, Spring Security, JWT
- Dependencies: Task 1.2, Task 1.3
- Deliverables: AuthController.login(), authentication logic, tests

**Task 2.3**: Implement logout endpoint
- Description: Invalidate session/token
- Effort: 3 hours
- Skills: Java, Spring Security
- Dependencies: Task 2.2
- Deliverables: AuthController.logout(), session management

**Task 2.4**: Implement password reset flow
- Description: Request reset, verify token, update password
- Effort: 6 hours
- Skills: Java, email integration
- Dependencies: Task 1.2, Task 1.3
- Deliverables: Password reset endpoints, email templates

### Phase 3: Integration & Testing (16 hours)

**Task 3.1**: Integration tests
- Description: End-to-end tests for all auth flows
- Effort: 8 hours
- Skills: Testing, Spring Boot Test
- Dependencies: All Phase 2 tasks
- Deliverables: Integration test suite

**Task 3.2**: Error handling and validation
- Description: Comprehensive error responses and input validation
- Effort: 4 hours
- Skills: Java, validation
- Dependencies: All Phase 2 tasks
- Deliverables: Error handling, validation rules

**Task 3.3**: API documentation
- Description: OpenAPI/Swagger documentation
- Effort: 2 hours
- Skills: OpenAPI, documentation
- Dependencies: All Phase 2 tasks
- Deliverables: API docs, examples

**Task 3.4**: Security review
- Description: Review for vulnerabilities, test security
- Effort: 2 hours
- Skills: Security testing
- Dependencies: All above tasks
- Deliverables: Security checklist, fixes

### Dependency Graph
```
Task 1.1 (DB Schema)
    ↓
Task 1.2 (Entities) ←┐
    ↓                │
    ├────────────────┤
    ↓                │
Task 2.1 (Register)  │
Task 2.2 (Login)     │← Task 1.3 (Security Config)
Task 2.4 (Reset)     │
    ↓                │
Task 2.3 (Logout) ───┘
    ↓
Task 3.1, 3.2, 3.3 (parallel)
    ↓
Task 3.4 (Security Review)
```

### Critical Path
1.1 → 1.2 → 2.1 → 2.2 → 3.1 → 3.4 (33 hours)

### Parallel Work Opportunities
- Task 1.3 can run parallel to 1.1 → 1.2
- Tasks 2.1, 2.2, 2.4 can partially overlap
- Tasks 3.1, 3.2, 3.3 can run in parallel

### Risks

**Risk**: Email service integration complexity
- Impact: Medium (affects Task 2.4)
- Mitigation: Use proven email library, start early
- Buffer: +2 hours

**Risk**: JWT token security concerns
- Impact: High (affects all endpoints)
- Mitigation: Security review in Task 3.4, use industry standards
- Buffer: Included in security review

**Risk**: Database performance at scale
- Impact: Low (can optimize later)
- Mitigation: Proper indexing in Task 1.1
```

## Related Skills
- `/architecture-design` - Design before planning
- `/implementation` - Execute the tasks
