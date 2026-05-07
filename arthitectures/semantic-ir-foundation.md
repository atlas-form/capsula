# Data Capsule Architecture：Semantic IR Foundation

## 1. 一句话定义

Data Capsule 是 AI-native 数据世界中的用户所有语义中间表示。

它不是数据库模型，不是 CRUD API，不是平台业务 schema，也不是某个 App 的数据格式。

它是一个统一语义层：把来自不同平台、不同文件、不同应用的数据，lower 成 Agent Runtime 可以理解的稳定语义对象。

```text
Source Data
  -> Semantic Lowering
  -> Capsule IR
  -> Agent Cognition / Runtime / Action
```

核心判断是：

> Data Capsule 之于 AI-native 数据层，类似 LLVM IR 之于编译器，WASM 之于执行环境。

它让 Agent 面向稳定的语义结构进行理解、记忆、推理和行动，而不是直接依赖平台 schema。

## 2. 为什么需要 Capsule

当前互联网是 platform-native 的。

数据通常被困在平台内部：

- 微信拥有自己的消息结构。
- 邮件系统拥有自己的邮箱结构。
- GitHub 拥有 issue、pull request、review 的结构。
- Slack 拥有 channel、thread、message 的结构。
- 浏览器拥有历史记录、书签、页面上下文。
- 笔记软件拥有自己的文档格式。

用户可以通过平台 UI 操作这些数据，但 Agent 很难把它们作为用户自己的统一语义记忆来理解。每个平台都有自己的 schema、权限边界、对象模型和语义假设。

AI-native 世界应该反过来：

- 数据属于用户。
- 平台只是服务提供者。
- App schema 只是 source format。
- Agent 应该运行在用户拥有的语义数据之上。
- 平台数据需要 lower 成统一语义结构。

Capsule 就是这个统一语义结构。

## 3. 设计目标：面向 Agent Cognition

Capsule 不是面向 UI 的结构。

UI 关心页面、组件、交互、布局和状态。业务后端关心表、事务、接口和流程。Agent 需要的是另一种东西：一个能够说明“这段数据在世界中意味着什么”的稳定语义对象。

例如：

- 微信消息、Slack 消息、邮件回复、GitHub 评论，都可以 lower 成 `conversation.message`。
- 个人笔记、阅读笔记、会议笔记，可以 lower 成 `learning.note` 或 `knowledge.note`。
- 信用评价、商品评价、服务评价，可以 lower 成评价类语义对象。
- 个人主页、账号资料、联系人卡片，可以 lower 成 `identity.profile`。

source format 在 ingestion 阶段很重要，但不应该成为 Agent 的认知基础。

Agent 应该理解 Capsule，而不是理解每个平台的内部 schema。

## 4. 用户拥有数据

Capsule 从一个简单原则开始：

> 数据本体属于用户，不属于平台。

平台可以提供：

- 存储
- 同步
- 索引
- 搜索
- API
- 转换服务
- 路由
- 可用性

但平台不能被视为语义数据本体的所有者。

这是一个根本架构边界。如果平台拥有 schema 和权威对象，Agent 就仍然依赖平台。如果用户拥有语义对象，平台就只是可替换的服务层。

因此，所有权模型是：

- 用户拥有 semantic data。
- 平台持有 source representation。
- lowering pipeline 生成 Capsule。
- Agent Runtime 消费 Capsule IR。
- 服务可以增强、索引、路由 Capsule，但不应该重新定义 Capsule 的语义身份。

当前阶段不需要复杂协议来证明这一点。当前阶段需要先冻结这个哲学：用户拥有的语义数据是根对象。

## 5. Capsule 是 Semantic IR

在编译器系统中，源语言通常不会直接执行。它会先被 lower 成中间表示。

不同语言可以编译到同一个 IR：

```text
C / Rust / Swift / Zig
  -> Frontend Parser
  -> LLVM IR
  -> Optimization
  -> Machine Code
```

IR 不是源语言，也不是最终机器码。它是一个抽象层，让不同输入可以被统一分析、优化和执行。

Capsule 在 AI-native 数据层中扮演类似角色：

```text
WeChat / Email / GitHub / Slack / Browser / PDF / Notes
  -> Semantic Lowering
  -> Data Capsule IR
  -> Agent Runtime
  -> Reasoning / Memory / Planning / Action
```

Capsule 不是原始平台数据，也不是最终业务结构。它是中间的语义表示。

这带来几个关键性质：

- Source independence：Agent 不需要学习每个平台的 schema。
- Runtime stability：Agent Cognition 可以依赖稳定语义形态。
- Semantic normalization：相似含义可以跨来源比较。
- Extensibility：新平台只需要增加 lowering pipeline。
- Portability：用户数据迁移时不丢失语义身份。

## 6. Semantic Lowering

Lowering 是把 source object 翻译成 Capsule 的过程。

它不是简单字段映射。

字段映射只是复制结构。Semantic lowering 需要解释意义。

例如，邮件有 `from`、`to`、`subject`、`body`、`thread_id`。Slack 消息有 `user`、`channel`、`text`、`ts`。GitHub review comment 有 author、body、diff position、repository 和 pull request context。

它们的 source schema 完全不同。但 lower 之后，Agent 可能只需要看到：

```text
type: conversation.message
actor: 谁说的
audience: 对谁说的
content: 说了什么
context: 这句话属于哪个上下文
time: 什么时候说的
source: 来自哪里
```

目标不是抹掉 source 信息，而是防止 source schema 成为 Agent 的主认知结构。

Lowering 应该完成：

- semantic extraction
- semantic normalization
- type assignment
- context preservation
- provenance preservation
- source-specific detail isolation

source 仍然有价值，但它不再是主抽象。

## 7. Semantic Normalization

Normalization 的目标，是让不同来源中的等价或相关含义可以被统一理解。

如果没有 normalization，每个平台都会形成一个小宇宙。Agent 必须不断在不兼容结构之间翻译。

Capsule IR 应该帮助系统 normalize：

- identity reference
- timestamp
- authorship
- conversation structure
- document title
- event boundary
- review target
- claim and opinion
- note subject
- knowledge fragment

Normalization 不等于把所有数据压平成一个万能表。

它的目标是给 Agent Runtime 一个共享语义语法，让不同来源的数据可以在同一认知空间里被比较、检索和推理。

目标不是形式统一，而是认知互操作。

## 8. 当前阶段：只处理文本语义数据

当前阶段只处理文本语义数据。

范围内：

- note
- message
- review
- profile
- article
- knowledge
- event
- comment
- claim
- summary
- document-like text

当前阶段明确不处理：

- 图片
- 视频
- 音频
- embedding
- vector search
- 多媒体提取
- 分布式存储
- blockchain
- CRDT
- P2P
- 加密设计
- 网络协议设计

这个限制是有意的。

系统应该先冻结语义基础，再扩展到媒体、存储、密码学、同步或分布式执行。

第一阶段只需要把一个问题回答好：

> Agent 最小需要什么样的语义对象，才能可靠理解用户数据？

## 9. Capsule 本身不包含行为

Capsule 只提供语义数据。

Capsule 不负责：

- create
- update
- delete
- workflow
- execution
- automation
- business logic
- UI interaction
- runtime policy decision

行为属于：

- Agent
- Runtime
- Skill
- Cognition
- Application service
- Workflow engine

这个边界很重要。一旦行为进入 Capsule，Capsule 就会变成应用对象或协议对象，而不是 IR。

IR 描述“存在什么”，并以 runtime 可消费的形式呈现。IR 不决定 runtime 必须做什么。

## 10. 最小 Capsule 核心

第一阶段只应该冻结最小概念核心。

一个最小文本 Capsule 至少需要六类信息：

```text
Capsule
  id
  owner
  type
  content
  context
  source
```

这不是最终 wire format，也不是数据库 schema。它只是概念最小集。

### `id`

Capsule 的稳定标识。

它让 Capsule 可以被引用、链接、索引、比较和迁移。

### `owner`

语义数据的拥有者。

这里的 owner 不是数据库外键，而是数据主权的根。

### `type`

Capsule 的语义类型。

这是 AI 理解对象的入口。

### `content`

规范化后的文本语义内容。

当前阶段应该 text-first。内容可以有结构，但结构必须服务语义，而不是复刻平台 schema。

### `context`

理解内容所需的上下文。

context 可以包括 conversation、document、event、actor、target、topic、temporal placement 等。目标是保留意义，不是复刻平台对象。

### `source`

lowering 前的数据来源。

source 用于追溯、调试和验证，但不应该支配 Capsule 的语义形态。

## 11. Type System 非常重要

Type system 是 Capsule 最重要的部分之一。

type 不是数据库字段类型。

type 是：

> AI 对世界的语义理解入口。

例如：

- `learning.note`
- `conversation.message`
- `credit.review`
- `identity.profile`
- `knowledge.article`
- `calendar.event`
- `project.issue`
- `code.review`

一个 type 告诉 Agent Runtime：这个 Capsule 表示世界中的哪类对象。

它影响：

- content 应该如何解释
- context 应该包含什么
- 可能存在什么关系
- 哪些 reasoning operation 是自然的
- 如何与其他 Capsule 比较
- 哪些 Skill 可能相关

因此，type system 不是实现细节，而是 semantic contract。

## 12. Namespace

Type 应该使用 namespace。

Namespace 防止系统走向两个极端：

- 所有类型混成一个不可维护的扁平列表。
- 所有类型都必须由中央权威预先批准。

示例：

```text
conversation.message
conversation.thread
learning.note
learning.question
identity.profile
credit.review
knowledge.article
project.issue
project.decision
```

Namespace 提供：

- semantic grouping
- extensibility
- readability
- future compatibility
- domain-specific vocabulary 的空间

正确方向是：稳定核心 + 可扩展 namespace。

## 13. Semantic Stability

Capsule type 必须足够稳定，Agent 才能在其上建立长期记忆和推理。

如果一个 type 的含义会随着某个 App 的字段变化而变化，它就不是 semantic type，只是改名后的平台 schema。

Semantic stability 意味着：

- `conversation.message` 不依赖 Slack、微信或邮件内部结构。
- `identity.profile` 不依赖某个平台的账号页。
- `learning.note` 不依赖某个笔记软件的文档模型。
- `credit.review` 不依赖某个交易平台的评分数据库。

稳定语义类型应该能穿越 source format 的变化。

这不代表 type 永远不能演化。它代表 type 的语义演化必须谨慎、缓慢、兼容。

## 14. 不要从大而全协议开始

系统不能一开始就设计成大而全协议。

过早定义所有领域、所有对象、所有平台、所有行为、所有 runtime 规则，会在语义还没有充分清晰时把系统冻死。

第一阶段应该 freeze：

- Semantic IR philosophy
- ownership model
- text-only scope
- no-behavior boundary
- type philosophy
- minimal Capsule shape
- lowering model

第一阶段不应该 freeze：

- 复杂存储模型
- 分布式共识
- 同步协议
- 加密方案
- vector system
- UI schema
- CRUD API
- workflow engine
- 完整世界本体论

架构应该小到可以实现，但清楚到足以指导未来扩展。

## 15. 与现有 Capsule 层的关系

当前项目已有文档讨论了安全容器、签名、SealedBlock、Cap0/Cap1/Cap2、policy 和数据银行服务。

这些是实现层、信任层和治理层的重要设计。

本文档位于它们之上。

Semantic IR foundation 回答的是：

> Agent 应该理解和推理的语义对象是什么？

安全胶囊层回答的是：

> 这个对象如何被存储、验证、封装、链接或治理？

这两个问题应该分离。

Semantic Capsule 不能退化成纯密码学 envelope。密码学 envelope 可以保护数据，但它本身不告诉 Agent 数据是什么意思。

长期架构需要二者并存：

- Semantic IR 用于 Agent Cognition。
- Security / provenance layer 用于 trust and control。

但当前阶段应该先冻结 semantic layer。

## 16. Agent Runtime Model

Agent Runtime 不应该直接运行在平台 schema 上。

它应该运行在 Capsule IR 上：

```text
Source Connectors
  -> Lowering Pipelines
  -> Capsule Store / Index
  -> Agent Runtime
  -> Skills / Tools / Actions
```

Runtime 看到的是一组语义对象，而不是一堆互不兼容的 App 导出。

这让 Agent 可以：

- 跨平台记住用户数据
- 比较语义相似对象
- 按意义检索上下文
- 构建用户级知识
- 跨 source boundary 推理
- 在不依赖平台 schema 的情况下选择行动

Runtime 后续仍然可以调用平台 API。但它的 cognition 应该从 Capsule IR 开始，而不是从平台 API shape 开始。

## 17. Lowering 示例

### Message

```text
Slack message
Email reply
WeChat message
GitHub comment
  -> conversation.message
```

source object 不同，但语义角色相似：某个 actor 在某个 communicative context 中说了一段话。

### Note

```text
Markdown note
Notion page
Browser highlight
PDF annotation
  -> learning.note
```

source object 不同，但语义角色是用户拥有的知识片段。

### Review

```text
Marketplace rating
Credit comment
Service evaluation
Code review comment
  -> credit.review / code.review
```

source object 不同，但语义角色是某个 actor 对某个 target 的评价。

### Profile

```text
Contact card
Account profile
Resume section
Organization member record
  -> identity.profile
```

source object 不同，但语义角色是身份描述。

## 18. Non-Goals

本文档不定义：

- database schema
- REST API
- UI
- full protocol
- sync algorithm
- encryption system
- distributed network
- embedding model
- ontology for the whole world
- workflow language
- permission system

这些以后可能需要，但它们不属于最小 semantic foundation。

## 19. 架构原则

最重要的规则是：

> Capsule 应该是最小的稳定语义对象，让 Agent 能跨平台理解用户拥有的数据。

其他东西都应该围绕它分层。

如果一个设计直接帮助语义理解，它可以靠近核心。

如果一个设计控制存储、传输、执行、workflow 或 UI，它应该在核心之外。

## 20. 最终立场

Data Capsule 是 AI-native Semantic Data Layer 的基础。

它的目标是让未来 Agent：

- 运行在用户拥有的数据之上
- 在 cognition 阶段不依赖平台 schema
- 直接理解文本语义对象
- 在统一语义结构上推理
- 保留 source traceability，但不被 source 绑定
- 基于稳定 semantic type 构建记忆和行动

第一阶段不是构建一个大系统。

第一阶段是冻结正确抽象。

这个抽象是：

> 用户拥有的文本语义数据，被 lower 成稳定 Capsule IR，由可扩展 semantic type system 标注，并作为 Agent Runtime 的认知基底。
