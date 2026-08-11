---
name: smartexam-spec-documents
overview: 为 SmartExam 在线智能题库实训平台创建完整的项目规范文档，包括项目说明、功能规格、架构设计、数据库设计和 AI 开发指南，基于用户提供的详细需求文档。
todos:
  - id: create-readme
    content: 创建 SmartExam/README.md 项目概述文档，包含项目简介、业务目标、用户角色、技术栈、分阶段开发计划及风险分析
    status: completed
  - id: create-spec
    content: 创建 SmartExam/docs/spec.md 功能规格说明，详细描述三类用户角色、六大功能模块及二十五项子功能
    status: completed
    dependencies:
      - create-readme
  - id: create-architecture
    content: 创建 SmartExam/docs/architecture.md 系统架构设计文档，包含前后端分层架构、模块划分、API设计规范及安全设计
    status: completed
    dependencies:
      - create-readme
  - id: create-database
    content: 创建 SmartExam/docs/database.md 数据库设计文档，包含ER图、核心表结构、索引设计及数据字典
    status: completed
    dependencies:
      - create-spec
      - create-architecture
  - id: create-agents
    content: 创建 SmartExam/AGENTS.md AI 开发指南，定义项目编码规范、开发流程与开发原则
    status: completed
    dependencies:
      - create-architecture
---

## 项目概述
为 SmartExam 在线智能题库实训平台创建全套项目规范文档，基于用户提供的需求文档，遵循 team-harness 仓库现有模板规范，产出 5 份标准化文档。

## 核心目标
- 创建项目概述文档，定义项目背景、业务目标与技术栈
- 创建功能规格说明，详细描述三类用户角色及六大功能模块
- 创建系统架构设计，明确前后端分层架构与模块划分
- 创建数据库设计文档，定义核心表结构与关系
- 创建 AI 开发指南，确保后续 AI 辅助开发遵循团队规范

## 技术方案

### 文档技术栈
- 文档格式：Markdown
- 模板参考：`templates/project.md`、`templates/AGENTS.md`
- 规范参考：`rules/java/java-backend.md`、`rules/global/base.md`

### 项目目标技术栈（文档中定义）
- 后端：SpringBoot 2.7+ / 3.x + MySQL 8.0 + MyBatis-Plus 3.5+
- 前端：Vue 3 + TypeScript + Element Plus
- 认证：JWT（Spring Security）
- 缓存：Redis（可选，用于会话和热点数据）
- 文件存储：本地存储 / OSS（题目导入导出）

### 文档结构设计

遵循 team-harness 现有模板规范，每份文档结构如下：

**1. README.md**（项目概述）
```
# SmartExam — 在线智能题库实训平台
## Overview — 项目简介
## Business — 业务目标、核心流程、用户角色
## Technology — 技术栈明细
## Architecture — 系统架构概览
## Development — 分阶段开发计划
## Risks — 技术风险与业务风险
```

**2. docs/spec.md**（功能规格说明）
```
# SmartExam 功能规格说明
## 用户角色定义 — 管理员、教师、学生权限矩阵
## 功能模块详述 — 6大模块、25项子功能的详细描述
## 业务流程 — 核心业务流程图（文字描述）
## 非功能性需求 — 性能、安全、可用性
```

**3. docs/architecture.md**（系统架构设计）
```
# SmartExam 系统架构设计
## 系统架构概览 — 前后端分离架构图
## 后端分层架构 — Controller → Service → Mapper → Entity
## 模块划分 — 各业务模块及依赖关系
## 接口设计规范 — RESTful API 设计原则
## 安全设计 — JWT认证、RBAC权限模型
## 部署架构 — 开发/测试/生产环境
```

**4. docs/database.md**（数据库设计）
```
# SmartExam 数据库设计
## 数据库选型 — MySQL 8.0
## ER 图 — 核心实体关系
## 核心表结构 — 用户表、题库表、试卷表、考试记录表、错题表等
## 索引设计 — 关键查询索引
## 数据字典 — 枚举值说明
```

**5. AGENTS.md**（AI 开发指南）
```
# SmartExam AI 开发指南
## Project — 项目基本信息
## Architecture — 系统架构说明
## Coding Rules — 遵循的编码规范
## Workflow — 开发流程
## Principles — 开发原则
```

### 分阶段开发计划（文档中定义）
- 第一阶段：用户权限基础 + 用户管理模块
- 第二阶段：核心题库管理模块
- 第三阶段：试卷与考试实训模块
- 第四阶段：学习与学情统计模块
- 第五阶段：系统运维辅助模块 + 智能审题/智能组卷 AI 功能
