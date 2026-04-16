# BACKEND

## 范围
本文分析 `wms-api` 的技术栈、模块结构、启动方式、配置入口和当前风险点，帮助开发者快速定位后端代码。

## 规范入口
后端实现与评审时，应优先参考 [CODE_RULES.md](G:\wms_workspace\docs\CODE_RULES.md)。

当前可明确提炼出的核心约束包括：

- 分层以 `controller -> biz -> service -> mapper` 为主
- `biz` 用于业务编排、跨模块调用和外部系统适配
- `service` 负责本域业务规则、事务和状态流转
- `mapper` 只负责数据访问
- 新代码默认使用构造器注入
- Controller 不直连 Mapper，VO/DO/DTO 需要分层隔离

## 技术栈
- Java 8
- Spring Boot 2.7.8
- Maven 多模块
- MyBatis Plus
- Dynamic Datasource + Druid
- Redis
- Quartz
- Flowable
- Knife4j / SpringDoc

## 模块结构
根 `pom.xml` 当前聚合了以下核心模块：

- `yudao-server`：启动容器与最终可运行 Jar。
- `yudao-framework`：通用框架扩展与基础 starter。
- `yudao-dependencies`：依赖版本管理。
- `yudao-module-system`：系统管理能力。
- `yudao-module-infra`：基础设施能力。
- `yudao-module-bpm`：流程引擎相关能力。
- `yudao-module-report`：报表能力。
- `yudao-module-mall`：商城相关能力。
- `yudao-module-member`：会员相关能力。
- `yudao-module-wh`：WMS 主业务模块。
- `ulinkle-module-basic`：项目自定义基础业务扩展。
- `ulinkle-module-semis`：半成品/专项业务扩展模块。

## 启动方式
### 应用入口
- 启动类：`wms-api/yudao-server/src/main/java/cn/iocoder/yudao/server/YudaoServerApplication.java`
- 扫描范围：`${yudao.info.base-package}.server` 与 `${yudao.info.base-package}.module`

### 本地常用方式
```bash
cd wms-api
mvn -pl yudao-server -am spring-boot:run
```

或先打包：

```bash
cd wms-api
mvn -pl yudao-server -am clean package -DskipTests
java -jar yudao-server/target/yudao-server.jar
```

## 配置结构
### 通用配置
- `yudao-server/src/main/resources/application.yaml`
  - 激活 profile
  - Swagger / Knife4j
  - Flowable
  - MyBatis Plus
  - WebSocket
  - 租户开关
  - 验证码与错误码配置

### 环境配置
- `application-local.yaml`
- `application-dev.yaml`
- `application-prod.yaml`
- `application-uat.yaml`
- `application-test.yaml`
- 以及其他自定义 profile

### 关键约定
- 服务端口在 `application-local.yaml` 中可见默认为 `48080`。
- 前端默认通过 `/admin-api/` 前缀访问后端。
- Swagger/Knife4j 已启用，历史 README 中使用 `http://127.0.0.1:48080/doc.html`。

## 代码组织方式
从 `yudao-module-wh` 可见，WMS 模块大体遵循分层结构：

- `controller`：接口层
- `service`：业务逻辑
- `dal`：数据访问
- `convert`：DO / VO / DTO 转换
- `job`：定时任务
- `mq`：消息队列
- `sync`：外部系统同步
- `sse`：服务端事件推送
- `components` / `framework` / `util`：公共支撑能力

`ulinkle-module-basic` 也基本沿用相同套路，说明项目在定制模块上尽量保持了与主干一致的分层方式。

## 业务重心判断
结合模块名、前端目录和 SQL 脚本，后端当前重点集中在：

- 仓储商品与物料管理
- 销售、订单、财务与库存联动
- 盘点、调拨、留存、报表
- 工作流审批与基础系统能力
- 若干企业私有业务同步接口

## 接口与前端协同方式
- 前端 axios 默认将请求发往 `${VUE_APP_BASE_API}/admin-api/`。
- 登录态通过 Bearer Token 传递。
- 租户模式在配置中存在，但当前前后端均默认关闭。
- 项目保留了大量字典、菜单、权限码驱动的页面行为，这意味着接口字段与权限标识对前端影响很大。

## 数据与脚本
- `sql/01_create_system.sql`、`02_create_wh.sql` 等文件用于基础建表与初始化。
- `sql/update/`、`sql/version/` 累积了大量增量脚本，能反映项目演进历史。
- `sql/report/` 提供报表相关视图和过程脚本。

## 当前观察到的后端风险
### 配置安全
- 多个环境配置文件中存在明文数据库、Redis、第三方服务凭据。
- 这些信息不应继续作为仓库内长期配置基线，建议迁移到环境变量、配置中心或部署系统密文中。

### 测试策略较弱
- 根 `pom.xml` 中 `maven-surefire-plugin` 默认 `skip=true`。
- 这意味着构建过程默认不执行单测，回归风险主要依赖人工验证。

### 环境耦合偏强
- `application-local.yaml` 中直接写入了具体外部地址与账号，说明开发、测试、线上环境边界可能不够清晰。
- 一旦更换数据库、Redis 或第三方服务地址，迁移成本会比较高。

### 模块规模持续膨胀
- 当前是模块化单体，业务域很多，若继续把私有逻辑堆在 `wh/basic` 模块内，后续定位问题和编译效率都会下降。

## 建议的维护原则
- 新业务优先落在已有模块域中，避免横切散落。
- Controller 只做参数接收和响应封装，业务规则集中在 Service。
- 外部系统同步逻辑与主业务解耦，优先放到 `sync`、`job` 或独立适配层。
- 变更数据库脚本时同步维护 `sql/update` 或 `sql/version`，避免仅改代码不留迁移记录。

## 建议补充的后续文档
- `docs/DB_CHANGE_GUIDE.md`：数据库变更流程与脚本命名规范。
- `docs/INTEGRATIONS.md`：老庙、半成品、微信、钉钉等外部系统集成说明。
- `docs/BACKEND_TESTING.md`：单测、接口测试、联调回归清单。
