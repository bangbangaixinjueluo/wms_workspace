# DESIGN

## 文档目的
本文基于当前仓库代码与现有业务资料，描述 `wms-web` 与 `wms-api` 的整体设计、模块边界、主要集成点以及文档导航。后续做需求评审、问题定位、方案设计时，优先以本文作为总览入口。

## 资料优先级
后续在本仓库继续补文档或实现功能时，建议遵循以下资料优先级：

1. `docs/business/*`：业务逻辑与领域规则主资料
2. `docs/CODE_RULES.md`：后端编码规范主资料
3. 实际代码与配置：用于验证现状与发现偏差
4. 本目录下的总览与索引文档：用于导航与串联

## 仓库结构总览
- `wms-web`：Vue 2 管理后台，负责登录、权限路由、业务页面、打印与扫码相关前端交互。
- `wms-api`：Spring Boot 2.7 + Maven 多模块后端，提供 `admin-api` 接口、权限体系、工作流、报表与仓储业务能力。
- `docs`：项目知识沉淀目录。当前已补充前端、后端、可靠性、安全、质量评估等文档。

## 总体架构
### 前端
- 技术栈：Vue 2.7、Vue CLI 4、Vuex、Vue Router、Element UI、VXETable、Axios。
- 应用入口：`wms-web/src/main.js`。
- 路由模型：静态公共路由 + 登录后按权限动态生成业务路由。
- 请求约定：统一通过 `wms-web/src/utils/request.js` 发起，默认访问 `${VUE_APP_BASE_API}/admin-api/`。

### 后端
- 技术栈：Java 8、Spring Boot 2.7.8、MyBatis Plus、Dynamic Datasource、Redis、Quartz、Flowable、Knife4j。
- 启动入口：`wms-api/yudao-server/src/main/java/cn/iocoder/yudao/server/YudaoServerApplication.java`。
- 架构形态：`yudao-server` 作为聚合启动容器，按 Maven Module 挂载系统、基础设施、报表、工作流、商城和 WMS 业务模块。
- API 前缀：代码与前端约定的核心管理接口前缀为 `/admin-api/`。

## 关键业务域
从前后端目录可以看出，当前项目并非纯通用后台，而是“Yudao/RuoYi Pro 底座 + 定制化仓储/加工/销售业务系统”：

- 平台域：系统管理、基础设施、工作流、报表、商城。
- WMS 域：`wh` 模块下的库存、订单、商品、物料、财务、客户留存、盘点、调拨、报表等能力。
- 定制扩展域：`ulinkle-module-basic` 和 `ulinkle-module-semis`，承载项目私有业务扩展。

## 运行链路
### 登录与权限
1. 前端通过 `login / socialLogin / smsLogin / utLogin` 获取访问令牌。
2. Axios 请求头附带 `Authorization: Bearer <token>`。
3. 登录成功后，前端依次拉取用户信息、字典数据、部门/仓库/客户/供应商等基础数据。
4. 后端基于 Spring Security 与权限数据返回可访问菜单、按钮与接口权限。

### 页面访问
1. 浏览器进入 Vue 应用。
2. `permission.js` 判断是否已有 token。
3. 首次登录后触发 `GenerateRoutes`，将权限路由注入前端路由表。
4. 页面上的按钮权限和首页快捷入口，依赖接口权限码进一步控制。

### 数据交互
1. 前端统一请求 `/admin-api/**`。
2. 后端由 `yudao-server` 汇聚各业务模块 Controller。
3. 数据层基于 MyBatis Plus 与动态数据源访问 MySQL，缓存与分布式能力依赖 Redis。

## 主要集成点
- Redis：缓存、验证码、状态、分布式能力。
- Quartz：定时任务调度。
- Flowable：流程引擎。
- Knife4j / Swagger：接口文档。
- WebSocket：消息推送与通知能力。
- 第三方登录：钉钉、企业微信、微信小程序。
- 外部业务同步：配置中可见 `laomiao`、`semis` 等外部服务集成。

## 当前设计特征
- 前端是单体 SPA，但业务目录按领域拆分较清晰，`views/wh` 与 `api/wh` 已形成对应关系。
- 后端是模块化单体，不是微服务；模块边界清楚，但最终仍由一个 Spring Boot 进程承载。
- 项目沿用了 Yudao/RuoYi Pro 的基础能力，同时在 WMS 业务和企业私有流程上做了大量定制。
- 文档与代码此前存在脱节，`docs` 中不少文件是模板性质，本次已开始按真实代码结构回填。

## 建议的文档阅读顺序
1. [FRONTEND.md](G:\wms_workspace\docs\FRONTEND.md)
2. [BACKEND.md](G:\wms_workspace\docs\BACKEND.md)
3. [业务逻辑总览](G:\wms_workspace\docs\business\overview.md)
4. [后端编码规范](G:\wms_workspace\docs\CODE_RULES.md)
5. [PRODUCT_SENSE.md](G:\wms_workspace\docs\PRODUCT_SENSE.md)
6. [RELIABILITY.md](G:\wms_workspace\docs\RELIABILITY.md)
7. [SECURITY.md](G:\wms_workspace\docs\SECURITY.md)
8. [QUALITY_SCORE.md](G:\wms_workspace\docs\QUALITY_SCORE.md)

## 后续建议
- 单独补一份 `docs/API_CONVENTIONS.md`，固化接口返回结构、错误码、分页和导出约定。
- 单独补一份 `docs/DEPLOYMENT.md`，沉淀前后端部署、环境变量、Nginx/进程启动方式。
- 为 `wms-web/src/views/wh` 和 `wms-api/yudao-module-wh` 建立按业务域拆分的专题文档，降低新成员理解成本。
