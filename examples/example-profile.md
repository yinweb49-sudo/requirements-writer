# 轻记 LightDiary · 需求文档编写项目档案（requirements-profile）

> **这是一份虚构的示例档案**，仅用于演示 `requirements-profile.md` 该怎么填。
> 里面的产品、服务名、端口、路径都是编造的，请勿照抄——按 `references/project-profile-template.md` 填你自己项目的真实值。
> 本例特意采用“桌面前端 + 本地后端 + 云端后端 + 双数据库 + 订阅计费”的较复杂形态，方便展示模板的完整结构；你的项目更简单就删掉用不上的层。

## 基本信息

- 项目名称：轻记 LightDiary（虚构·离线优先的桌面日记 / 手账应用）
- 仓库根说明文件：`README.md` + `docs/架构总览.md`
- 产品视角 / 沟通约定：面向重视隐私的个人用户，先讲“做完能干什么”，再讲实现

## 架构分层

| 占位符 | 本项目对应 | 说明 |
|---|---|---|
| `{{前端}}` | `desktop-web`（Electron + React） | 桌面客户端前端，dev 端口 5180 |
| `{{本地后端}}` | `local-api`（`RunMode=Offline`） | 本地 SQLite，端口 8790，负责离线读写与本地检索 |
| `{{云端后端}}` | `cloud-api`（`RunMode=Online`） | 云端 PostgreSQL，端口 8890，负责账号登录、会员订阅计费、云同步、AI 润色网关 |
| `{{运行档位机制}}` | `RunMode` 开关（Offline / Online） | 同一套后端代码，切数据库 / 服务注册 / 路由白名单 |

## 数据库

| 占位符 | 本项目对应 |
|---|---|
| `{{主库}}` | SQLite（客户端本地，离线档位） |
| `{{次库}}` | PostgreSQL（云端，在线档位） |

- 用户隔离字段：`TenantId` / `UserId`
- 加列 / 建表约定：SQLite 幂等建表或 `ALTER ADD COLUMN`；云端迁移用 EF Core migrations，执行时显式指定 Online 档位连接串，避免按本地档位生成错误迁移

## 鉴权与白名单术语

- `{{鉴权方案}}`：`Bearer(User)`（登录用户）/ `Bearer(DeviceToken)`（本地设备令牌）
- `{{白名单机制}}`：`OfflineRouteFilter` / `OnlineRouteFilter`（按运行档位暴露路由；某接口在一个档位 404 属正常）
- 计费机制：会员订阅（按月 / 按年，云端结算）

## 验收 / 调试方式

- `{{验收方式}}`：`npm run dev:desktop` —— 一键起 本地后端 8790 + 云端后端 8890 + 前端 dev 5180 + Electron 窗口，所见即打包后真实形态

## 结构检索工具

- `{{结构检索工具}}`：grep + 文件读取（若项目接入了代码索引工具，改写成对应工具名）

## 样本文档

- 主需求文档样本：`docs/云同步/云同步-开发需求文档.md`
- 分期提示词样本：
  - `docs/云同步/开发提示词/README.md`
  - `docs/云同步/开发提示词/期1-本地草稿与冲突表骨架.md`
  - `docs/云同步/开发提示词/期3-云端增量同步.md`

## 文档产出位置约定

- 默认放：`docs/<功能名称>/`

## 端口 / 目录约定

- 前端 dev 5180 · 本地后端 8790 · 云端后端 8890
- 前端构建产物写入 `local-api/wwwroot`；云端管理台产物进入 `cloud-api/wwwroot/admin`
