# Team Harness Architecture

## Overview
Team Harness is a multi-agent AI development framework that orchestrates specialized agents through structured workflows to deliver high-quality software development outcomes.

## Core Concepts

### 1. Rules
Global and language-specific coding standards that all agents must follow.

**Structure**:
```
rules/
├── global/          # Universal rules
│   └── base.md
├── java/            # Java-specific rules
│   └── java-backend.md
└── frontend/        # Frontend rules
```

**Purpose**: Ensure consistency, quality, and best practices across all code generation.

### 2. Skills
Reusable capabilities that agents can invoke to perform specific tasks.

**Structure**:
```
skills/
├── common/                    # General-purpose skills
├── development/               # Development lifecycle skills
│   ├── requirement-analysis/
│   ├── architecture-design/
│   ├── task-planning/
│   ├── implementation/
│   ├── debugging/
│   ├── testing/
│   └── code-review/
└── technology/                # Technology-specific skills
    ├── java/
    ├── springboot/
    ├── mysql/
    └── vue/
```

**Categories**:
- **Common**: Cross-cutting skills (finding skills, creating skills)
- **Development**: SDLC phase-specific skills
- **Technology**: Stack-specific expertise
- **Business**: Domain-specific knowledge

### 3. Workflows
Orchestrated sequences of skills and agents for complete development processes.

**Available Workflows**:
- `feature.md`: Full feature development lifecycle
- `bugfix.md`: Systematic bug investigation and resolution
- `refactor.md`: Code quality improvement process
- `review.md`: Comprehensive code review
- `testing.md`: Multi-level testing strategy
- `release.md`: Release management and deployment

**Purpose**: Guide complex multi-step processes from start to finish.

### 4. Agents
Specialized AI agents with specific roles and expertise.

**Agent Types**:
- **Planner**: Strategic planning and task breakdown
- **Coder**: Implementation and code writing
- **Reviewer**: Quality assurance and code review
- **Debugger**: Issue diagnosis and resolution

**Properties**:
- Name and description
- Model configuration
- Responsibilities
- Output format
- Interaction style

### 5. Templates
Standardized document formats for consistency.

**Available Templates**:
- `AGENTS.md`: Project AI guide template
- `project.md`: Project description template
- `task.md`: Task specification template
- `plan.md`: Implementation plan template

## System Architecture

### Agent Orchestration Flow

```
User Request
    ↓
Workflow Selection
    ↓
Agent Coordination
    ↓
    ├─→ Planner Agent ──→ Create Plan
    ├─→ Coder Agent ───→ Implement Code
    ├─→ Reviewer Agent ─→ Review Code
    └─→ Debugger Agent ─→ Fix Issues
    ↓
Deliverable Output
```

### Component Interactions

```
┌─────────────┐
│   Rules     │◄─────┐
└─────────────┘      │
                     │ Consulted by
┌─────────────┐      │
│   Skills    │◄─────┤
└─────────────┘      │
       ▲             │
       │ Invoked by  │
       │             │
┌─────────────┐      │
│  Workflows  │──────┤
└─────────────┘      │
       ▲             │
       │ Executed by │
       │             │
┌─────────────┐      │
│   Agents    │──────┘
└─────────────┘
       ▲
       │ Instantiated by
       │
┌─────────────┐
│  Templates  │
└─────────────┘
```

## Design Principles

### 1. Modularity
Each component (rule, skill, workflow, agent) is self-contained and reusable.

### 2. Composability
Components can be combined in different ways to achieve various outcomes.

### 3. Specialization
Each agent has a specific role and expertise area.

### 4. Consistency
Templates and rules ensure uniform output quality.

### 5. Extensibility
New skills, workflows, and agents can be added without modifying existing ones.

## Usage Patterns

### Pattern 1: Feature Development
```
User: "Implement user authentication"
  → feature.md workflow
    → Planner agent (requirement analysis, task planning)
    → Coder agent (implementation)
    → Reviewer agent (code review)
    → Deliverable: Working feature with tests
```

### Pattern 2: Bug Fix
```
User: "Fix login timeout issue"
  → bugfix.md workflow
    → Debugger agent (root cause analysis)
    → Coder agent (fix implementation)
    → Reviewer agent (fix verification)
    → Deliverable: Bug fix with regression test
```

### Pattern 3: Code Review
```
User: "Review PR #123"
  → review.md workflow
    → Reviewer agent (comprehensive review)
    → Deliverable: Review comments and approval
```

## Extension Points

### Adding New Skills
1. Create skill directory under appropriate category
2. Define skill metadata and implementation
3. Document inputs, outputs, and usage
4. Add skill to relevant workflows

### Adding New Agents
1. Create agent definition in `agents/`
2. Specify model, responsibilities, and behavior
3. Define output format and interaction style
4. Update workflows to utilize new agent

### Adding New Workflows
1. Create workflow file in `workflows/`
2. Define stages and skill invocations
3. Specify entry and exit criteria
4. Document usage and examples

### Adding New Rules
1. Create rule file in appropriate category
2. Define standards and guidelines
3. Ensure rules are language/technology appropriate
4. Reference from relevant skills and agents

## Quality Gates

Each workflow includes built-in quality gates:
- **Requirements**: Clear acceptance criteria
- **Implementation**: Code standards compliance
- **Testing**: Coverage and quality thresholds
- **Review**: Peer validation
- **Deployment**: Production readiness checks

## Best Practices

### For Users
- Choose appropriate workflow for the task
- Provide clear requirements and context
- Review agent outputs and provide feedback
- Use templates for consistency

### For Agents
- Follow rules strictly
- Use skills as designed
- Produce structured output per templates
- Document decisions and trade-offs
- Ask clarifying questions when needed

### For System Maintenance
- Keep rules up to date with best practices
- Evolve skills based on usage patterns
- Refine workflows based on outcomes
- Update agent behaviors based on feedback
- Maintain template relevance

## Future Enhancements

### Planned Features
- Agent learning from past executions
- Workflow optimization based on metrics
- Dynamic agent selection
- Cross-workflow coordination
- Performance analytics and reporting

### Integration Points
- CI/CD pipeline integration
- Issue tracking system sync
- Code repository hooks
- Team collaboration tools
- Monitoring and alerting systems
