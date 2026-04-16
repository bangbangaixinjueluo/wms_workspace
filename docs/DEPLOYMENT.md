# DEPLOYMENT

## 目的
本文记录当前仓库可见的前后端启动、构建和联调要点，帮助开发者快速跑通环境。

## 前端 `wms-web`
### 安装依赖
```bash
cd wms-web
npm install --legacy-peer-deps
```

### 启动方式
```bash
npm run dev
```

可选脚本：
- `npm run local`
- `npm run front`
- `npm run build:prod`
- `npm run build:stage`
- `npm run build:dev`
- `npm run build:static`

### 关键环境变量
- `VUE_APP_BASE_API`
- `VUE_APP_TENANT_ENABLE`
- `VUE_APP_CAPTCHA_ENABLE`
- `VUE_APP_DOC_ENABLE`
- `VUE_APP_SOCKET_SERVER`

### 开发代理
- `vue.config.js` 中当前开发代理会把 `/admin-api` 转发到 `http://120.77.247.123:8091`
- 若本地后端自行启动，通常需要同步调整代理目标或环境变量

## 后端 `wms-api`
### 启动方式
```bash
cd wms-api
mvn -pl yudao-server -am spring-boot:run
```

或先打包：
```bash
mvn -pl yudao-server -am clean package -DskipTests
java -jar yudao-server/target/yudao-server.jar
```

### 配置文件
- 通用配置：`yudao-server/src/main/resources/application.yaml`
- 环境配置：
  - `application-local.yaml`
  - `application-dev.yaml`
  - `application-prod.yaml`
  - `application-uat.yaml`
  - `application-test.yaml`

### 默认端口
- 本地配置文件可见默认端口为 `48080`

### 接口文档
- 历史说明中使用 `http://127.0.0.1:48080/doc.html`

## 本地联调建议
### 推荐流程
1. 先确认后端 profile 与数据库、Redis 是否可用
2. 启动后端 `yudao-server`
3. 根据本地实际接口地址调整前端 `VUE_APP_BASE_API` 或开发代理
4. 启动前端并登录验证
5. 检查用户信息、菜单、字典和仓库基础数据是否能正常加载

### 首轮联调检查项
- 登录是否成功
- `GetInfo` 是否能返回用户、角色、权限
- 动态菜单是否生成
- 字典数据是否正常加载
- 仓库、库位、客户、供应商等基础数据是否正常预加载
- `doc.html` 是否可访问

## 当前阻碍点
- 后端环境文件中绑定了较多实际资源地址
- 前端开发代理也默认指向固定远程地址
- 不同开发环境下如果不先梳理配置，容易出现“前端起来了但实际没连到目标后端”的情况

## 建议后续治理
- 将真实资源地址迁移到部署环境变量或本地私有配置
- 为前端补 `env.example` 或本地联调说明
- 为后端补 profile 差异说明和最小依赖清单
