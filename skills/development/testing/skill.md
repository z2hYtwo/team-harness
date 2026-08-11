---
name: testing
description: 编写全面的自动化测试
agent: coder
---

# 测试技能

## 目的
编写全面、可靠的自动化测试，确保代码质量和功能正确性。

## 流程

### 1. 测试规划
- 识别测试范围
- 定义测试用例
- 准备测试数据
- 选择测试策略

### 2. 单元测试
- 测试独立单元（函数、方法、类）
- Mock 外部依赖
- 测试边界条件
- 达到目标覆盖率

### 3. 集成测试
- 测试组件交互
- 测试数据库集成
- 测试 API 契约
- 测试第三方服务集成

### 4. 端到端测试
- 测试完整业务流程
- 模拟真实用户场景
- 验证系统整体功能

### 5. 测试审查
- 检查测试覆盖率
- 审查测试质量
- 重构测试代码
- 更新测试文档

## 测试策略

### 测试金字塔
```
    /\
   /E2E\      端到端测试（少量）
  /------\
 /集成测试\    集成测试（适量）
/---------\
/单元测试  \   单元测试（大量）
-----------
```

**原则**:
- 单元测试占 70%
- 集成测试占 20%
- 端到端测试占 10%

## Java / JUnit 5

### 单元测试
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
            "zhangsan", "Password123", "test@example.com"
        );
        User savedUser = new User(1L, "zhangsan", "test@example.com");
        when(userRepository.save(any(User.class))).thenReturn(savedUser);
        
        // When
        User result = userService.createUser(request);
        
        // Then
        assertThat(result).isNotNull();
        assertThat(result.getUsername()).isEqualTo("zhangsan");
        verify(userRepository).save(any(User.class));
    }
    
    @Test
    @DisplayName("创建用户 - 用户名已存在")
    void createUser_UsernameExists() {
        // Given
        when(userRepository.existsByUsername("zhangsan")).thenReturn(true);
        
        // When & Then
        assertThatThrownBy(() -> userService.createUser(request))
            .isInstanceOf(UsernameAlreadyExistsException.class)
            .hasMessageContaining("用户名已存在");
    }
    
    @ParameterizedTest
    @ValueSource(strings = {"", " ", "ab"})
    @DisplayName("创建用户 - 用户名无效")
    void createUser_InvalidUsername(String username) {
        // Given
        CreateUserRequest request = new CreateUserRequest(
            username, "Password123", "test@example.com"
        );
        
        // When & Then
        assertThatThrownBy(() -> userService.createUser(request))
            .isInstanceOf(ValidationException.class);
    }
}
```

### 集成测试
```java
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
@AutoConfigureTestDatabase
@Sql(scripts = "/test-data.sql")
class UserControllerIntegrationTest {
    
    @Autowired
    private TestRestTemplate restTemplate;
    
    @Autowired
    private UserRepository userRepository;
    
    @Test
    @DisplayName("注册用户 - 完整流程")
    void registerUser_FullFlow() {
        // Given
        CreateUserRequest request = new CreateUserRequest(
            "lisi", "Password123", "lisi@example.com"
        );
        
        // When: 调用注册接口
        ResponseEntity<UserResponse> response = restTemplate
            .postForEntity("/api/auth/register", request, UserResponse.class);
        
        // Then: 验证响应
        assertThat(response.getStatusCode()).isEqualTo(HttpStatus.CREATED);
        assertThat(response.getBody()).isNotNull();
        assertThat(response.getBody().getUsername()).isEqualTo("lisi");
        
        // Then: 验证数据库
        Optional<User> savedUser = userRepository.findByUsername("lisi");
        assertThat(savedUser).isPresent();
        assertThat(savedUser.get().getEmail()).isEqualTo("lisi@example.com");
    }
}
```

## Python / pytest

### 单元测试
```python
import pytest
from unittest.mock import Mock, AsyncMock, patch

class TestUserService:
    """用户服务测试"""
    
    @pytest.fixture
    def service(self):
        """创建服务实例"""
        return UserService(repository=Mock())
    
    def test_create_user_success(self, service):
        """测试创建用户 - 成功"""
        # Given
        request = CreateUserRequest(
            username="zhangsan",
            email="test@example.com",
            password="Password123"
        )
        service.repository.exists_by_username = Mock(return_value=False)
        service.repository.save = Mock(return_value=User(id=1, username="zhangsan"))
        
        # When
        user = service.create_user(request)
        
        # Then
        assert user is not None
        assert user.username == "zhangsan"
        service.repository.save.assert_called_once()
    
    def test_create_user_username_exists(self, service):
        """测试创建用户 - 用户名已存在"""
        # Given
        service.repository.exists_by_username = Mock(return_value=True)
        
        # When & Then
        with pytest.raises(UsernameExistsError):
            service.create_user(request)
    
    @pytest.mark.parametrize("username", ["", " ", "ab"])
    def test_create_user_invalid_username(self, service, username):
        """测试创建用户 - 用户名无效"""
        # Given
        request = CreateUserRequest(
            username=username,
            email="test@example.com",
            password="Password123"
        )
        
        # When & Then
        with pytest.raises(ValidationError):
            service.create_user(request)

# 异步测试
@pytest.mark.asyncio
async def test_async_fetch_user():
    """测试异步获取用户"""
    # Given
    service = UserService()
    
    # When
    user = await service.fetch_user(user_id=1)
    
    # Then
    assert user.id == 1
```

### 集成测试 (FastAPI)
```python
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)

def test_register_user_full_flow():
    """测试注册用户 - 完整流程"""
    # Given
    request_data = {
        "username": "lisi",
        "email": "lisi@example.com",
        "password": "Password123"
    }
    
    # When: 调用注册接口
    response = client.post("/api/auth/register", json=request_data)
    
    # Then: 验证响应
    assert response.status_code == 201
    data = response.json()
    assert data["username"] == "lisi"
    assert data["email"] == "lisi@example.com"
    
    # Then: 验证可以登录
    login_response = client.post("/api/auth/login", json={
        "username": "lisi",
        "password": "Password123"
    })
    assert login_response.status_code == 200
    assert "token" in login_response.json()
```

## 前端测试 (Vue/React)

### 组件测试
```typescript
// Vue Test Utils + Vitest
import { mount } from '@vue/test-utils'
import { describe, it, expect, vi } from 'vitest'
import UserCard from '@/components/UserCard.vue'

describe('UserCard', () => {
  it('渲染用户信息', () => {
    const user = { id: 1, name: '张三', email: 'test@example.com' }
    const wrapper = mount(UserCard, {
      props: { user }
    })
    
    expect(wrapper.text()).toContain('张三')
    expect(wrapper.text()).toContain('test@example.com')
  })
  
  it('点击编辑按钮触发事件', async () => {
    const wrapper = mount(UserCard)
    const onEdit = vi.fn()
    wrapper.vm.$emit = onEdit
    
    await wrapper.find('[data-testid="edit-button"]').trigger('click')
    
    expect(onEdit).toHaveBeenCalled()
  })
})
```

## 测试最佳实践

### 测试命名
```java
// ✅ 清晰的命名: 方法名_场景_预期结果
void createUser_UsernameExists_ThrowsException()

// ✅ 中文 DisplayName
@DisplayName("创建用户 - 用户名已存在时抛出异常")
```

### Given-When-Then 结构
```java
@Test
void testExample() {
    // Given: 准备测试数据和环境
    User user = new User("zhangsan");
    
    // When: 执行被测试的操作
    boolean result = service.validate(user);
    
    // Then: 验证结果
    assertThat(result).isTrue();
}
```

### Mock 原则
- Mock 外部依赖（数据库、API、文件系统）
- 不 Mock 被测试对象本身
- 验证 Mock 的交互（`verify`）

### 测试独立性
- 每个测试应该独立
- 测试间不应有顺序依赖
- 使用 `@Before` / `setUp` 准备环境
- 使用 `@After` / `tearDown` 清理环境

### 测试数据
```java
// ✅ 使用有意义的测试数据
User user = new User("zhangsan", "test@example.com");

// ❌ 避免无意义的数据
User user = new User("abc", "xxx@xxx.com");
```

## 测试覆盖率

### 目标
- 单元测试: **核心业务逻辑 > 80%**
- 分支覆盖: **> 70%**
- 不强求 100% 覆盖率

### 重点覆盖
- 核心业务逻辑
- 复杂算法
- 边界条件
- 异常分支
- 安全相关代码

### 可忽略
- 简单的 getter/setter
- 配置类
- 数据模型（POJO）

## 测试工具

### Java
- **JUnit 5**: 单元测试框架
- **Mockito**: Mock 框架
- **AssertJ**: 流畅断言
- **TestContainers**: 容器化集成测试
- **JaCoCo**: 覆盖率工具

### Python
- **pytest**: 测试框架
- **pytest-asyncio**: 异步测试
- **unittest.mock**: Mock 库
- **pytest-cov**: 覆盖率
- **httpx**: 异步 HTTP 测试

### 前端
- **Vitest**: 单元测试框架
- **Testing Library**: 组件测试
- **Playwright**: E2E 测试
- **MSW**: API Mock

## 关联技能
- `/implementation` - 编写可测试的代码
- `/code-review` - 审查测试质量
- `/debugging` - 调试测试失败
