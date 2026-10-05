# good7ob 关系型数据库与 NoSQL 数据库选型方案

## 1. 结论

good7ob 不适合把核心数据库整体改造成 NoSQL。

推荐原则：

> **关系型数据库作为 System of Record，NoSQL 作为事件、运行时状态、搜索、缓存及大规模非结构化数据的辅助存储。**

长期建议：
- **Aurora PostgreSQL / PostgreSQL**：核心业务事实、关系、事务
- **DynamoDB**：AI Execution Event、Activity Event、Agent Runtime State
- **S3**：文档、原始数据、长期日志、归档
- **OpenSearch**：全文检索、大规模搜索、后期 Vector Search
- **Redis**：缓存、临时状态、Heartbeat、Rate Limit、Distributed Lock
- **pgvector**：初中期 Knowledge / RAG 向量检索

不建议因为“数据是 JSON”就引入 MongoDB。PostgreSQL JSONB 已经可以覆盖 good7ob 很多动态 Schema 场景。

## 2. 关系型数据库与 NoSQL 对比

| 特性 | 关系型数据库 | NoSQL |
|---|---|---|
| 典型产品 | PostgreSQL / Aurora | DynamoDB / MongoDB / Redis |
| Schema | 强结构 | 通常更灵活 |
| JOIN | 强 | 通常较弱或没有 |
| Transaction | 强 | 取决于具体产品 |
| 复杂查询 | 强 | 通常围绕 Access Pattern 设计 |
| 数据关系 | 非常适合 | 不适合复杂关系 |
| 水平扩展 | 相对复杂 | 通常更容易 |
| 大量事件写入 | 可以 | 很适合 |
| 动态结构 | JSONB 可覆盖大量场景 | 很适合 |
| AI 日志 / Event | 可以 | 很适合 |
| 项目 / 需求管理 | 非常适合 | 不适合作为主库 |

good7ob 核心业务关系非常强：

```text
Organization
     ↓
Product
     ↓
Requirement
     ↓
Design
     ↓
Task
     ↓
Code
     ↓
Test
     ↓
Release
```

因此核心业务仍应建立在 PostgreSQL / Aurora PostgreSQL 上。

## 3. good7ob 数据存储建议

| 数据类型 | 推荐存储 |
|---|---|
| Organization | PostgreSQL |
| User / Actor | PostgreSQL |
| Product | PostgreSQL |
| PRD / Requirement | PostgreSQL |
| Project / Task | PostgreSQL |
| Test Case / Release | PostgreSQL |
| Approval / Billing | PostgreSQL |
| Artifact Relation | PostgreSQL |
| AI Run 主记录 | PostgreSQL |
| AI Execution Event | DynamoDB |
| Agent Runtime State | DynamoDB / Redis |
| 大量 Activity Event | DynamoDB / S3 |
| 动态 AI Metadata | PostgreSQL JSONB / DynamoDB |
| Knowledge 文件原文 | S3 |
| 小中规模向量数据 | PostgreSQL + pgvector |
| 大规模搜索 / Vector Search | OpenSearch |
| Cache | Redis |
| 长期 AI Log | S3 |
| Analytics | S3 + Athena |

## 4. AI Execution Event：最适合 DynamoDB

一个 AI Run 可能产生：

```text
AI Run
 ├── RUN_STARTED
 ├── PROMPT_CREATED
 ├── MODEL_CALLED
 ├── TOOL_CALLED
 ├── FILE_READ
 ├── FILE_WRITTEN
 ├── COMMAND_EXECUTED
 ├── TOKEN_USED
 ├── RETRY
 ├── ERROR
 ├── TEST_STARTED
 ├── TEST_COMPLETED
 └── RUN_COMPLETED
```

这类数据写入量大、基本不 UPDATE、主要按 `run_id` 和时间查询、Schema 持续变化，因此很适合 DynamoDB。

推荐职责分离：

```text
PostgreSQL
└── ai_run
    ├── id
    ├── org_id
    ├── task_id
    ├── agent_id
    ├── model_id
    ├── status
    ├── started_at
    ├── completed_at
    ├── total_token
    └── total_cost

DynamoDB
└── ai_run_event
    ├── RUN_STARTED
    ├── MODEL_CALLED
    ├── TOOL_CALLED
    ├── FILE_WRITTEN
    ├── TEST_EXECUTED
    ├── ERROR
    └── RETRY
```

原则：

> PostgreSQL 保存 AI Run 的业务事实和最终状态；DynamoDB 保存 AI Run 的执行过程。

## 5. Agent Runtime State

Agent 运行状态例如：

```text
status = RUNNING
current_task = TASK-123
current_step = 17
heartbeat = 15:30:22
context = {...}
```

特点：
- 高频更新
- 生命周期短
- Schema 灵活
- 不需要复杂 JOIN
- 经常需要 TTL

可以使用 DynamoDB。非常短暂的 Runtime State、Heartbeat 则更适合 Redis。

## 6. Activity Feed

例如：

```text
15:20 AI 完成 TASK-001
15:21 用户修改 Requirement
15:22 AI 创建 Test Case
15:23 GitHub PR #123 创建
15:25 Test Run 完成
15:27 Release 审批通过
```

Activity Feed 本质是 Event Stream，可以使用：

```text
PK = ORG#10001
SK = timestamp
```

或者：

```text
PK = PRODUCT#10001
SK = timestamp
```

近期数据保留 DynamoDB，长期低频数据可归档到 S3。

## 7. Knowledge / RAG

推荐分层。

### PostgreSQL

保存文档 Metadata：

```text
document
├── id
├── org_id
├── knowledge_base_id
├── title
├── status
├── version
├── s3_key
├── created_by
└── created_at
```

### S3

保存：
- PDF / Word / Markdown
- 代码
- 原始文件
- 解析文本
- Chunk
- 中间处理结果
- 归档版本

### Vector Store

初中期：

```text
PostgreSQL + pgvector
```

规模明显增长后：

```text
OpenSearch Vector Engine
```

Knowledge / RAG 并不意味着必须增加 MongoDB。

## 8. AI Metadata：优先 PostgreSQL JSONB

例如：

```json
{
  "model": "xxx",
  "confidence": 0.93,
  "complexity": {
    "frontend": 3,
    "backend": 5,
    "database": 2
  }
}
```

可以直接：

```text
requirement
├── id
├── title
├── status
└── ai_metadata JSONB
```

原则：

> **有 JSON ≠ 需要 NoSQL。**

稳定且需要 JOIN、WHERE、ORDER、GROUP、UNIQUE、INDEX 的核心字段应优先设计为普通 Column。

## 9. Redis 的定位

适合：
- Session
- Cache
- Rate Limit
- Distributed Lock
- Temporary Token
- Agent Heartbeat
- 短期 Workflow State
- 实时状态

Redis 不应该作为 Requirement、Task、PRD、Project、Release 等核心数据的 System of Record。

## 10. MongoDB 是否需要

当前阶段不建议专门引入 MongoDB。

PostgreSQL 已经提供：

```text
Relational Model + JSONB
```

额外引入 MongoDB 会增加：
- 数据一致性复杂度
- DevOps 成本
- Backup / Restore 成本
- Monitoring 成本
- 权限管理成本
- AI Agent 开发复杂度

数据库种类越多并不代表架构越先进。每增加一种数据库，都应该存在明确业务理由。

## 11. 推荐长期架构

```text
                     good7ob
                        │
        ┌───────────────┼────────────────┐
        │               │                │
        ▼               ▼                ▼
 Aurora PostgreSQL   DynamoDB            S3
        │               │                │
 Core Business      High-volume       Documents
        │             Events          Logs/Archive
        │               │
        │        ┌──────┴──────┐
        │        │             │
        │     AI Events     Activity
        │
        ├───────────────┐
        │               │
        ▼               ▼
    pgvector        OpenSearch
        │               │
   Small RAG         Full Search
                    Large RAG
                        │
                        ▼
                      Redis
                        │
                  Cache / Runtime
```

## 12. 各存储职责

### Aurora PostgreSQL

负责：

```text
事实
关系
业务状态
Transaction
Consistency
```

属于核心 **System of Record**。

### DynamoDB

负责：

```text
AI Execution Event
Agent Runtime State
Activity Stream
大量高频 Event
```

### S3

负责：

```text
File
Raw Data
Long-term Log
Archive
Knowledge Source
AI Large Output
```

### OpenSearch

负责：

```text
全文搜索
跨对象搜索
日志搜索
大规模 Vector Search
```

### Redis

负责：

```text
Cache
Temporary State
Lock
Heartbeat
Rate Limit
Session
```

## 13. 分阶段实施

### Phase 1：当前阶段

```text
PostgreSQL / Aurora
+
JSONB
+
S3
```

数据库模型提前把：

```text
AI Run
   ↓
AI Event
```

分开。

即使第一版 `ai_run_event` 暂时仍然存 PostgreSQL，也不要把 Event 数据全部塞入 `ai_run`。

### Phase 2：AI Agent 执行规模增长

增加 DynamoDB：

```text
AI Execution Event
Agent Runtime State
Activity Stream
```

### Phase 3：Knowledge / Search 规模增长

增加 OpenSearch：

```text
全文搜索
跨 Artifact 搜索
大型 Knowledge Search
Vector Search
```

小规模 RAG 继续使用 PostgreSQL + pgvector。

### Phase 4：并发和实时需求增长

增加 Redis：

```text
Cache
Heartbeat
Runtime State
Distributed Lock
Rate Limit
Session
```

## 14. 数据生命周期

```text
核心业务数据
    ↓
PostgreSQL
    ↓
长期保存

AI Runtime
    ↓
Redis / DynamoDB
    ↓
短期

AI Event
    ↓
DynamoDB
    ↓
近期在线查询
    ↓
S3
    ↓
长期归档

Knowledge File
    ↓
S3
    ↓
长期保存

Search Index
    ↓
OpenSearch
    ↓
可重新构建
```

Search Index、Cache、部分 Runtime State 不应该成为唯一数据源。

## 15. System of Record 原则

| 数据 | System of Record |
|---|---|
| Organization | PostgreSQL |
| Requirement | PostgreSQL |
| Task | PostgreSQL |
| Test Case | PostgreSQL |
| Release | PostgreSQL |
| AI Run 最终状态 | PostgreSQL |
| AI Execution Event | DynamoDB |
| Knowledge File | S3 |
| Search Index | 非权威数据，可重建 |
| Cache | 非权威数据，可丢失 |
| Runtime Heartbeat | Redis / DynamoDB |

必须避免：

```text
PostgreSQL 有一份
DynamoDB 有一份
OpenSearch 有一份

但不知道谁才是正确数据。
```

## 16. good7ob 推荐原则

good7ob 不应该采用：

> Relational VS NoSQL 二选一

而应该采用：

> **Polyglot Persistence（多模型持久化）**

但必须保持克制。

1. **PostgreSQL 是核心 System of Record**
2. **DynamoDB 处理高频 AI/Event 数据**
3. **S3 管理大对象和长期数据**
4. **OpenSearch 是搜索层，不是主数据库**
5. **Redis 是缓存与临时运行状态层**

## 17. 对当前数据库重构的直接建议

当前阶段优先完成：

```text
Core Data Model
        ↓
Organization
        ↓
Actor
        ↓
Product
        ↓
Artifact
        ↓
Traceability
```

同时把 AI 数据明确拆成：

```text
AI Task
   │
   ▼
AI Run
   │
   ▼
AI Event
```

存储策略：

```text
AI Task  → PostgreSQL
AI Run   → PostgreSQL
AI Event → 当前 PostgreSQL / 未来 DynamoDB
```

这样不会增加当前基础设施复杂度，同时为未来大规模 AI Agent 执行留下清晰迁移路径。

## 18. 最终推荐

> **good7ob 应以 PostgreSQL / Aurora 作为核心 System of Record；利用 DynamoDB 承载高频 AI/Event 数据，S3 承载文件和长期数据，OpenSearch 承载搜索，Redis 承载缓存和短期运行状态。NoSQL 是关系型数据库的补充，而不是替代。**

现阶段最值得提前设计好的 NoSQL 边界是：

> **AI Run 与 AI Execution Event 分离。**

即使当前两者都存 PostgreSQL，未来也可以在不改变核心业务模型的情况下，将海量 AI Event 平滑迁移到 DynamoDB / S3。
