# ODM - 正交领域建模

**把业务建模变成"有规矩、能算账、可审查"的工程，让 AI 也能执行。**

---

## 一句话说明

**问题**：DDD 太抽象，建模靠直觉，评审各说各话。

**解法**：六域归属表 + 十条铁律 + 复杂度测算，把建模变成"查表 + 过规则 + 算账"。

**用法**：作为 AI Skill 或团队规范使用。

---

## 核心概念

### 六域：属性归属哪个域？

| 域 | 问法 | 典型属性 |
|----|------|---------|
| 身份域 | 这东西是什么？属于谁？作用于谁？ | orderType, tenantId, customerId |
| 需求域 | 要达成什么业务目标？ | requirement, businessGoal |
| 计划域 | 预期怎么达成？ | plannedQty, deadline |
| 执行域 | 实际怎么做？ | actualQty, executor |
| 结果域 | 最终达成了什么？ | status, completedAt |
| 约束域 | 什么条件下可以做/不能做？ | maxAmount, validationRules |

**原则**：一个属性只属于一个域，跨域必须拆分。

---

### 十条铁律：建模必须遵守的规则

| 序号 | 准则 | 一句话解释 |
|------|------|-----------|
| 1 | 一个属性只属于一个域 | 别把计划数量和实际数量混成一个字段 |
| 2 | 不同业务的状态要分开 | 报价态和合同态是两回事，别合并 |
| 3 | 多个状态用组合，不要合并 | 用 `auditStatus × refundStatus`，别造 `AUDITING_REFUNDING` |
| 4 | 状态要完整、不重叠 | 覆盖所有情况，别留盲区 |
| 5 | 结果只追加，不覆盖 | 用流水表记录，别覆盖历史 |
| 6 | 计划和实际要分开 | `plannedQty` 和 `actualQty` 分开 |
| 7 | 规则要写成属性 | 别把规则藏在代码里 |
| 8 | 用基础类型，不要JSON | 别用 `extra_data` JSON 字段 |
| 9 | 状态要有始有终 | 有起点和终点，别出僵尸状态 |
| 10 | 同名不同域要隔离 | `OrderCustomer` 和 `ContractCustomer` 分开 |

---

## 一个案例看懂

**场景**：出货单有两个独立流程——"单据确认"和"货物配送"。

```
❌ 错误：合并成一个状态链
status: 草稿 → 已提交 → 已出货 → 已送达

问题：业务要加"撤销"
- 撤销插在哪？状态链被打断
- 已出货后还能撤销吗？→ 出现"已出货且已撤销"
- 状态叠加，if-else 暴增

✅ 正确：拆成两个独立状态
docStatus:   草稿 | 已提交 | 已撤销
shipStatus:  未出货 | 已出货 | 已送达

组合矩阵（3×3=9种）：
草稿+未出货 → 可编辑
已提交+未出货 → 待发货
已撤销+已出货 → 需召回
```

---

## 快速开始

### 作为 AI Skill

```bash
# Trae：将本项目放入 Skills 目录
# Cursor/Copilot：将 SKILL.md 内容添加到 .cursorrules

/m-modeling 评审下订单模型设计
/m-modeling 分析下 User 类的属性设计
/m-modeling 设计一个发货单实体
```

### 作为团队规范

1. 读 [1.guide.md](skills/m-modeling/references/1.guide.md) —— 学六域 + 十条铁律 + 建模步骤
2. 读 [demo.md](skills/m-modeling/references/demo.md) —— 看案例
3. 建模时按步骤执行，输出属性归属表 + 状态矩阵

---

## 文档

| 文档 | 内容 | 何时看 |
|------|------|--------|
| [1.guide.md](skills/m-modeling/references/1.guide.md) | 六域 + 十条铁律 + 建模步骤 | 必读 |
| [2.exceptions.md](skills/m-modeling/references/2.exceptions.md) | 技术属性（冗余存储、乐观锁等） | 建模完成后 |
| [3.complexity.md](skills/m-modeling/references/3.complexity.md) | 复杂度测算（防止过度设计） | 拿不准拆不拆时 |
| [demo.md](skills/m-modeling/references/demo.md) | 案例演示 | 学习参考 |

---

## 项目结构

```
odm/
├── README.md
└── skills/
    └── m-modeling/
        ├── SKILL.md              # AI Skill 定义
        └── references/
            ├── 1.guide.md        # 主指南（必读）
            ├── 2.exceptions.md   # 排除特例
            ├── 3.complexity.md   # 复杂度测算
            └── demo.md           # 案例演示
```

---

## 与其他 DDD 资源的关系

| 资源 | 提供什么 |
|------|---------|
| ddd-starter | 流程图：从哪开始、按什么顺序做 |
| EventStorming | 发现工具：挖掘业务事件 |
| Bounded Context Canvas | 设计模板：定义上下文边界 |
| **ODM** | **检查清单 + 计算器：属性怎么拆、状态怎么设计** |

---

## 适用场景

- 新项目建模
- 现有模型评审
- 重构前审查
- 团队规范统一
- AI 辅助开发

---

## 许可证

MIT License