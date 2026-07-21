# Java Backend Rules

## Purpose

定义团队 Java 后端开发规范，确保代码具备一致性、可维护性、可测试性和生产可用性。

所有 Java 后端项目默认遵循本规范。

---

## Scope

适用于：

- Java 17+
- Spring Boot
- REST API
- 微服务
- 单体应用

---

## Coding

- 使用具有业务语义的命名。
- 方法职责单一，避免超长方法。
- 优先组合而非继承。
- 避免魔法值，使用常量或枚举。
- 禁止重复代码，优先复用已有能力。
- 优先不可变对象（Immutable）。

---

## Architecture

遵循项目既有架构。

推荐分层：

- Controller
- Service
- Repository
- Domain
- DTO
- VO

保持：

- 高内聚
- 低耦合
- 单向依赖

禁止跨层直接调用。

---

## Spring

- 使用构造器注入。
- 避免字段注入（Field Injection）。
- 合理划分 Bean 生命周期。
- Service 保持无状态（Stateless）。
- Controller 不处理业务逻辑。

---

## Database

- 使用参数化查询。
- 避免 N+1 查询。
- 合理设计索引。
- 大数据量必须分页。
- 明确事务边界。
- 避免长事务。

---

## Exception

- 使用统一异常处理。
- 自定义业务异常。
- 禁止空 catch。
- 禁止吞异常。

---

## Logging

使用统一日志框架。

日志应：

- 可检索
- 可追踪
- 可定位问题

禁止记录：

- 密码
- Token
- Secret
- 用户敏感信息

---

## Security

默认遵循最小权限原则。

检查：

- 输入校验
- 权限控制
- SQL 注入
- XSS
- CSRF
- JWT 安全
- 敏感信息保护

---

## Performance

关注：

- 时间复杂度
- 内存占用
- SQL 性能
- IO 操作
- 批量处理
- 缓存策略

避免重复计算和资源浪费。

---

## Testing

新增功能应具备：

- 单元测试
- 边界测试
- 异常测试

核心业务建议增加集成测试。

---

## Principles

- Production Ready
- Readability First
- Reuse First
- Security by Default
- Performance Awareness
- Keep It Simple