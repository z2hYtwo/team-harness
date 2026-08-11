# Team Harness

## Purpose

Team Harness 是团队统一 AI 开发规范，为多 AI Agent 协作提供结构化框架。

所有 AI Agent（Claude Code、Codex CLI、Cursor、Gemini CLI 等）均应遵循本仓库中的 Rules、Skills、Workflows 与 Agents 定义。

---

## Architecture

```
team-harness/
│
├── rules/              # 编码规范
│   ├── global/        # 全局规则
│   ├── java/          # Java 规则
│   └── frontend/      # 前端规则
│
├── skills/            # 技能封装
│   ├── common/        # 通用技能
│   ├── development/   # 开发生命周期技能
│   │   ├── requirement-analysis/
│   │   ├── architecture-design/
│   │   ├── task-planning/
│   │   ├── implementation/
│   │   ├── debugging/
│   │   ├── testing/
│   │   └── code-review/
│   └── technology/    # 技术栈技能
│       ├── java/
│       ├── springboot/
│       ├── mysql/
│       └── vue/
│
├── workflows/         # 工作流编排
│   ├── feature.md    # 特性开发流程
│   ├── bugfix.md     # Bug 修复流程
│   ├── refactor.md   # 重构流程
│   ├── review.md     # 代码审查流程
│   ├── testing.md    # 测试流程
│   └── release.md    # 发布流程
│
├── agents/           # 专业 Agent 定义
│   ├── planner.md   # 规划 Agent
│   ├── coder.md     # 编码 Agent
│   ├── reviewer.md  # 审查 Agent
│   └── debugger.md  # 调试 Agent
│
├── templates/        # 标准模板
│   ├── AGENTS.md    # 项目 AI 指南
│   ├── project.md   # 项目描述
│   ├── task.md      # 任务模板
│   └── plan.md      # 实施计划
│
└── docs/            # 文档
    ├── architecture.md  # 架构文档
    └── onboarding.md   # 入门指南
```

---

## Core Concepts

### 1. Rules (规范)
全局和特定语言的编码标准，确保所有 Agent 生成的代码一致且高质量。

### 2. Skills (技能)
可复用的能力单元，Agent 可调用执行特定任务。按类型分为：
- **Common**: 通用技能
- **Development**: SDLC 阶段技能
- **Technology**: 技术栈专项技能

### 3. Workflows (工作流)
编排技能和 Agent 的完整开发流程，涵盖从需求到发布的全生命周期。

### 4. Agents (智能体)
具有特定角色和专长的 AI Agent：
- **Planner**: 战略规划和任务分解
- **Coder**: 代码实现和编写
- **Reviewer**: 质量保证和代码审查
- **Debugger**: 问题诊断和修复

### 5. Templates (模板)
标准化文档格式，确保输出一致性。

---

## Workflow

```
User Request
    ↓
Workflow Selection (选择工作流)
    ↓
Agent Orchestration (Agent 协同)
    ↓
    ├─→ Planner  ──→ 需求分析 + 任务规划
    ├─→ Coder    ──→ 代码实现
    ├─→ Reviewer ──→ 代码审查
    └─→ Debugger ──→ 问题修复
    ↓
Deliverable (交付产出)
```

---

## Usage Examples

### Feature Development (特性开发)
```bash
# 使用 feature workflow
User: "实现用户认证功能"
→ feature.md workflow
  → Planner: 分析需求，设计架构，规划任务
  → Coder: 实现代码和测试
  → Reviewer: 代码审查
  → 产出：可工作的特性 + 测试
```

### Bug Fix (Bug 修复)
```bash
# 使用 bugfix workflow
User: "修复登录超时问题"
→ bugfix.md workflow
  → Debugger: 根因分析
  → Coder: 修复实现
  → Reviewer: 修复验证
  → 产出：Bug 修复 + 回归测试
```

### Code Review (代码审查)
```bash
# 使用 review workflow
User: "审查 PR #123"
→ review.md workflow
  → Reviewer: 全面审查
  → 产出：审查意见和批准
```

---

## Design Principles

### 1. Modularity (模块化)
每个组件（规则、技能、工作流、Agent）独立且可复用。

### 2. Composability (可组合性)
组件可以不同方式组合以实现各种目标。

### 3. Specialization (专业化)
每个 Agent 有特定角色和专长领域。

### 4. Consistency (一致性)
模板和规则确保统一的输出质量。

### 5. Extensibility (可扩展性)
可添加新技能、工作流和 Agent 而不修改现有组件。

---

## Getting Started

1. **了解架构**: 阅读 [docs/architecture.md](docs/architecture.md)
2. **选择工作流**: 根据任务选择合适的 workflow
3. **调用 Agent**: 使用对应的 Agent 执行任务
4. **遵循规范**: 始终遵循 rules/ 中的编码规范
5. **使用模板**: 使用 templates/ 确保输出一致

---

## Development Principles

- **Rule First**: 规范优先
- **Reuse First**: 复用优先
- **Specification Driven**: 规格驱动
- **Agent Collaboration**: Agent 协作
- **Quality Gates**: 质量门禁
- **Least Surprise**: 最少意外
- **Keep Consistency**: 保持一致