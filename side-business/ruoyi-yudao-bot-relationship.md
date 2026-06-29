# RuoYi / Yudao 平台化 Bot 关系

本页记录 `Clone-ruoyi-vue-pro-Bot` 与 `codegen-bot` 在 RuoYi/Yudao 平台化流水线中的定位和边界。

## 涉及仓库

- [`FutureTechQuant/Clone-ruoyi-vue-pro-Bot`](https://github.com/FutureTechQuant/Clone-ruoyi-vue-pro-Bot)
- [`FutureTechQuant/codegen-bot`](https://github.com/FutureTechQuant/codegen-bot)
- 当前生成/改造目标示例：[`FutureTechQuant/future-vue-pro`](https://github.com/FutureTechQuant/future-vue-pro)

## 一句话关系

`Clone-ruoyi-vue-pro-Bot` 负责初始化或重构一套 RuoYi/Yudao 基础工程；`codegen-bot` 负责在已有 RuoYi/Yudao 工程上，根据业务 SQL 表结构生成后端模块和前端页面。

换句话说：

```text
Clone-ruoyi-vue-pro-Bot：先造房子 / 平台底座
codegen-bot：再按业务数据模型生成房间 / 业务模块
```

## 当前定位

### Clone-ruoyi-vue-pro-Bot

用途：

- 从 RuoYi/Yudao 基础项目生成或初始化 Future 风格的后端工程。
- 重构原始平铺模块结构。
- 当前生成出的 `future-vue-pro` 结构大致为：

```text
apps/
  future-server
platform/
  future-dependencies
  future-framework
modules/
  core/
    system
    infra
  biz/
    crm
    erp
    mall
  extend/
    ai
    bpm
    iot
    member
    mp
    pay
    report
  custom/
    asset
```

适合承担：

- 平台底座初始化。
- RuoYi/Yudao 工程结构改造。
- 业务实例代码仓库生成前的基础工程准备。

### codegen-bot

用途：

- 输入 `sql/schema/*.sql` 业务建表 SQL。
- 启动 MySQL/Redis/Java/Maven 环境。
- 使用 Yudao 内置 `CodegenService` 生成代码。
- 输出后端模块和 Vue3 前端页面。
- 支持单模块和多模块生成。

当前示例：

```text
sql/schema/asset.sql
```

会生成资产/权益相关模块，例如：

```text
asset_definition
asset_sku_grant_rule
asset_user_account
asset_user_account_source
asset_user_account_log
asset_usage_record
```

当前输出方向大致是：

```text
后端：yudao-module-asset
前端：src/api/asset
前端：src/views/asset
```

适合承担：

- 从业务数据模型快速生成 CRUD 后端代码。
- 从表结构快速生成后台管理端页面。
- 批量生成多个业务模块的初始代码。
- 生成后交给人或 Agent 做二次审查和业务化改造。

## 两者的流水线关系

理想流程：

```text
1. 业务需求 / 数据模型 / SQL
   ↓
2. Clone-ruoyi-vue-pro-Bot 初始化平台底座或业务后端仓库
   ↓
3. codegen-bot 根据 sql/schema/*.sql 生成业务模块代码
   ↓
4. 生成代码以 PR 形式进入目标业务代码仓库
   ↓
5. Agent / 人审查权限、接口、数据边界、前端页面和测试
   ↓
6. 合并后由独立 deploy 仓库负责部署
```

## 当前主要断点

### 1. 两个 bot 还没有完全打通

`Clone-ruoyi-vue-pro-Bot` 当前会生成 Future 风格结构：

```text
modules/custom/<module>/future-module-<module>-api
modules/custom/<module>/future-module-<module>-biz
```

但 `codegen-bot` 当前仍偏向原始 Yudao 输出结构：

```text
yudao-module-<module>
src/api/<module>
src/views/<module>
```

因此后续需要让 `codegen-bot` 支持 `future-layout` 输出模式。

### 2. 两个 bot 都存在硬编码组织问题

当前仍有 `FutureTechQuant`、固定后端仓库、固定前端仓库等硬编码。后续应参数化：

```text
target_org
backend_repo
frontend_repo
backend_branch
frontend_branch
layout_mode
```

### 3. 当前发布方式不适合长期生产流程

`codegen-bot` 当前发布逻辑存在删除并重建目标仓库的行为，适合实验或重建演示仓库，但不适合长期业务仓库。

目标方式应改为：

```text
clone existing repo
create branch
apply generated code
write manifest/report
open PR
human/agent review
merge
```

### 4. 部署不应放在应用代码仓库

生成代码仓库只负责：

```text
build
test
package
artifact/image
```

部署应由独立 deploy 仓库负责。

## 建议归属

长期建议将两者都归入平台工程资产：

```text
Business-Unit-for-Platform/clone-ruoyi-vue-pro-bot
Business-Unit-for-Platform/codegen-bot
Business-Unit-for-Platform/ruoyi-vue-pro-base
```

具体业务实例再进入对应业务 Organization，例如：

```text
Business-Unit-for-Gaokao/gaokao-admin-backend
Business-Unit-for-Debet/debet-admin-backend
Business-Unit-for-Future/xinliang-admin-backend
```

## 改造优先级

### P0：安全与边界

- `target_org` 参数化，不再硬编码 `FutureTechQuant`。
- 默认禁止删除已有仓库。
- 生成代码默认开 PR，不直接推主分支。
- 移除人为 workflow timeout。
- 应用仓库只保留 CI/build/test，不内置部署。

### P1：事实源与溯源

- 生成 `AGENTS.md`。
- 生成 `docs/` 骨架。
- 生成 Issue / PR 模板。
- 生成完整 `generated/manifest.json`。
- 生成 `generated/codegen-report.md` 或 `docs/architecture/generated-code.md`。

manifest 至少记录：

```text
source repo / branch / commit
generator repo / commit
SQL 文件列表
业务表列表
模块名与表前缀
输出文件列表
目标 org/repo/branch
生成时间
人工 review checklist
```

### P2：结构打通

- `codegen-bot` 增加 `layout_mode=yudao-upstream|future-layout`。
- `future-layout` 输出到 `future-vue-pro` 的 `apps/platform/modules` 结构。
- 自动更新对应 Maven 聚合 POM。
- 区分 API 契约模块和 biz 实现模块。

### P3：纳入 AI 项目操作系统

- 每次生成 PR 关联需求、数据模型、SQL 和验收清单。
- 从 `requirements`、`data-assets`、`knowledge-base` 读取上下文。
- 将可复用生成流程沉淀到 `skills-library`。
- 代码生成后必须进行权限、安全、数据隔离、接口暴露范围审查。

## 最终判断

这两个 bot 应该被看作同一条平台化流水线的两个阶段：

```text
平台底座生成 / 重构 → 业务模块代码生成 → PR 审查 → 业务仓库合并 → 独立部署
```

当前都已经具备早期自动化能力，但还没有达到长期生产级平台工程标准。下一步重点不是继续手动生成代码，而是先把安全边界、组织参数化、PR 流程、manifest 溯源和 `future-layout` 输出模式补齐。
