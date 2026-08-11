---
name: implementation
description: Write production-quality code following best practices
agent: coder
---

# Implementation Skill

## Purpose
Transform design and task specifications into clean, efficient, maintainable production code.

## Process

### 1. Task Understanding
- Review task requirements
- Understand acceptance criteria
- Check architecture design
- Identify dependencies

### 2. Code Writing
- Write implementation code
- Follow coding standards
- Apply design patterns
- Handle edge cases

### 3. Testing
- Write unit tests
- Ensure coverage
- Test edge cases
- Verify functionality

### 4. Documentation
- Add inline comments for complex logic
- Document public APIs
- Update relevant docs

## Coding Guidelines

### Code Quality
- **Clear naming**: Use descriptive names, avoid abbreviations
- **Single Responsibility**: Each function/class does one thing
- **DRY**: Don't repeat yourself
- **Small functions**: Keep functions focused and short
- **Consistent formatting**: Follow project style guide

### Error Handling
- Validate inputs at system boundaries
- Provide meaningful error messages
- Use appropriate exception types
- Log errors with context

### Security
- Sanitize all user inputs
- Use parameterized queries (prevent SQL injection)
- Validate and escape data
- Protect sensitive information

### Performance
- Choose appropriate data structures
- Avoid premature optimization
- Consider algorithm complexity
- Profile when needed

## Input
- Task specification
- Architecture design
- Coding standards
- Existing codebase context

## Output
- Production code
- Unit tests
- Documentation updates
- Implementation notes

## Example

**Task**: Implement user registration endpoint

**Output**:
```java
@RestController
@RequestMapping("/api/auth")
@Validated
public class AuthController {
    
    private final UserService userService;
    private final EmailService emailService;
    
    @PostMapping("/register")
    public ResponseEntity<UserResponse> register(
            @Valid @RequestBody RegisterRequest request) {
        
        // Check if email already exists
        if (userService.existsByEmail(request.getEmail())) {
            throw new EmailAlreadyExistsException(request.getEmail());
        }
        
        // Create user with hashed password
        User user = userService.createUser(
            request.getEmail(),
            request.getPassword()
        );
        
        // Send verification email
        emailService.sendVerificationEmail(user);
        
        return ResponseEntity
            .status(HttpStatus.CREATED)
            .body(UserResponse.from(user));
    }
}

// DTO with validation
public record RegisterRequest(
    @NotBlank
    @Email
    String email,
    
    @NotBlank
    @Size(min = 8, max = 100)
    @Pattern(regexp = "^(?=.*[A-Z])(?=.*[a-z])(?=.*\\d).*$",
             message = "Password must contain uppercase, lowercase, and digit")
    String password
) {}

// Unit test
@WebMvcTest(AuthController.class)
class AuthControllerTest {
    
    @Test
    void register_ValidRequest_ReturnsCreated() throws Exception {
        RegisterRequest request = new RegisterRequest(
            "user@example.com",
            "SecurePass123"
        );
        
        mockMvc.perform(post("/api/auth/register")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
            .andExpect(status().isCreated())
            .andExpect(jsonPath("$.email").value("user@example.com"));
    }
    
    @Test
    void register_DuplicateEmail_ThrowsException() throws Exception {
        when(userService.existsByEmail(anyString())).thenReturn(true);
        
        RegisterRequest request = new RegisterRequest(
            "existing@example.com",
            "SecurePass123"
        );
        
        mockMvc.perform(post("/api/auth/register")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
            .andExpect(status().isConflict());
    }
    
    @Test
    void register_InvalidPassword_ReturnsBadRequest() throws Exception {
        RegisterRequest request = new RegisterRequest(
            "user@example.com",
            "weak"  // Too short, no uppercase/digit
        );
        
        mockMvc.perform(post("/api/auth/register")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
            .andExpect(status().isBadRequest());
    }
}
```

**Implementation Notes**:
- Used Bean Validation for input validation
- Password pattern enforces strong password policy
- Checked for duplicate email before creating user
- Used record for immutable DTO
- Comprehensive test coverage (happy path + error cases)
- Follows RESTful conventions (POST, 201 Created)

## Best Practices

### Java/Spring Boot
- Use constructor injection
- Leverage Spring Boot auto-configuration
- Use `@Valid` for request validation
- Return appropriate HTTP status codes
- Use records for immutable DTOs

### Testing
- Test happy path and error cases
- Use meaningful test names
- Mock external dependencies
- Aim for >80% coverage on business logic
- Test edge cases and boundary conditions

### Documentation
- Document complex algorithms
- Explain non-obvious decisions
- Keep comments up to date
- Don't document obvious code

## Related Skills
- `/task-planning` - Plan before implementing
- `/testing` - Write comprehensive tests
- `/code-review` - Review implementation
