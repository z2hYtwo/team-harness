---
name: debugging
description: Systematically identify and resolve bugs
agent: debugger
---

# Debugging Skill

## Purpose
Systematically diagnose and fix bugs through structured investigation and root cause analysis.

## Process

### 1. Bug Understanding
- Read bug report carefully
- Clarify reproduction steps
- Understand expected vs actual behavior
- Gather environment details

### 2. Reproduction
- Set up test environment
- Follow reproduction steps
- Confirm the bug
- Document observations

### 3. Investigation
- Review relevant code
- Analyze stack traces
- Check logs
- Use debugging tools

### 4. Root Cause Analysis
- Identify the exact cause
- Understand why it happens
- Check for related issues
- Assess impact

### 5. Fix Implementation
- Design minimal fix
- Implement solution
- Add regression test
- Verify fix works

## Debugging Techniques

### Code Analysis
- **Static analysis**: Read code flow
- **Git blame**: Find when code was introduced
- **Git log**: Check related changes
- **Dependency analysis**: Check upstream/downstream

### Dynamic Analysis
- **Logging**: Add strategic log statements
- **Debugging**: Use breakpoints and stepping
- **Profiling**: Identify performance issues
- **Tracing**: Follow execution path

### Systematic Approach
- **Binary search**: Narrow down problem area
- **Hypothesis testing**: Form and test theories
- **Isolation**: Remove variables one by one
- **Comparison**: Compare working vs broken cases

## Input
- Bug report with reproduction steps
- Stack traces or error logs
- Environment information
- Expected behavior

## Output
```markdown
## Bug Analysis

### Bug Description
**Title**: [Bug title]
**Severity**: Critical/High/Medium/Low
**Environment**: [Where it occurs]

### Reproduction
**Steps**:
1. Step 1
2. Step 2

**Expected**: [What should happen]
**Actual**: [What happens]

### Root Cause
**Location**: [File:Line]
**Cause**: [Technical explanation]
**Why it happens**: [Detailed analysis]

### Impact
- **Affected users**: [Who is impacted]
- **Affected features**: [What breaks]
- **Urgency**: [How urgent]

### Solution
**Approach**: [Chosen fix]
**Alternatives**: [Other options considered]
**Trade-offs**: [Any compromises]

### Fix
[Code changes]

### Verification
- [ ] Bug fixed
- [ ] Regression test added
- [ ] No side effects
- [ ] Edge cases tested
```

## Example

**Input**: "Users can't log in - getting 500 error"

**Investigation**:
```markdown
### Bug Analysis

#### Bug Description
**Title**: Login endpoint returns 500 Internal Server Error
**Severity**: Critical
**Environment**: Production, all users affected

#### Reproduction
**Steps**:
1. Navigate to /login
2. Enter valid credentials
3. Click "Login"

**Expected**: User logged in, redirected to dashboard
**Actual**: 500 error, user not logged in

#### Investigation Steps

1. **Checked logs**:
```
java.lang.NullPointerException
  at com.example.auth.AuthService.authenticate(AuthService.java:42)
  at com.example.auth.AuthController.login(AuthController.java:28)
```

2. **Reviewed code** (AuthService.java:42):
```java
String hashedPassword = user.getPassword().hash();  // NPE here
```

3. **Analyzed user object**:
- User object exists
- But password field is null

4. **Checked database**:
- Password column added recently
- Existing users have null passwords (migration issue)

#### Root Cause
**Location**: AuthService.java:42
**Cause**: NullPointerException when calling .hash() on null password
**Why**: Database migration added password column but didn't set values for existing users

**Related Issue**: Migration script was incomplete

#### Impact
- **Affected users**: All users created before password migration
- **Affected features**: Login, authentication
- **Urgency**: Immediate - system is down

#### Solution
**Approach**: Two-part fix
1. Handle null password gracefully (immediate fix)
2. Data migration to populate passwords (follow-up)

**Alternatives**:
- Option A: Force password reset for all users (disruptive)
- Option B: Lock out affected users (too restrictive)
- Option C: Handle null + gradual migration (chosen - least disruptive)

#### Fix

**Immediate Fix** (AuthService.java):
```java
public boolean authenticate(String email, String password) {
    User user = userRepository.findByEmail(email)
        .orElseThrow(() -> new UserNotFoundException(email));
    
    // Handle legacy users without password
    if (user.getPassword() == null) {
        logger.warn("User {} has null password, forcing reset", email);
        throw new PasswordResetRequiredException(
            "Please reset your password"
        );
    }
    
    return passwordEncoder.matches(password, user.getPassword());
}
```

**Data Migration** (Follow-up):
```java
@Service
public class UserMigrationService {
    
    @Transactional
    public void migrateNullPasswords() {
        List<User> usersWithNullPassword = 
            userRepository.findByPasswordIsNull();
        
        for (User user : usersWithNullPassword) {
            // Generate temp password, send reset email
            String tempPassword = generateTempPassword();
            user.setPassword(passwordEncoder.encode(tempPassword));
            userRepository.save(user);
            emailService.sendPasswordResetEmail(user, tempPassword);
        }
        
        logger.info("Migrated {} users", usersWithNullPassword.size());
    }
}
```

**Regression Test**:
```java
@Test
void authenticate_NullPassword_ThrowsPasswordResetRequired() {
    User user = new User("user@example.com", null);  // null password
    when(userRepository.findByEmail(anyString()))
        .thenReturn(Optional.of(user));
    
    assertThrows(PasswordResetRequiredException.class,
        () -> authService.authenticate("user@example.com", "any"));
}
```

#### Verification
- [x] Bug fixed - null password handled gracefully
- [x] Regression test added
- [x] No side effects - users with valid passwords unaffected
- [x] Edge cases tested - null password case
- [ ] Data migration scheduled (follow-up task)

#### Lessons Learned
- Always handle null values for nullable database columns
- Migration scripts should populate data, not just schema
- Consider existing data when adding required fields
```

## Debugging Checklist

### Information Gathering
- [ ] Reproduction steps clear
- [ ] Environment details collected
- [ ] Logs and stack traces reviewed
- [ ] Recent changes identified (git log)

### Investigation
- [ ] Bug reproduced locally
- [ ] Root cause identified
- [ ] Related issues checked
- [ ] Impact assessed

### Fix
- [ ] Minimal fix implemented
- [ ] Regression test added
- [ ] Edge cases considered
- [ ] Side effects checked

### Verification
- [ ] Fix verified in test environment
- [ ] No regressions introduced
- [ ] Performance impact assessed
- [ ] Documentation updated

## Common Bug Patterns

### NullPointerException
- Check for null before dereferencing
- Use Optional or null checks
- Validate inputs

### ConcurrentModificationException
- Don't modify collection while iterating
- Use thread-safe collections
- Synchronize access

### Memory Leaks
- Close resources (try-with-resources)
- Remove listeners
- Clear references

### Performance Issues
- Check O(n²) algorithms
- Review database queries (N+1)
- Profile hot paths

## Related Skills
- `/implementation` - Implement the fix
- `/testing` - Verify the fix
- `/code-review` - Review fix quality
