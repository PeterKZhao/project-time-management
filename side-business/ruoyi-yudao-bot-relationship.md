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

## Clone-ruoyi-vue-pro-Bot 待解决问题

以下问题专门针对 [`FutureTechQuant/Clone-ruoyi-vue-pro-Bot`](https://github.com/FutureTechQuant/Clone-ruoyi-vue-pro-Bot)。当前它生成的 Java/Maven 目录方向基本合理，但还没有达到长期平台底座生成器标准。

### 1. 目标 Organization 硬编码

当前 workflow 中存在类似：

```text
ORG=FutureTechQuant
```

问题：

- 不符合 `Business-Unit-for-*` 的业务组织原则。
- 不能直接复用到高考、Debet、新粮、相亲、同伴游等不同业务 Organization。
- 容易把平台底座、业务实例和实验仓库都混在 `FutureTechQuant`。

建议：

```text
target_org
repo_name
repo_description
private
```

都应作为 workflow input 或配置项。

### 2. 默认删除已有目标仓库，风险高

当前生成流程里有删除已存在目标仓库的逻辑。

问题：

- 一旦 repo 名填错，可能删除已有资产。
- 不适合长期生产仓库。
- 不符合“生成代码走 PR、可回滚、可审查”的原则。

建议：

- 默认不删除已有仓库。
- 已存在时 fail fast，并提示用户确认。
- 如确实需要重建，只允许显式传入：

```text
delete_existing=true
```

并在日志和文档中明确这是破坏性操作。

### 3. 生成仓库缺少项目事实源

当前生成出的 `future-vue-pro` 缺少或不完整：

```text
AGENTS.md
docs/
.github/ISSUE_TEMPLATE/
.github/pull_request_template.md
docs/architecture/generated-from.md
generated/manifest.json
generated/transform-report.md
```

问题：

- Agent 进入仓库后缺少操作规则。
- 人类和 Agent 不知道项目从哪里生成、用过哪些 transform、源 commit 是什么。
- 后续无法可靠判断哪些文件是上游继承、哪些是本地改造、哪些是业务定制。

建议：

- clone-bot 默认生成 `AGENTS.md`、`docs/`、Issue/PR 模板。
- 生成完整 manifest 和 transform report。
- 至少记录 source repo/branch/commit、generator repo/commit、target repo、生成时间、移动目录映射、脚本列表、构建结果。

### 4. README 仍偏上游宣传文案，没有业务化

当前生成仓库 README 仍保留大量上游 Yudao/RuoYi 风格内容，例如演示地址、上游项目关系、外包宣传等。

问题：

- 不能准确说明 Future 平台底座的定位。
- 不能说明本仓库是平台底座还是业务实例。
- 不利于业务方、Agent、后续开发者理解仓库用途。

建议 README 改为说明：

- 本仓库是什么。
- 从哪里生成。
- 使用哪个 source commit 和 generator commit。
- 当前结构说明。
- 如何本地开发。
- 如何构建和测试。
- 如何生成业务模块。
- 部署归哪个 deploy 仓库负责。

### 5. 应用仓库混入部署逻辑

当前生成出的应用仓库 workflow 中存在 build + deploy 混合逻辑，包含 SSH、SCP、Cloudflare Tunnel、Docker Compose 等部署动作。

问题：

- 应用代码仓库职责过重。
- 不符合应用仓库只负责 build/test/package/artifact 的边界。
- 生产部署配置容易分散在多个应用仓库。
- 后续多业务、多环境部署会难维护。

建议：

- 应用仓库只保留 CI/build/test/package。
- 部署迁移到独立 deploy 仓库。
- deploy 仓库负责环境配置、部署编排、健康检查、回滚。

### 6. workflow 使用第三方 SSH/SCP action

当前生成出的 workflow 存在类似：

```text
appleboy/ssh-action
appleboy/scp-action
```

问题：

- 不符合当前部署偏好：避免带默认超时和隐藏行为的第三方 SSH action。
- 排障和安全边界不如原生 `ssh/scp` 清晰。

建议：

- 如果确实需要 SSH，使用原生 `ssh/scp` 命令。
- 更推荐把部署动作移出应用仓库，交给 deploy 仓库。

### 7. workflow 存在人为 timeout

当前 workflow 存在类似：

```text
timeout-minutes: 30
```

问题：

- 对大型 Java 项目构建、首次 Maven 下载、生成和推送流程不友好。
- 不符合避免人为超时限制的偏好。

建议：

- 移除硬编码 timeout。
- 用明确失败条件、健康检查和日志排障替代固定超时。

### 8. 生成 manifest 太弱

当前生成仓库中的 `generated-manifest.json` 内容较少，例如只记录：

```json
{
  "frontend_dirs": ["src/api/asset", "src/views/asset"],
  "backend_modules": ["future-module-asset"]
}
```

问题：

- 无法完整追踪生成来源。
- 无法支持审计、回滚、重放生成。
- 无法让 Agent 判断生成代码与源 SQL、源 commit、transform 脚本之间的关系。

建议 manifest 至少记录：

```text
source repo / branch / commit
generator repo / commit
target org/repo/branch
generated timestamp
transform scripts and versions
module move map
frontend dirs
backend modules
split api/biz report
build result
manual review checklist
```

### 9. api/biz 拆分策略需要显式化

当前 `split_api_biz.py` 会按启发式规则拆分模块：

```text
future-module-<module>-api
future-module-<module>-biz
```

问题：

- 自动拆分有边界误判风险。
- 不同模块是否需要拆分，不应完全由脚本猜测。
- `api` 包、`enums` 包、`Impl` 文件等规则需要写成可配置策略。

建议：

```text
split_api_biz: true/false
split_modules:
  - asset
  - crm
api_packages:
  - api
  - enums
biz_packages:
  - controller
  - service
  - dal
```

并在 transform report 里记录每次拆分了哪些文件。

### 10. core / biz / extend / custom 语义需要文档化

当前结构方向合理：

```text
modules/core
modules/biz
modules/extend
modules/custom
```

但语义还应写清楚。

建议补充：

- `core`：所有业务必选的平台核心能力。
- `biz`：标准业务套件，如 CRM/ERP/Mall，可按业务启用。
- `extend`：可选扩展能力，如 AI、支付、报表、工作流、公众号等。
- `custom`：当前业务或客户定制模块。

否则后续业务会纠结模块到底该放 `biz`、`extend` 还是 `custom`。

### 11. 平台底座和业务实例还没分清

当前 `future-vue-pro` 既像平台底座，又像业务实例。

问题：

- 平台通用能力和具体业务定制容易混在一个 repo。
- 高考、Debet、新粮、相亲、同伴游都可能需要不同裁剪。

建议区分：

```text
Business-Unit-for-Platform/ruoyi-vue-pro-base
Business-Unit-for-Gaokao/gaokao-admin-backend
Business-Unit-for-Debet/debet-admin-backend
Business-Unit-for-Future/xinliang-admin-backend
```

clone-bot 负责生成 base 或业务实例，但生成模式要明确。

### 12. sql/script 等上游全量内容需要分层裁剪

当前生成仓库保留大量上游内容，例如：

```text
sql/mysql
sql/oracle
sql/postgresql
sql/sqlserver
sql/dm
script/docker
script/jenkins
script/shell
```

问题：

- 作为平台底座可以保留。
- 作为具体业务实例会显得臃肿。
- Agent 容易误以为所有数据库和脚本都是当前业务实际使用。

建议：

- 平台底座保留全量并标注用途。
- 业务实例只保留实际使用的数据库脚本和运行脚本。
- 其他内容转为引用平台底座或文档说明。

### 13. 缺少 PR 化和审查闭环

clone-bot 当前更像一次性创建/推送工具。

问题：

- 对已有业务仓库缺少 branch + PR 改造模式。
- 生成结果缺少标准审查清单。
- 不利于 Agent/人协作验收。

建议支持两种模式：

```text
create_new_repo
update_existing_repo_with_pr
```

`update_existing_repo_with_pr` 应：

```text
clone existing repo
create branch
apply transform
write manifest/report
open PR
wait for CI/review
```

### 14. 归属还应迁到平台工程

当前仓库在：

```text
FutureTechQuant/Clone-ruoyi-vue-pro-Bot
```

长期建议：

```text
Business-Unit-for-Platform/clone-ruoyi-vue-pro-bot
```

原因：

- 它是平台级生成器，不属于某个单一业务。
- 后续会服务高考、Debet、新粮、相亲、同伴游等多个业务。
- 应与 `codegen-bot`、`ruoyi-vue-pro-base` 共同纳入平台工程资产。

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
