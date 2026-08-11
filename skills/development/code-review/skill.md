---
name: code-review
description: 全面的代码审查清单和流程
agent: reviewer
---

# 代码审查技能

## 目的
通过系统化的代码审查，确保代码质量、安全性和可维护性。

## 审查流程

### 1. 预审查
- 检查 PR 描述是否清晰
- 确认 CI/CD 检查通过
- 验证测试覆盖率
- 检查代码量是否合理（建议 < 500 行）

### 2. 功能审查
- 验证实现符合需求
- 检查业务逻辑正确性
- 测试边界情况
- 验证错误处理

### 3. 代码质量审查
- 检查代码可读性
- 验证命名规范
- 检查代码复杂度
- 评估设计模式使用

### 4. 安全审查
- 检查输入验证
- 识别SQL注入风险
- 检查权限控制
- 验证敏感信息处理

### 5. 性能审查
- 识别性能瓶颈
- 检查算法复杂度
- 审查数据库查询
- 评估资源使用

## 审查清单

### 功能正确性
- [ ] 实现符合需求规格
- [ ] 边界情况已处理
- [ ] 错误处理完善
- [ ] 测试用例覆盖关键场景

### 代码质量
- [ ] 代码遵循规范（Google Style）
- [ ] 命名清晰且一致
- [ ] 函数职责单一
- [ ] 无重复代码
- [ ] 注释清晰（中文）
- [ ] 无 TODO 或已说明

### 架构设计
- [ ] 符合现有架构
- [ ] 层次划分合理
- [ ] 依赖关系清晰
- [ ] 可扩展性好

### 安全性
- [ ] 输入验证充分
- [ ] 无 SQL 注入风险
- [ ] 权限控制正确
- [ ] 敏感信息已保护
- [ ] 无硬编码密钥

### 性能
- [ ] 无明显性能问题
- [ ] 数据库查询优化
- [ ] 避免 N+1 查询
- [ ] 大数据集有分页
- [ ] 缓存使用合理

### 测试
- [ ] 单元测试完备
- [ ] 测试覆盖率达标（>80%）
- [ ] 测试用例有意义
- [ ] 集成测试（如需要）
- [ ] 测试命名清晰

### 文档
- [ ] 复杂逻辑有注释
- [ ] API 文档已更新
- [ ] README 已更新（如需要）
- [ ] 变更日志已记录

## Java 代码审查要点

### Spring Boot
```java
// ✅ 推荐：构造器注入
@Service
public class UserService {
    private final UserRepository repository;
    
    public UserService(UserRepository repository) {
        this.repository = repository;
    }
}

// ❌ 避免：字段注入
@Service
public class UserService {
    @Autowired
    private UserRepository repository;  // 不推荐
}
```

### 事务处理
```java
// ✅ 推荐：事务边界清晰
@Transactional
public void transferMoney(Long from, Long to, BigDecimal amount) {
    // 事务内操作
    accountService.deduct(from, amount);
    accountService.add(to, amount);
}

// ❌ 避免：事务内调用外部服务
@Transactional
public void processOrder(Order order) {
    orderRepository.save(order);
    externalApi.notify(order);  // 事务内调用外部 API，可能超时
}
```

### 异常处理
```java
// ✅ 推荐：具体的异常处理
try {
    User user = userRepository.findById(id)
        .orElseThrow(() -> new UserNotFoundException(id));
} catch (UserNotFoundException e) {
    log.error("用户不存在: {}", id);
    throw e;
}

// ❌ 避免：空 catch
try {
    doSomething();
} catch (Exception e) {
    // 什么都不做
}
```

## Python 代码审查要点

### 类型提示
```python
# ✅ 推荐：使用类型提示
def get_user(user_id: int) -> Optional[User]:
    return user_repository.find_by_id(user_id)

# ❌ 避免：无类型提示
def get_user(user_id):
    return user_repository.find_by_id(user_id)
```

### 异常处理
```python
# ✅ 推荐：具体的异常类型
try:
    user = get_user(user_id)
except UserNotFoundException as e:
    logger.error(f"用户不存在: {user_id}", exc_info=True)
    raise

# ❌ 避免：裸 except
try:
    user = get_user(user_id)
except:  # 禁止
    pass
```

### 异步编程
```python
# ✅ 推荐：异步函数
async def fetch_users(user_ids: List[int]) -> List[User]:
    tasks = [fetch_user(uid) for uid in user_ids]
    return await asyncio.gather(*tasks)

# 确保统一使用 asyncio，不混用其他异步库
```

## 前端代码审查要点

### 组件设计
```vue
<!-- ✅ 推荐：Props 类型明确 -->
<script setup lang="ts">
interface Props {
  userId: number
  userName: string
  onSubmit?: (data: FormData) => void
}
const props = defineProps<Props>()
</script>

<!-- ❌ 避免：Props 无类型 -->
<script setup>
const props = defineProps(['userId', 'userName'])
</script>
```

### 性能优化
```tsx
// ✅ 推荐：使用 memo 避免不必要渲染
export const UserCard = React.memo<UserCardProps>(({ user }) => {
  return <div>{user.name}</div>
})

// ✅ 推荐：使用 useCallback 缓存函数
const handleClick = useCallback(() => {
  onSelect(user.id)
}, [user.id, onSelect])
```

## 常见问题

### 安全问题
```java
// ❌ SQL 注入风险
String sql = "SELECT * FROM users WHERE name = '" + name + "'";

// ✅ 使用参数化查询
@Query("SELECT u FROM User u WHERE u.name = :name")
User findByName(@Param("name") String name);
```

```java
// ❌ 敏感信息泄露
log.info("用户登录: username={}, password={}", username, password);

// ✅ 不记录敏感信息
log.info("用户登录: username={}", username);
```

### 性能问题
```java
// ❌ N+1 查询
List<Order> orders = orderRepository.findAll();
for (Order order : orders) {
    User user = userRepository.findById(order.getUserId());  // N+1
}

// ✅ JOIN 查询
@Query("SELECT o FROM Order o JOIN FETCH o.user")
List<Order> findAllWithUser();
```

### 代码重复
```java
// ❌ 重复代码
public void createUser(CreateUserRequest request) {
    if (request.getUsername() == null || request.getUsername().isEmpty()) {
        throw new ValidationException("用户名不能为空");
    }
    // ...
}

public void updateUser(UpdateUserRequest request) {
    if (request.getUsername() == null || request.getUsername().isEmpty()) {
        throw new ValidationException("用户名不能为空");
    }
    // ...
}

// ✅ 提取公共方法
private void validateUsername(String username) {
    if (username == null || username.isEmpty()) {
        throw new ValidationException("用户名不能为空");
    }
}
```

## 审查反馈

### 反馈原则
- **建设性**: 提供解决方案，不只是指出问题
- **具体**: 引用具体代码位置和行号
- **优先级**: 按严重程度分类（阻塞/建议）
- **尊重**: 保持专业和友好

### 反馈分类
- **Critical (阻塞)**: 安全问题、功能错误、性能严重问题
- **Major (重要)**: 代码质量、架构问题、可维护性
- **Minor (建议)**: 命名、格式、最佳实践

### 反馈模板
```markdown
**文件**: `src/main/java/com/example/UserService.java:42`

**问题**: SQL 注入风险

**描述**: 
直接拼接用户输入到 SQL 语句中，存在 SQL 注入风险。

**建议**:
使用 JPA 的 @Query 注解和参数绑定：
\`\`\`java
@Query("SELECT u FROM User u WHERE u.name = :name")
User findByName(@Param("name") String name);
\`\`\`

**优先级**: Critical
```

## 审查工具

### 静态分析
- **Java**: SonarQube, Checkstyle, SpotBugs
- **Python**: pylint, flake8, mypy
- **TypeScript**: ESLint, TypeScript Compiler

### 代码覆盖率
- **Java**: JaCoCo
- **Python**: pytest-cov
- **JavaScript**: Istanbul/nyc

### 依赖检查
- **Java**: OWASP Dependency-Check
- **Python**: Safety
- **Node.js**: npm audit

## 关联技能
- `/implementation` - 了解实现细节
- `/testing` - 审查测试质量
- `/debugging` - 识别潜在问题
