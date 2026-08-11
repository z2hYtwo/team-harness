# Java 后端开发规范

## 目的

定义团队 Java 后端开发规范，确保代码具备一致性、可维护性、可测试性和生产可用性。

所有 Java 后端项目默认遵循本规范。

---

## 适用范围

- **Java**: JDK 17 / JDK 21
- **框架**: Spring Boot 3.x
- **架构**: 多模块、分布式、微服务
- **构建**: Maven
- **测试**: JUnit 5

---

## 代码风格

### 基础规范
- **遵循 Google Java Style Guide**
- 注释使用中文
- 使用 4 空格缩进
- 行宽限制 100 字符
- 包名全小写，类名大驼峰，方法名小驼峰

### 命名规范
- 使用具有业务语义的命名
- 布尔变量以 `is`、`has`、`can` 开头
- 集合类型加复数后缀（`users`、`orderList`）
- 常量全大写，下划线分隔（`MAX_RETRY_COUNT`）

### 编码原则
- 方法职责单一，避免超长方法（建议 < 50 行）
- 优先组合而非继承
- 避免魔法值，使用常量或枚举
- 禁止重复代码，优先复用已有能力
- 优先不可变对象（使用 `record`、`final`）
- 使用 Optional 避免 null 返回

---

## 架构规范

### 多模块结构
```
project-root/
├── project-api/          # API 接口定义
├── project-common/       # 公共组件
├── project-domain/       # 领域模型
├── project-service/      # 业务服务
├── project-web/          # Web 层
└── project-integration/  # 外部集成
```

### 分层架构
```
Controller  → 接收请求，参数校验，返回响应
   ↓
Service     → 业务逻辑，事务控制
   ↓
Repository  → 数据访问
   ↓
Domain      → 领域模型（Entity、DO）

DTO         → 数据传输对象（入参、出参）
VO          → 视图对象（前端展示）
```

**原则**:
- 高内聚、低耦合
- 单向依赖（上层依赖下层）
- 禁止跨层直接调用
- 各层职责明确，不越界

### 包结构规范
```
com.company.project
├── controller      # 控制器
├── service         # 服务层
│   └── impl       # 服务实现
├── repository      # 数据访问
├── domain         # 领域模型
│   ├── entity     # 实体
│   └── vo         # 值对象
├── dto            # 传输对象
│   ├── request    # 请求 DTO
│   └── response   # 响应 DTO
├── config         # 配置类
├── exception      # 异常定义
├── util           # 工具类
└── constant       # 常量定义
```

---

## Spring Boot 规范

### 依赖注入
```java
// ✅ 推荐：构造器注入
@Service
public class UserService {
    private final UserRepository userRepository;
    private final EmailService emailService;
    
    public UserService(UserRepository userRepository, 
                       EmailService emailService) {
        this.userRepository = userRepository;
        this.emailService = emailService;
    }
}

// ❌ 避免：字段注入
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;  // 不推荐
}
```

### Bean 管理
- Service 必须无状态（Stateless）
- Controller 不处理业务逻辑
- 合理使用 `@Scope` 控制生命周期
- 避免循环依赖

### 配置管理
```java
// 使用 @ConfigurationProperties 管理配置
@ConfigurationProperties(prefix = "app.feature")
@Validated
public record FeatureProperties(
    boolean enableCache,
    int maxRetryCount,
    Duration timeout
) {}
```

---

## 数据库规范

### 通用规范
- **使用参数化查询**（防止 SQL 注入）
- 避免 N+1 查询（使用 JOIN 或批量查询）
- 合理设计索引（WHERE、JOIN、ORDER BY 字段）
- 大数据量必须分页（建议单页 ≤ 100 条）
- 明确事务边界（`@Transactional` 加在 Service 层）
- 避免长事务（事务内不调用外部接口）

### JPA/Hibernate
```java
// ✅ 推荐：使用 JPQL 避免 N+1
@Query("SELECT u FROM User u LEFT JOIN FETCH u.roles WHERE u.id = :id")
Optional<User> findByIdWithRoles(@Param("id") Long id);

// ✅ 推荐：批量操作
@Modifying
@Query("UPDATE User u SET u.status = :status WHERE u.id IN :ids")
void batchUpdateStatus(@Param("ids") List<Long> ids, 
                       @Param("status") String status);
```

### 多数据源支持
- MySQL: 主要业务数据
- PostgreSQL: 需要高级特性的数据
- Milvus: 向量检索（AI/搜索场景）

---

## 异常处理

### 异常层次
```java
// 基础业务异常
public class BusinessException extends RuntimeException {
    private final String errorCode;
    private final Object[] args;
}

// 具体业务异常
public class UserNotFoundException extends BusinessException {
    public UserNotFoundException(Long userId) {
        super("USER_NOT_FOUND", new Object[]{userId});
    }
}
```

### 统一异常处理
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(BusinessException.class)
    public ResponseEntity<ErrorResponse> handleBusinessException(
            BusinessException e) {
        // 返回统一错误格式
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationException(
            MethodArgumentNotValidException e) {
        // 参数校验异常
    }
}
```

### 原则
- 禁止空 catch
- 禁止吞异常（至少记录日志）
- 不要用异常做流程控制
- 自定义异常继承合适的父类

---

## 日志规范

### 日志框架
- 使用 Logback（Spring Boot 默认）
- 配置文件: `logback-spring.xml`
- 日志级别: TRACE < DEBUG < INFO < WARN < ERROR

### 日志规范
```java
// ✅ 推荐：使用占位符
log.info("用户登录成功, userId={}, ip={}", userId, ip);

// ❌ 避免：字符串拼接
log.info("用户登录成功, userId=" + userId + ", ip=" + ip);

// ✅ 推荐：异常记录带堆栈
log.error("处理订单失败, orderId={}", orderId, exception);

// ✅ 推荐：关键业务节点记录
log.info("开始处理订单, orderId={}", orderId);
log.info("订单处理完成, orderId={}, cost={}ms", orderId, cost);
```

### 日志内容
**应该记录**:
- 关键业务操作（登录、下单、支付）
- 外部调用（API、MQ、缓存）
- 异常错误（带上下文和堆栈）
- 性能指标（慢查询、慢接口）

**禁止记录**:
- 密码、密钥
- Token、Session
- 用户敏感信息（身份证、手机号完整内容）

### 日志级别
- **ERROR**: 系统错误，需要立即处理
- **WARN**: 警告信息，需要关注
- **INFO**: 关键业务节点（生产环境默认级别）
- **DEBUG**: 详细调试信息（开发环境）
- **TRACE**: 最详细的追踪信息

---

## 安全规范

### 基本原则
- 默认遵循最小权限原则
- 永远不信任用户输入
- 敏感操作必须鉴权

### 输入校验
```java
// 使用 Bean Validation
public record CreateUserRequest(
    @NotBlank(message = "用户名不能为空")
    @Size(min = 3, max = 20, message = "用户名长度 3-20")
    String username,
    
    @NotBlank(message = "密码不能为空")
    @Pattern(regexp = "^(?=.*[A-Z])(?=.*[a-z])(?=.*\\d).{8,}$",
             message = "密码必须包含大小写字母和数字，至少 8 位")
    String password,
    
    @Email(message = "邮箱格式不正确")
    String email
) {}
```

### 安全检查清单
- [ ] 输入校验（长度、格式、范围）
- [ ] 权限控制（@PreAuthorize）
- [ ] SQL 注入防护（参数化查询）
- [ ] XSS 防护（输出转义）
- [ ] CSRF 防护（Spring Security 默认开启）
- [ ] JWT 安全（签名验证、过期检查）
- [ ] 敏感信息保护（加密存储）

### 密码处理
```java
// ✅ 使用 BCrypt
@Service
public class SecurityService {
    private final PasswordEncoder passwordEncoder;
    
    public String hashPassword(String rawPassword) {
        return passwordEncoder.encode(rawPassword);
    }
    
    public boolean matches(String raw, String encoded) {
        return passwordEncoder.matches(raw, encoded);
    }
}
```

---

## 性能规范

### 关注点
- **时间复杂度**: 避免 O(n²) 以上算法
- **内存占用**: 避免大对象、内存泄漏
- **数据库**: 索引优化、批量操作
- **IO 操作**: 异步处理、连接池
- **缓存**: 合理使用 Redis/本地缓存

### 性能优化
```java
// ✅ 批量查询避免 N+1
List<Long> userIds = orders.stream()
    .map(Order::getUserId)
    .distinct()
    .toList();
Map<Long, User> userMap = userRepository.findAllById(userIds)
    .stream()
    .collect(Collectors.toMap(User::getId, u -> u));

// ✅ 使用缓存
@Cacheable(value = "users", key = "#id")
public User getUserById(Long id) {
    return userRepository.findById(id)
        .orElseThrow(() -> new UserNotFoundException(id));
}

// ✅ 异步处理
@Async
public CompletableFuture<Void> sendEmailAsync(String to, String content) {
    emailService.send(to, content);
    return CompletableFuture.completedFuture(null);
}
```

---

## 测试规范

### 测试覆盖
新增功能必须包含：
- **单元测试**: 覆盖核心业务逻辑
- **边界测试**: 测试边界条件
- **异常测试**: 测试异常分支

核心业务建议增加：
- **集成测试**: 测试多组件协作
- **性能测试**: 压测关键接口

### JUnit 5
```java
@SpringBootTest
class UserServiceTest {
    
    @Autowired
    private UserService userService;
    
    @MockBean
    private UserRepository userRepository;
    
    @Test
    @DisplayName("创建用户 - 成功")
    void createUser_Success() {
        // Given
        CreateUserRequest request = new CreateUserRequest(
            "testuser", "Password123", "test@example.com"
        );
        
        // When
        User user = userService.createUser(request);
        
        // Then
        assertThat(user).isNotNull();
        assertThat(user.getUsername()).isEqualTo("testuser");
        verify(userRepository).save(any(User.class));
    }
    
    @Test
    @DisplayName("创建用户 - 用户名已存在")
    void createUser_UsernameExists() {
        // Given
        when(userRepository.existsByUsername("testuser"))
            .thenReturn(true);
        
        // When & Then
        assertThatThrownBy(() -> userService.createUser(request))
            .isInstanceOf(UsernameAlreadyExistsException.class);
    }
}
```

### 测试原则
- 测试命名清晰（方法名_场景_预期结果）
- 使用中文 `@DisplayName`
- 遵循 Given-When-Then 结构
- 测试应该独立、可重复
- Mock 外部依赖

---

## JDK 17/21 特性

### Record（JDK 14+）
```java
// DTO 优先使用 record
public record UserDTO(
    Long id,
    String username,
    String email,
    LocalDateTime createdAt
) {}
```

### Sealed Classes（JDK 17+）
```java
// 限制继承层次
public sealed interface PaymentMethod 
    permits CreditCard, Alipay, WechatPay {}

public final class CreditCard implements PaymentMethod {}
public final class Alipay implements PaymentMethod {}
public final class WechatPay implements PaymentMethod {}
```

### Pattern Matching（JDK 17+）
```java
// instanceof 模式匹配
if (obj instanceof String str) {
    System.out.println(str.toUpperCase());
}

// switch 模式匹配（JDK 21）
String result = switch (obj) {
    case Integer i -> "整数: " + i;
    case String s -> "字符串: " + s;
    case null -> "空值";
    default -> "其他";
};
```

### Virtual Threads（JDK 21）
```java
// 虚拟线程（高并发场景）
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    executor.submit(() -> {
        // 任务代码
    });
}
```

---

## 开发原则

### SOLID 原则
- **S**ingle Responsibility: 单一职责
- **O**pen-Closed: 开闭原则
- **L**iskov Substitution: 里氏替换
- **I**nterface Segregation: 接口隔离
- **D**ependency Inversion: 依赖倒置

### 核心原则
- **生产就绪**: Production Ready
- **可读性优先**: Readability First
- **优先复用**: Reuse First
- **默认安全**: Security by Default
- **性能意识**: Performance Awareness
- **保持简单**: Keep It Simple
- **中文注释**: Comments in Chinese