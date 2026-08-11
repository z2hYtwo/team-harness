# 项目重构迁移指南

## 概述
本文档说明 team-harness 新结构及从旧组织方式的迁移路径。

## 变更内容

### 之前
```
team-harness/
├── rules/
├── skills/
├── templates/
└── docs/
```

### 之后
```
team-harness/
├── rules/              # 不变 - 编码规范
├── skills/            # 按生命周期阶段重组
│   ├── common/
│   ├── development/   # 新增 - SDLC 阶段技能
│   │   ├── requirement-analysis/
│   │   ├── architecture-design/
│   │   ├── task-planning/
│   │   ├── implementation/
│   │   ├── debugging/
│   │   ├── testing/
│   │   └── code-review/
│   └── technology/    # 新增 - 技术栈技能
│       ├── java/
│       ├── springboot/
│       ├── mysql/
│       └── vue/
├── workflows/         # 新增 - 流程编排
├── agents/           # 新增 - 专业 AI Agent
├── templates/        # 增强 - 更多模板
└── docs/            # 更新 - 文档
```

## 新概念

### 1. 工作流 (Workflows)
**用途**: 定义编排多个技能和 Agent 的端到端流程。

**可用工作流**:
- `feature.md` - 完整特性开发
- `bugfix.md` - Bug 调查和修复
- `refactor.md` - 代码质量改善
- `review.md` - 代码审查流程
- `testing.md` - 全面测试
- `release.md` - 发布管理

**使用方式**:
```markdown
# 在项目 CLAUDE.md 或指令中:
开发新特性时，遵循 feature 工作流:
1. 需求分析
2. 架构设计
3. 任务规划
4. 编码实现
5. 测试
6. 代码审查
```

### 2. Agent (智能体)
**用途**: 定义具有特定角色和行为的专业 AI Agent。

**可用 Agent**:
- `planner.md` - 战略规划, 需求分析, 任务分解
- `coder.md` - 代码实现, 遵循最佳实践
- `reviewer.md` - 代码审查, 质量保障
- `debugger.md` - Bug 诊断和修复

**使用方式**:
```markdown
# 需要规划时:
@planner 分析需求并创建实施计划

# 需要实现时:
@coder 基于计划实现用户认证

# 需要审查时:
@reviewer 审查认证实现

# 需要调试时:
@debugger 调查登录超时问题
```

### 3. 开发技能 (Development Skills)
**用途**: 为软件开发生命周期的每个阶段提供结构化方法。

**组织**:
- **development/** - SDLC 阶段技能
  - requirement-analysis (需求分析)
  - architecture-design (架构设计)
  - task-planning (任务规划)
  - implementation (编码实现)
  - debugging (调试)
  - testing (测试)
  - code-review (代码审查)

- **technology/** - 技术栈技能
  - java
  - springboot
  - mysql
  - vue

## 迁移步骤

### 对现有项目

1. **更新 CLAUDE.md 引用**:
```markdown
# 之前
遵循 skills/ 目录中的技能

# 之后
根据任务选择合适的工作流:
- 新特性: workflows/feature.md
- Bug 修复: workflows/bugfix.md
- 重构: workflows/refactor.md

使用专业 Agent 处理特定任务:
- 规划: agents/planner.md
- 实现: agents/coder.md
- 审查: agents/reviewer.md
- 调试: agents/debugger.md
```

2. **更新技能引用**:
```markdown
# 之前
使用 skills/[技能名称]

# 之后
使用 skills/development/[阶段名称]/
或 skills/technology/[技术名称]/
```

3. **采用工作流**:
- 为任务选择合适的工作流
- 按顺序执行各阶段
- 使用工作流推荐的技能和 Agent

### 对新项目

1. **使用模板**:
   - 复制 `templates/AGENTS.md` 到项目根目录
   - 填写项目特定信息

2. **引用工作流**:
   - 在项目文档中链接相关工作流
   - 按需定制阶段

3. **指定 Agent**:
   - 定义哪个 Agent 处理哪类任务
   - 使用 Agent 定义作为指导

## 使用示例

### 示例 1: 新特性开发

**旧方式**:
```
1. 手动规划实现
2. 编写代码
3. 测试
4. 审查
```

**新方式**:
```markdown
遵循 workflows/feature.md:

阶段 1: 需求分析
- 调用: skills/development/requirement-analysis/
- Agent: planner
- 产出: 带验收标准的需求

阶段 2: 架构设计
- 调用: skills/development/architecture-design/
- Agent: planner
- 产出: 技术设计

阶段 3: 任务规划
- 调用: skills/development/task-planning/
- Agent: planner
- 产出: 带估算的任务分解

阶段 4: 编码实现
- 调用: skills/development/implementation/
- Agent: coder
- 产出: 代码 + 测试

阶段 5: 测试
- 调用: skills/development/testing/
- Agent: coder
- 产出: 测试套件

阶段 6: 代码审查
- 调用: skills/development/code-review/
- Agent: reviewer
- 产出: 审查反馈
```

## 最佳实践

### 1. 始终使用工作流
不要跳过阶段 - 工作流确保质量和完整性。

### 2. 任务匹配 Agent
使用正确的 Agent 做正确的事:
- **规划** → planner
- **编码** → coder
- **审查** → reviewer
- **调试** → debugger

### 3. 遵循技能结构
每个技能提供:
- 目的
- 流程
- 输入/输出格式
- 示例

一致地使用它们。

### 4. 利用模板
使用模板确保:
- 项目文档 (AGENTS.md)
- 任务规格 (task.md)
- 实施计划 (plan.md)

### 5. 保持一致性
- 遵循 rules/ 规范
- 使用标准工作流
- 应用一致的 Agent 行为
- 按模板格式化输出

## 常见问题

### 问: 每个任务都要用所有 Agent 吗？
答: 不需要。只使用任务需要的 Agent。小任务可能只需要一个 Agent。

### 问: 可以自定义工作流吗？
答: 可以。工作流是指导方针。根据项目需要调整，同时保持质量门禁。

### 问: 项目特定的技能放哪里？
答: 放在 `skills/business/[项目名]/`。

### 问: 需要更新现有技能吗？
答: 不急。旧技能仍然有效。重构时逐步迁移。

### 问: 怎么选择工作流？
答: 按任务类型匹配:
- feature.md → 新功能
- bugfix.md → 修复问题
- refactor.md → 改善代码
- review.md → 审查代码
- testing.md → 编写测试
- release.md → 部署发布
