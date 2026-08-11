# Global Rules

## Purpose

所有项目默认加载。

---

## Coding

保持简单

保持一致

避免重复

禁止过度设计

---

## Architecture

高内聚

低耦合

SOLID

DRY

KISS

---

## Security

默认安全

最小权限

输入校验

输出转义

敏感信息保护

---

## Performance

避免重复计算

分页

缓存

连接池

资源释放

---

## Testing

新增功能必须可测试。

---

## Documentation

重要设计必须记录。

---

## Workflow

所有功能开发严格遵循以下流程，不可跳过或打乱顺序：

1. **需求分析** — 明确用户需求、业务流程、功能边界，产出功能规格说明
2. **数据库设计** — 设计ER图、表结构、索引、数据字典，产出DDL脚本
3. **接口设计** — 定义RESTful API接口、请求/响应格式、状态码，产出接口文档
4. **后端开发** — 基于接口文档实现Controller/Service/Mapper层，完成后端单元测试
5. **前端开发** — 基于接口文档实现页面、组件、路由、状态管理，完成前端自测
6. **联调** — 前后端对接联调，解决跨域、数据格式、业务流程问题
7. **测试** — 功能测试、集成测试、回归测试，修复缺陷后完成交付

每个阶段完成后必须经过评审确认，方可进入下一阶段。

---

## Principles

Production Ready

Specification Driven

Code over Comments