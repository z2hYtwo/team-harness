---
name: requirement-analysis
description: Analyze and clarify user requirements
agent: planner
---

# Requirement Analysis Skill

## Purpose
Transform user requests into clear, actionable requirements with acceptance criteria.

## Process

### 1. Requirement Elicitation
- Listen to user needs
- Ask clarifying questions
- Identify implicit requirements
- Understand context and constraints

### 2. Requirement Analysis
- Categorize requirements (functional/non-functional)
- Identify dependencies
- Assess feasibility
- Prioritize requirements

### 3. Requirement Specification
- Write clear requirement statements
- Define acceptance criteria
- Document assumptions
- Note constraints

### 4. Requirement Validation
- Review with stakeholders
- Ensure completeness
- Verify testability
- Confirm understanding

## Input
- User request or feature description
- Project context
- Existing system constraints

## Output
```markdown
## Requirements

### Functional Requirements
- **FR-1**: [Clear, testable requirement]
- **FR-2**: [Clear, testable requirement]

### Non-Functional Requirements
- **NFR-1**: Performance - [Specific metric]
- **NFR-2**: Security - [Specific requirement]

### Acceptance Criteria
- [ ] AC-1: [Specific, testable criterion]
- [ ] AC-2: [Specific, testable criterion]

### Assumptions
- [Assumption 1]

### Constraints
- [Constraint 1]

### Out of Scope
- [What's not included]
```

## Example

**Input**: "Add user authentication"

**Output**:
```markdown
### Functional Requirements
- **FR-1**: Users must be able to register with email and password
- **FR-2**: Users must be able to log in with credentials
- **FR-3**: System must support password reset via email
- **FR-4**: Users must be able to log out

### Non-Functional Requirements
- **NFR-1**: Passwords must be hashed using bcrypt
- **NFR-2**: Session timeout after 30 minutes of inactivity
- **NFR-3**: Login page must load within 2 seconds

### Acceptance Criteria
- [ ] User can register with valid email and strong password
- [ ] User cannot register with duplicate email
- [ ] User can log in with correct credentials
- [ ] User sees error message with incorrect credentials
- [ ] User receives password reset email within 5 minutes
- [ ] User session expires after 30 minutes

### Assumptions
- Email service is available
- Users have access to email

### Constraints
- Must comply with GDPR
- Must work on mobile and desktop

### Out of Scope
- Social login (OAuth)
- Two-factor authentication
```

## Related Skills
- `/architecture-design` - Design system architecture
- `/task-planning` - Break down into tasks
