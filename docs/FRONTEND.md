# FRONTEND

## 范围
本文分析 `wms-web` 的技术选型、目录结构、运行方式、权限模型与当前维护重点。

## 技术栈
- Vue 2.7
- Vue CLI 4
- Vue Router 3
- Vuex 3
- Element UI 2
- Axios
- VXETable
- Sass

## 启动与构建
### 常用命令
```bash
cd wms-web
npm install --legacy-peer-deps
npm run dev
```

其他脚本：

- `npm run local`
- `npm run front`
- `npm run build:prod`
- `npm run build:stage`
- `npm run build:dev`
- `npm run build:static`

### 环境文件
- `.env.dev`
- `.env.prod`
- `.env.stage`
- `.env.static`

当前代码中前端最关键的环境变量包括：

- `VUE_APP_BASE_API`：后端基础地址
- `VUE_APP_TENANT_ENABLE`：多租户开关
- `VUE_APP_CAPTCHA_ENABLE`：验证码开关
- `VUE_APP_DOC_ENABLE`：文档入口开关
- `VUE_APP_SOCKET_SERVER`：通知 websocket 地址

## 目录结构
### 应用核心
- `src/main.js`：应用入口，集中注册全局组件、工具方法、指令和插件。
- `src/router`：静态路由与动态路由承载入口。
- `src/store`：用户、权限、字典、通知、全局配置等状态管理。
- `src/utils/request.js`：统一请求封装。
- `src/permission.js`：登录态校验与路由守卫。

### 业务目录
- `src/views/system`：系统管理页面
- `src/views/infra`：基础设施页面
- `src/views/bpm`：流程页面
- `src/views/report`：报表页面
- `src/views/mall`：商城相关页面
- `src/views/wh`：WMS 业务主页面

### WMS 页面重点
`src/views/wh` 下已形成较稳定的业务域拆分：

- `goods`、`item`、`material`、`product`
- `order`、`sale`、`finance`
- `counting`、`customerRetain`
- `merchant`、`maintain`
- 多个仓储/销售统计报表目录

与之对应，`src/api/wh` 也按同样的业务域进行接口拆分，属于较清晰的前后端对齐方式。

## 启动流程
### 应用初始化
- `main.js` 中注册 Element UI、VXETable、全局工具函数、字典组件、打印能力和自定义表单组件。
- 项目有较多 `Vue.prototype` 级别挂载，说明当前前端偏“传统 Vue 2 全局能力注入”风格。

### 路由与权限
- 公共路由定义在 `src/router/index.js`。
- 登录后在 `permission.js` 中调用 `GetInfo` 与 `GenerateRoutes` 注入业务路由。
- 如果权限中包含特定按钮码，首页还会动态追加快捷入口。

### 基础数据预加载
首次登录成功后，前端会批量拉取以下基础数据并缓存到 store：

- 部门
- 网点
- 仓库
- 库位
- 大类 / 品类 / 子品类
- 材质
- 客户 / 供应商
- 业务分区 / 业务员
- 账户、结算类型、生产工艺等

这意味着很多页面默认依赖“先全量预热，再开始操作”的模式。

## 请求层约定
- 请求基地址为 `${VUE_APP_BASE_API}/admin-api/`。
- 默认在请求头中附带页面路径、标题和名称，便于后端记录操作上下文。
- 登录过期时，前端内置 refresh token 刷新逻辑。
- 401、500、演示模式、重复登录等状态有专门处理逻辑。

## 组件生态特征
- 表格能力大量依赖 `vxe-table`，适合复杂表格、导出、键盘交互和大数据量场景。
- UI 主体仍是 `element-ui`，整体是典型管理后台范式。
- 存在较多仓储专用组件，例如打印、扫码、库存处理、自定义表单、上传和标签组件。

## 构建与代理
- `vue.config.js` 中开发代理默认将 `/admin-api` 转发到 `http://120.77.247.123:8091`。
- 构建默认启用 gzip 压缩、Terser 去掉 `console/debugger`、SVG Sprite、分包优化。
- 生产包默认关闭 `sourceMap`。

## 当前前端特征与风险
### 优点
- 业务目录比较完整，`views/wh` 与 `api/wh` 的对应关系清晰。
- 请求层、权限层、全局字典与基础数据加载有统一入口。
- 针对复杂业务表格、打印和扫码场景已有较强的现成能力。

### 风险
- `main.js` 体量较大，很多全局注册与工具注入集中在一个文件中，维护成本偏高。
- 首次登录的预加载数据较多，可能影响首屏进入速度。
- Vue 2 + Element UI + 大量全局混入/原型扩展，后续升级 Vue 3 成本会比较高。
- 开发代理和环境文件中仍存在较强的环境耦合，部署切换时容易出现配置漂移。

## 维护建议
- 新页面优先放到对应业务域目录，接口文件与页面目录保持一致。
- 降低 `main.js` 的全局注册密度，可逐步拆出 `bootstrap`、`global-components`、`global-utils`。
- 对“全量预加载”的基础数据做分级，区分必须预热与按需加载。
- 为 `views/wh` 下高频页面补充业务说明，降低新成员理解成本。
