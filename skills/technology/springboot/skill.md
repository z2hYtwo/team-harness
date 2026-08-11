---
name: springboot
description: Spring Boot 3.x 开发最佳实践
---

# Spring Boot 技能

## 适用版本
- Spring Boot 3.x
- JDK 17/21
- Maven

## 项目结构

```
src/main/java/
└── com.company.project
    ├── Application.java        # 启动类
    ├── controller/            # 控制器
    ├── service/               # 服务层
    │   └── impl/
    ├── repository/            # 数据访问
    ├── domain/                # 领域模型
    │   ├── entity/           # JPA 实体
    │   └── vo/               # 值对象
    ├── dto/                   # 传输对象
    │   ├── request/
    │   └── response/
    ├── config/                # 配置类
    ├── exception/             # 异常定义
    ├── constant/              # 常量
    └── util/                  # 工具类
```

## 依赖注入

### 构造器注入（推荐）
```java
@Service
public class UserService {
    private final UserRepository userRepository;
    private final EmailService emailService;
    
    // 单个构造器可省略 @Autowired
    public UserService(UserRepository userRepository, 
                       EmailService emailService) {
        this.userRepository = userRepository;
        this.emailService = emailService;
    }
}
```

### 避免字段注入
```java
// ❌ 不推荐
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;
}
```

## REST API

### Controller 规范
```java
@RestController
@RequestMapping("/api/users")
@Validated
public class UserController {
    
    private final UserService userService;
    
    public UserController(UserService userService) {
        this.userService = userService;
    }
    
    @PostMapping
    public ResponseEntity<UserResponse> createUser(
            @Valid @RequestBody CreateUserRequest request) {
        User user = userService.createUser(request);
        return ResponseEntity
            .status(HttpStatus.CREATED)
            .body(UserResponse.from(user));
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<UserResponse> getUser(@PathVariable Long id) {
        User user = userService.getUserById(id);
        return ResponseEntity.ok(UserResponse.from(user));
    }
    
    @PutMapping("/{id}")
    public ResponseEntity<UserResponse> updateUser(
            @PathVariable Long id,
            @Valid @RequestBody UpdateUserRequest request) {
        User user = userService.updateUser(id, request);
        return ResponseEntity.ok(UserResponse.from(user));
    }
    
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        userService.deleteUser(id);
        return ResponseEntity.noContent().build();
    }
}
```

### DTO 和 Validation
```java
public record CreateUserRequest(
    @NotBlank(message = "用户名不能为空")
    @Size(min = 3, max = 20, message = "用户名长度 3-20")
    String username,
    
    @NotBlank(message = "邮箱不能为空")
    @Email(message = "邮箱格式不正确")
    String email,
    
    @NotBlank(message = "密码不能为空")
    @Pattern(regexp = "^(?=.*[A-Z])(?=.*[a-z])(?=.*\\d).{8,}$",
             message = "密码必须包含大小写字母和数字，至少 8 位")
    String password
) {}
```

## 数据访问 (JPA)

### Entity
```java
@Entity
@Table(name = "users", indexes = {
    @Index(name = "idx_username", columnList = "username"),
    @Index(name = "idx_email", columnList = "email")
})
public class User {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false, unique = true, length = 50)
    private String username;
    
    @Column(nullable = false, unique = true)
    private String email;
    
    @Column(nullable = false)
    private String password;
    
    @CreatedDate
    @Column(nullable = false, updatable = false)
    private LocalDateTime createdAt;
    
    @LastModifiedDate
    private LocalDateTime updatedAt;
    
    // Getters and setters
}
```

### Repository
```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    
    Optional<User> findByUsername(String username);
    
    boolean existsByUsername(String username);
    
    @Query("SELECT u FROM User u WHERE u.email = :email")
    Optional<User> findByEmail(@Param("email") String email);
    
    // 避免 N+1，使用 JOIN FETCH
    @Query("SELECT u FROM User u LEFT JOIN FETCH u.roles WHERE u.id = :id")
    Optional<User> findByIdWithRoles(@Param("id") Long id);
}
```

## 事务管理

```java
@Service
public class OrderService {
    
    @Transactional
    public void createOrder(CreateOrderRequest request) {
        // 事务内操作
        Order order = new Order();
        orderRepository.save(order);
        inventoryService.deduct(request.getProductId(), request.getQuantity());
        
        // 事务结束时自动提交
    }
    
    @Transactional(readOnly = true)
    public List<Order> getUserOrders(Long userId) {
        // 只读事务，性能优化
        return orderRepository.findByUserId(userId);
    }
    
    // 不要在事务内调用外部 API
    public void processOrder(Long orderId) {
        Order order = createOrderInTransaction(orderId);
        // 事务外调用外部 API
        externalApiService.notify(order);
    }
    
    @Transactional
    private Order createOrderInTransaction(Long orderId) {
        // 事务内操作
        return orderRepository.save(new Order());
    }
}
```

## 异常处理

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    private static final Logger log = LoggerFactory.getLogger(GlobalExceptionHandler.class);
    
    @ExceptionHandler(BusinessException.class)
    public ResponseEntity<ErrorResponse> handleBusinessException(BusinessException e) {
        log.warn("业务异常: {}", e.getMessage());
        return ResponseEntity
            .status(HttpStatus.BAD_REQUEST)
            .body(new ErrorResponse(e.getCode(), e.getMessage()));
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationException(
            MethodArgumentNotValidException e) {
        String message = e.getBindingResult().getFieldErrors()
            .stream()
            .map(FieldError::getDefaultMessage)
            .collect(Collectors.joining(", "));
        
        return ResponseEntity
            .status(HttpStatus.BAD_REQUEST)
            .body(new ErrorResponse("VALIDATION_ERROR", message));
    }
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleException(Exception e) {
        log.error("系统异常", e);
        return ResponseEntity
            .status(HttpStatus.INTERNAL_SERVER_ERROR)
            .body(new ErrorResponse("INTERNAL_ERROR", "系统错误，请稍后重试"));
    }
}
```

## 配置管理

```java
@ConfigurationProperties(prefix = "app.feature")
@Validated
public record FeatureProperties(
    @NotNull Boolean enableCache,
    @Min(1) @Max(10) Integer maxRetryCount,
    @NotNull Duration timeout
) {}

// application.yml
app:
  feature:
    enable-cache: true
    max-retry-count: 3
    timeout: 30s
```

## 缓存

```java
@Configuration
@EnableCaching
public class CacheConfig {
    
    @Bean
    public CacheManager cacheManager() {
        return new ConcurrentMapCacheManager("users", "products");
    }
}

@Service
public class UserService {
    
    @Cacheable(value = "users", key = "#id")
    public User getUserById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));
    }
    
    @CacheEvict(value = "users", key = "#id")
    public void updateUser(Long id, UpdateUserRequest request) {
        // 更新用户，清除缓存
    }
    
    @CacheEvict(value = "users", allEntries = true)
    public void deleteAllUsers() {
        // 清除所有用户缓存
    }
}
```

## 异步处理

```java
@Configuration
@EnableAsync
public class AsyncConfig {
    
    @Bean(name = "taskExecutor")
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);
        executor.setMaxPoolSize(10);
        executor.setQueueCapacity(100);
        executor.setThreadNamePrefix("async-");
        executor.initialize();
        return executor;
    }
}

@Service
public class EmailService {
    
    @Async("taskExecutor")
    public CompletableFuture<Void> sendEmailAsync(String to, String content) {
        // 异步发送邮件
        sendEmail(to, content);
        return CompletableFuture.completedFuture(null);
    }
}
```

## 最佳实践

1. **依赖注入**: 使用构造器注入
2. **事务**: 明确事务边界，避免长事务
3. **异常**: 使用 `@RestControllerAdvice` 统一处理
4. **验证**: 使用 Bean Validation
5. **缓存**: 合理使用 `@Cacheable`
6. **异步**: 使用 `@Async` 处理耗时操作
7. **配置**: 使用 `@ConfigurationProperties`
8. **测试**: 使用 `@SpringBootTest` 集成测试
