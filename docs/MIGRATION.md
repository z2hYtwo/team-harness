# Project Restructuring Guide

## Overview
This document explains the new team-harness structure and how to migrate from the old organization.

## What Changed

### Before
```
team-harness/
├── rules/
├── skills/
├── templates/
└── docs/
```

### After
```
team-harness/
├── rules/              # Unchanged - coding standards
├── skills/            # Reorganized by lifecycle phase
│   ├── common/
│   ├── development/   # NEW - SDLC phase skills
│   │   ├── requirement-analysis/
│   │   ├── architecture-design/
│   │   ├── task-planning/
│   │   ├── implementation/
│   │   ├── debugging/
│   │   ├── testing/
│   │   └── code-review/
│   └── technology/    # NEW - tech stack skills
│       ├── java/
│       ├── springboot/
│       ├── mysql/
│       └── vue/
├── workflows/         # NEW - process orchestration
├── agents/           # NEW - specialized AI agents
├── templates/        # Enhanced with more templates
└── docs/            # Updated documentation
```

## New Concepts

### 1. Workflows
**Purpose**: Define end-to-end processes that orchestrate multiple skills and agents.

**Available Workflows**:
- `feature.md` - Complete feature development
- `bugfix.md` - Bug investigation and resolution
- `refactor.md` - Code quality improvement
- `review.md` - Code review process
- `testing.md` - Comprehensive testing
- `release.md` - Release management

**Usage**:
```markdown
# In your project CLAUDE.md or instructions:
When developing a new feature, follow the feature workflow:
1. Requirement analysis
2. Architecture design
3. Task planning
4. Implementation
5. Testing
6. Code review
```

### 2. Agents
**Purpose**: Define specialized AI agents with specific roles and behaviors.

**Available Agents**:
- `planner.md` - Strategic planning, requirement analysis, task breakdown
- `coder.md` - Code implementation, following best practices
- `reviewer.md` - Code review, quality assurance
- `debugger.md` - Bug diagnosis and resolution

**Usage**:
```markdown
# When you need planning:
@planner analyze requirements and create implementation plan

# When you need implementation:
@coder implement user authentication based on the plan

# When you need review:
@reviewer review the authentication implementation

# When you need debugging:
@debugger investigate login timeout issue
```

### 3. Development Skills
**Purpose**: Provide structured approaches for each phase of the software development lifecycle.

**Organization**:
- **development/** - SDLC phase skills
  - requirement-analysis
  - architecture-design
  - task-planning
  - implementation
  - debugging
  - testing
  - code-review

- **technology/** - Tech stack specific skills
  - java
  - springboot
  - mysql
  - vue

**Migration from old skills**:
- Old business-specific skills → Keep in `skills/business/`
- Old generic skills → Move to `skills/common/` or appropriate development phase

## Migration Steps

### For Existing Projects

1. **Update CLAUDE.md references**:
```markdown
# Before
Follow skills in skills/ directory

# After
Follow the workflow appropriate for the task:
- New feature: workflows/feature.md
- Bug fix: workflows/bugfix.md
- Refactoring: workflows/refactor.md

Use specialized agents for specific tasks:
- Planning: agents/planner.md
- Implementation: agents/coder.md
- Review: agents/reviewer.md
- Debugging: agents/debugger.md
```

2. **Update skill references**:
```markdown
# Before
Use skills/[skill-name]

# After
Use skills/development/[phase-name]/
or skills/technology/[tech-name]/
```

3. **Adopt workflows**:
- Choose the appropriate workflow for your task
- Follow its stages in order
- Use the skills and agents it recommends

### For New Projects

1. **Start with templates**:
   - Copy `templates/AGENTS.md` to your project root
   - Fill in project-specific information

2. **Reference workflows**:
   - Link to relevant workflows in your project docs
   - Customize stages if needed

3. **Specify agents**:
   - Define which agents should handle which tasks
   - Use agent definitions as guidelines

## Usage Examples

### Example 1: New Feature Development

**Old approach**:
```
1. Manually plan implementation
2. Write code
3. Test
4. Review
```

**New approach**:
```markdown
Follow workflows/feature.md:

Stage 1: Requirement Analysis
- Invoke: skills/development/requirement-analysis/
- Agent: planner
- Output: Requirements with acceptance criteria

Stage 2: Architecture Design
- Invoke: skills/development/architecture-design/
- Agent: planner
- Output: Technical design

Stage 3: Task Planning
- Invoke: skills/development/task-planning/
- Agent: planner
- Output: Task breakdown with estimates

Stage 4: Implementation
- Invoke: skills/development/implementation/
- Agent: coder
- Output: Code + tests

Stage 5: Testing
- Invoke: skills/development/testing/
- Agent: coder
- Output: Test suite

Stage 6: Code Review
- Invoke: skills/development/code-review/
- Agent: reviewer
- Output: Review feedback
```

### Example 2: Bug Fix

**Old approach**:
```
1. Investigate bug
2. Fix code
3. Test fix
```

**New approach**:
```markdown
Follow workflows/bugfix.md:

Stage 1: Bug Investigation
- Invoke: skills/development/debugging/
- Agent: debugger
- Output: Root cause analysis

Stage 2: Fix Implementation
- Invoke: skills/development/implementation/
- Agent: coder
- Output: Fix + regression test

Stage 3: Verification
- Invoke: skills/development/testing/
- Agent: coder
- Output: Verified fix

Stage 4: Review
- Invoke: skills/development/code-review/
- Agent: reviewer
- Output: Approval
```

## Best Practices

### 1. Always Use Workflows
Don't skip stages - workflows ensure quality and completeness.

### 2. Match Agent to Task
Use the right agent for the right task:
- **Planning** → planner
- **Coding** → coder
- **Review** → reviewer
- **Debugging** → debugger

### 3. Follow Skill Structure
Each skill provides:
- Purpose
- Process
- Input/Output format
- Examples

Use them consistently.

### 4. Leverage Templates
Use templates for:
- Project documentation (AGENTS.md)
- Task specifications (task.md)
- Implementation plans (plan.md)

### 5. Maintain Consistency
- Follow rules/ standards
- Use standard workflows
- Apply consistent agent behaviors
- Format output per templates

## FAQ

### Q: Do I need to use all agents for every task?
A: No. Use only the agents appropriate for your task. Small tasks might only need one agent.

### Q: Can I customize workflows?
A: Yes. Workflows are guidelines. Adapt them to your project needs while maintaining the quality gates.

### Q: Where do project-specific skills go?
A: Keep them in `skills/business/[project-name]/` or create `skills/project/[project-name]/`

### Q: Should I update existing skills?
A: Not immediately. Old skills still work. Migrate gradually as you refactor.

### Q: How do I choose between workflows?
A: Match the workflow to your task type:
- feature.md → New functionality
- bugfix.md → Fixing issues
- refactor.md → Improving code
- review.md → Reviewing code
- testing.md → Writing tests
- release.md → Deploying

## Rollout Plan

### Phase 1: Immediate (Week 1)
- [x] Create new directory structure
- [x] Add workflow definitions
- [x] Add agent definitions
- [x] Create development skills
- [x] Update documentation

### Phase 2: Adoption (Week 2-3)
- [ ] Update project CLAUDE.md files
- [ ] Train team on new structure
- [ ] Migrate critical skills
- [ ] Document custom workflows

### Phase 3: Optimization (Week 4+)
- [ ] Gather feedback
- [ ] Refine workflows
- [ ] Add technology skills
- [ ] Expand agent capabilities

## Support

For questions or issues with the new structure:
1. Review docs/architecture.md
2. Check relevant workflow/agent/skill documentation
3. Refer to this migration guide
4. Ask team lead or architect
