# good7ob：主流开发方式与 AI Agent 开发流设计

## 1. 文档目的

本文整理当前主流软件开发方式，并重点分析适合 AI Agent 的开发流程设计。

对于 good7ob，不建议仅把传统 Scrum、Kanban 或 DevOps 原样搬入系统，而是建议构建一套面向 AI Agent 的开发流：

> **Spec-Driven Agentic Development**

即：

**需求 / 规格驱动 + AI Agent 执行 + 自动验证 + Gate 控制 + DevOps 闭环**

---

# 2. 当前主流开发方式

## 2.1 Waterfall 瀑布开发

典型流程：

```text
需求
 ↓
概要设计
 ↓
详细设计
 ↓
开发
 ↓
测试
 ↓
发布
```

### 特点

- 阶段边界清晰
- 文档完整
- 适合固定需求
- 变更成本较高
- 适合大型企业、金融、政府项目

### AI Agent 适配度

**★★★☆☆**

优点：

- 文档结构明确
- 输入输出清晰
- AI 容易根据设计文档执行

缺点：

- 灵活性不足
- 对持续迭代支持较弱

---

## 2.2 Agile 敏捷开发

核心理念：

- 小步迭代
- 快速反馈
- 持续交付
- 接受需求变化

典型流程：

```text
需求
 ↓
迭代
 ↓
开发
 ↓
测试
 ↓
反馈
 ↓
下一轮迭代
```

### AI Agent 适配度

**★★★★☆**

AI 可以很好地处理：

- 小 Task
- Bug Fix
- 重构
- 自动测试
- 文档生成

---

## 2.3 Scrum

典型对象：

```text
Product Backlog
 ↓
Sprint Backlog
 ↓
Sprint
 ↓
Review
 ↓
Retrospective
```

适合：

- 中小型开发团队
- 产品持续迭代

### AI Agent 适配度

**★★★★☆**

问题在于：

Scrum 本身是围绕人类团队设计的。

例如：

- Daily Scrum
- Sprint Planning
- Story Point
- 人工估时

AI Agent 出现以后，其中部分机制需要重新设计。

---

## 2.4 Kanban

典型状态：

```text
Todo
 ↓
In Progress
 ↓
Review
 ↓
Done
```

核心是：

- 可视化工作流
- WIP 限制
- 持续流动

### AI Agent 适配度

**★★★★☆**

非常适合 AI Agent Task 管理。

例如：

```text
Ready
 ↓
Assigned to Agent
 ↓
Running
 ↓
Verifying
 ↓
Review
 ↓
Done
```

---

# 3. DevOps

DevOps 关注：

```text
Plan
 ↓
Code
 ↓
Build
 ↓
Test
 ↓
Release
 ↓
Deploy
 ↓
Operate
 ↓
Monitor
```

核心目标：

- 开发与运维一体化
- 自动化
- CI/CD
- 持续反馈

### AI Agent 适配度

**★★★★★**

AI Agent 可以介入：

- 代码生成
- CI 修复
- 测试生成
- Release Note
- 部署
- 云资源管理
- 日志分析
- 事故分析

---

# 4. TDD

Test Driven Development：

```text
Test
 ↓
Code
 ↓
Refactor
```

基本循环：

```text
Red
 ↓
Green
 ↓
Refactor
```

AI Agent 非常适合 TDD。

原因：

Agent 如果只有：

```text
“完成这个功能”
```

很难判断什么时候真正完成。

但是如果存在 Test：

```text
Test Passed = Task Completed
```

Agent 就拥有了明确的完成条件。

### AI Agent 适配度

**★★★★★**

---

# 5. BDD

Behavior Driven Development 使用行为描述定义系统应该如何工作。

例如：

```gherkin
Given 用户未登录
When 用户访问订单页面
Then 系统跳转登录页面
```

BDD 对 AI 很友好，因为：

- 语义明确
- 接近自然语言
- 可转换测试
- 可直接作为验收标准

### AI Agent 适配度

**★★★★★**

---

# 6. Spec-Driven Development

Spec-Driven Development 的核心是：

> 先定义 Specification，再让 AI 执行。

传统 AI Coding：

```text
Prompt
 ↓
AI
 ↓
Code
```

推荐方式：

```text
Requirement
 ↓
Specification
 ↓
Plan
 ↓
Task
 ↓
AI
 ↓
Code
 ↓
Verification
```

这里的关键变化是：

**Specification 成为开发的中心资产。**

Specification 可以包括：

- 功能要求
- UI 行为
- API
- DB
- Constraints
- Acceptance Criteria
- Test Case
- Security Requirement
- Performance Requirement

---

# 7. Agentic Development

传统开发：

```text
Task
 ↓
Developer
 ↓
Code
```

Agentic Development：

```text
Goal
 ↓
Agent
 ↓
Planning
 ↓
Execution
 ↓
Verification
 ↓
Correction
 ↓
Complete
```

Agent 不只是执行单一步骤。

而是可以：

1. 分析任务
2. 制定计划
3. 修改代码
4. 执行测试
5. 分析失败
6. 自动修正
7. 提交成果物

---

# 8. 推荐模式：Spec-Driven Agentic Development

对于 good7ob，建议采用：

> **Spec-Driven Agentic Development**

整体流程：

```text
Idea
 ↓
Requirement
 ↓
Specification
 ↓
Plan
 ↓
Task
 ↓
Agent Execution
 ↓
Test / Verify
 ↓
Review
 ↓
PR
 ↓
CI/CD
 ↓
Deploy
 ↓
Monitor
 ↓
Feedback
 ↓
Requirement
```

核心思想：

```text
Human
负责
目标 / 约束 / 决策 / 审批

AI Agent
负责
分析 / 计划 / 执行 / 验证 / 修正
```

---

# 9. good7ob 推荐开发模型

结合 good7ob 当前结构：

```text
Product
 │
 ├─ Idea
 │
 ├─ FP
 │   └─ RP
 │
 ├─ UI Design
 │
 ├─ System Design
 │   ├─ Architecture
 │   ├─ DB
 │   ├─ API
 │   └─ Detailed Design
 │
Project
 │
 ├─ Task
 │   ├─ Subtask
 │   ├─ Dependency
 │   └─ Chain
 │
 ├─ Human
 └─ AI Employee
```

建议增加：

```text
Specification
```

最终变成：

```text
                 Product
                    │
                 Idea
                    │
                   FP
                    │
                   RP
                    │
             Specification
                    │
          ┌─────────┴─────────┐
          │                   │
      UI Design          System Design
                             │
               ┌─────────────┼─────────────┐
               │             │             │
             Arch           API            DB
               └─────────────┼─────────────┘
                             │
                          Project
                             │
                            Plan
                             │
                          Task DAG
                             │
               ┌─────────────┴─────────────┐
               │                           │
          Human Task                  Agent Task
                                           │
                                         Agent
                                           │
                         ┌─────────────────┼───────────────┐
                         │                 │               │
                       Code              Test            Docs
                         │                 │               │
                         └─────────────────┼───────────────┘
                                           │
                                        Verify
                                           │
                                          Gate
                                           │
                                           PR
                                           │
                                         CI/CD
                                           │
                                         Deploy
                                           │
                                        Monitor
                                           │
                                        Feedback
```

---

# 10. Agent Task：从普通 Task 升级为 Executable Task

传统 Task：

```text
实现用户登录功能
```

对于 Agent 来说信息不足。

建议设计：

```text
Task
├── Goal
├── Context
├── Input
├── Specification
├── Constraints
├── Repository
├── Branch
├── Environment
├── Tools
├── Permissions
├── Expected Output
├── Acceptance Criteria
├── Test Cases
├── Dependencies
└── Completion Conditions
```

示例：

```text
TASK-1024

Goal
实现用户注册 API

Requirement
RP-102

Repository
good7ob-api

Branch
feature/TASK-1024

Specification
API-034

Database
TABLE-user

Constraints
Java 21
Spring Boot
PostgreSQL

禁止修改现有认证接口

Acceptance Criteria

POST /users

成功：
201

重复 Email：
409

非法 Email：
400

Test
TC-101
TC-102
TC-103

Expected Output

- Source Code
- Unit Test
- API Documentation
- Migration Script
```

---

# 11. Agent Role 设计

不建议只有一个通用 AI Agent。

可以定义多个角色：

```text
Planner Agent
Architect Agent
Developer Agent
Test Agent
Reviewer Agent
Security Agent
Release Agent
Monitor Agent
```

---

## 11.1 Planner Agent

职责：

- 读取 RP
- 读取 Spec
- 生成开发计划
- 拆分 Task
- 创建依赖关系

输出：

```text
Development Plan
Task DAG
Estimate
Risk
```

---

## 11.2 Architect Agent

职责：

- 架构设计
- API 设计
- DB 设计
- 技术方案分析

输出：

```text
Architecture
API Spec
DB Spec
Technical Decision
```

---

## 11.3 Developer Agent

职责：

- 编写代码
- 修改代码
- 重构
- 编写单元测试

输出：

```text
Code
Unit Test
Commit
PR
```

---

## 11.4 Test Agent

职责：

- 生成 Test Case
- 执行测试
- E2E
- Regression Test

输出：

```text
Test Result
Bug
Quality Report
```

---

## 11.5 Reviewer Agent

职责：

- Code Review
- Architecture Review
- Spec Compliance

---

## 11.6 Security Agent

负责：

- Dependency Scan
- Secret Scan
- Vulnerability Scan
- Permission Review

---

## 11.7 Release Agent

负责：

```text
Build
Release
Deploy
Rollback
Release Note
```

---

## 11.8 Monitor Agent

负责：

```text
Monitoring
Log Analysis
Cost Analysis
Incident Detection
```

---

# 12. Task Chain 升级为 Task DAG

简单 Chain：

```text
A
 ↓
B
 ↓
C
 ↓
D
```

对于 AI Agent 来说不够。

建议支持 DAG：

```text
                 RP
                  │
               Planning
                  │
        ┌─────────┴─────────┐
        │                   │
    DB Design           UI Design
        │                   │
        ↓                   ↓
   Backend API          Frontend
        │                   │
        └─────────┬─────────┘
                  │
             Integration
                  │
             E2E Testing
                  │
              Review
                  │
              Release
```

优点：

- 支持并行执行
- Agent 可以同时工作
- 明确依赖
- 自动调度
- 自动判断 Block

---

# 13. Task DAG 建议数据模型

```text
task

task_dependency

task_execution

task_artifact

task_verification

task_agent_assignment
```

例如：

```text
TASK-A
   ↓
TASK-C

TASK-B
   ↓
TASK-C
```

表示：

```text
C depends on A + B
```

当：

```text
A = Done
B = Done
```

系统自动：

```text
C = Ready
```

---

# 14. Gate 机制

AI Agent 最大的问题不是：

```text
能不能执行
```

而是：

```text
什么时候允许继续执行
```

因此建议 good7ob 增加：

```text
Gate
```

---

## 14.1 Gate 类型

建议支持：

```text
AUTO
AI_REVIEW
HUMAN_REVIEW
HUMAN_APPROVAL
```

---

## 14.2 开发流程 Gate

例如：

```text
Requirement
     │
     ↓
Requirement Gate
     │
     ↓
Design
     │
     ↓
Design Gate
     │
     ↓
Development
     │
     ↓
Test Gate
     │
     ↓
Security Gate
     │
     ↓
Release Gate
```

---

# 15. MVP Gate 示例

MVP 阶段可以简化为：

```text
Requirement
   ↓
Human Approval
   ↓
Development
   ↓
AI Review
   ↓
Test
   ↓
Auto Gate
   ↓
PR
   ↓
Human Approval
   ↓
Production
```

这样既能自动化，又能控制风险。

---

# 16. Agent 执行循环

Agent 执行应该不是一次 Prompt。

推荐：

```text
Observe
 ↓
Think / Plan
 ↓
Act
 ↓
Verify
 ↓
Result?
 ├─ Fail → Retry / Replan
 └─ Pass → Complete
```

具体：

```text
Task
 ↓
Load Context
 ↓
Generate Plan
 ↓
Execute
 ↓
Run Test
 ↓
Analyze Result
 ↓
Failed?
 ├─ YES
 │   ↓
 │ Fix
 │   ↓
 │ Retry
 │
 └─ NO
     ↓
   Complete
```

---

# 17. Agent 核心对象

good7ob 如果想真正支持 Agentic Development，建议引入以下核心对象：

```text
Specification
Task
Task DAG
Agent
Skill
Tool
Permission
Artifact
Verification
Gate
Execution
Log
Metric
```

---

# 18. Specification

作用：

```text
AI 理解“要做什么”
```

内容：

- Requirement
- Business Rule
- UI Rule
- API
- DB
- Acceptance Criteria
- Test
- Constraint

---

# 19. Skill

Skill 表示：

```text
Agent 怎么做
```

例如：

```text
java-spring-development
react-development
db-migration
api-testing
aws-deployment
code-review
```

未来可以形成：

```text
Skill Marketplace
```

---

# 20. Tool

Tool 表示 Agent 可以调用什么。

例如：

```text
Git
GitHub
IDE
Terminal
Docker
AWS CLI
Database
Browser
Test Runner
```

---

# 21. Permission

决定 Agent 可以执行什么操作。

例如：

```text
Repository

read
write
merge

AWS

read
deploy
delete

Database

read
migration
write
```

权限可以与 good7ob 现有的临时授权机制结合。

---

# 22. Artifact

AI 执行产生的所有成果物建议统一管理为 Artifact。

例如：

```text
Code
Document
Diagram
API Spec
DB Migration
Test Case
Test Report
Build
Docker Image
Deployment
```

结构：

```text
Task
 ↓
Execution
 ↓
Artifact
```

---

# 23. Verification

Verification 回答：

```text
AI 做得对不对？
```

可以包括：

```text
Unit Test
Integration Test
E2E
Lint
Build
Security Scan
AI Review
Human Review
```

---

# 24. Log

Agent 应该记录：

```text
执行了什么
什么时候执行
调用了什么 Tool
修改了哪些文件
执行了哪些命令
使用了哪个模型
```

用途：

- 审计
- Debug
- Agent 学习
- 成本分析

---

# 25. Metric

建议记录：

```text
Execution Time

Token
├── Input Token
└── Output Token

Cost

Retry Count

Test Pass Rate

Task Success Rate

Human Intervention Count
```

这些数据以后可以用于：

```text
Agent Performance
Agent Cost
Agent Quality
Estimate
```

---

# 26. good7ob Agentic Development Flow

建议正式定义：

# ADF
## Agentic Development Flow

核心流程：

```text
Idea
 ↓
FP
 ↓
RP
 ↓
Specification
 ↓
Plan
 ↓
Task DAG
 ↓
Agent Assignment
 ↓
Execute
 ↓
Artifact
 ↓
Verify
 ↓
Gate
 ↓
Merge
 ↓
Release
 ↓
Monitor
 ↓
Feedback
```

---

# 27. 各对象的职责

```text
Specification
AI 理解什么要做

Task DAG
AI 按什么顺序做

Agent
谁来做

Skill
怎么做

Tool
使用什么工具

Permission
允许做什么

Artifact
产生了什么

Verification
是否正确

Gate
是否允许下一步

Log
执行了什么

Metric
花了多少时间 / Token / 成本
```

---

# 28. Human 与 AI 的职责重新分配

传统模式：

```text
Human

需求
设计
开发
测试
发布
运维
```

Agent 模式：

```text
Human

Goal
Constraint
Approval
Decision
Acceptance
```

AI：

```text
Analyze
Plan
Design
Code
Test
Review
Deploy
Monitor
```

最终：

```text
Human
负责决策

Agent
负责执行
```

---

# 29. MVP 阶段建议

第一阶段不要做完全 Autonomous Agent。

建议：

```text
Human
 ↓
Requirement
 ↓
Specification
 ↓
Task
 ↓
Assign Agent
 ↓
Agent Coding
 ↓
Auto Test
 ↓
AI Review
 ↓
Human Review
 ↓
Merge
```

需要实现：

- Specification
- Agent Task
- Agent Assignment
- Execution Log
- Artifact
- Test Result
- Human Gate

---

# 30. Growth 阶段

增加：

```text
Planner Agent
Task DAG
Parallel Agent
Retry
Agent Review
Security Agent
Cost Tracking
```

流程：

```text
RP
 ↓
Planner
 ↓
Task DAG
 ↓
Multiple Agents
 ↓
Verification
 ↓
Human Gate
```

---

# 31. Mature 阶段

最终：

```text
Requirement
 ↓
AI Planning
 ↓
AI Architecture
 ↓
AI Development
 ↓
AI Testing
 ↓
AI Review
 ↓
AI Security
 ↓
Human Approval
 ↓
Release Agent
 ↓
Monitor Agent
 ↓
Feedback
```

人类只在关键 Gate 参与。

---

# 32. good7ob 与传统项目管理工具的差异

传统：

```text
Jira

Issue
Task
Sprint
Kanban
```

Agent 开发平台：

```text
good7ob

Requirement
Specification
Task DAG
Agent
Skill
Execution
Artifact
Verification
Gate
Metric
```

核心差异：

传统工具解决：

```text
人如何管理工作
```

good7ob 可以解决：

```text
人如何管理 AI + 人共同完成软件开发
```

---

# 33. 产品定位建议

未来 good7ob 不应该简单定位为：

```text
Project Management Tool
```

也不只是：

```text
AI Coding Platform
```

可以定位为：

# AI Software Development Operating System

或：

# Agentic Software Engineering Platform

核心能力：

```text
Requirement
Design
Planning
Task
Agent
Development
Testing
Release
Operations
```

形成完整的软件生命周期。

---

# 34. 推荐最终模型

```text
                    Product
                       │
                     Idea
                       │
                      FP
                       │
                      RP
                       │
                Specification
                       │
                    Project
                       │
                     Plan
                       │
                   Task DAG
                       │
             Agent Orchestration
                       │
        ┌──────────────┼──────────────┐
        │              │              │
    Developer        Tester        Reviewer
      Agent           Agent          Agent
        │              │              │
        └──────────────┼──────────────┘
                       │
                   Artifact
                       │
                 Verification
                       │
                     Gate
                       │
                     Merge
                       │
                    Release
                       │
                    Deploy
                       │
                    Monitor
                       │
                   Feedback
                       │
                       └────────→ RP
```

---

# 35. 结论

对于 good7ob，最值得采用的不是单纯：

- Scrum
- Kanban
- Waterfall

而是融合：

```text
Spec-Driven Development
+
Agentic Development
+
TDD / BDD
+
DevOps
```

形成：

# Spec-Driven Agentic Development

开发核心从：

```text
人执行 Task
```

变成：

```text
Specification
 ↓
Task DAG
 ↓
Agent
 ↓
Artifact
 ↓
Verification
 ↓
Gate
```

这套模型可以成为 good7ob 未来 AI 辅助开发、AI 员工、任务调度、测试管理、发布管理和运营管理之间的统一主线。
