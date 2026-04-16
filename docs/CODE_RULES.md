# 编码规范

## 1. 目标与适用范围

本文档用于统一业务模块的编码方式，适用于当前仓库内基于 Spring Boot + MyBatis Plus + Yudao 框架的后端业务开发。

本规范不是通用 Java 教科书，而是基于当前成品代码的实际实现方式抽象出的“项目内统一写法”。新功能、改造、缺陷修复均应优先遵循本文档；遗留代码与本文档不一致时，新增代码不得继续放大历史问题。

当前仓库已经形成的主干分层是：

1. `controller`
2. `biz`
3. `service`
4. `mapper`

对应关系不是“所有模块都必须四层齐全”，而是：

1. 纯本模块 CRUD 场景可以只用 `controller -> service -> mapper`
2. 跨模块编排、外部 API 转接、组合查询、业务口径适配场景，必须引入 `biz`
3. `biz` 不是 `service` 的别名，它承担“业务编排层”的职责

---

## 2. 总体设计原则

### 2.1 单一职责

每层只做自己该做的事：

1. `controller` 负责 HTTP 协议适配、鉴权注解、参数接收、结果返回
2. `biz` 负责业务编排、跨模块调用、聚合转换、业务口径收敛
3. `service` 负责本域核心业务规则、事务、状态变更、持久化协调
4. `mapper` 负责数据访问，不承载业务判断。如需联表优先使用MPJ（mybatis-plus-join）

### 2.2 面向稳定边界编程

1. Controller 不直接依赖 Mapper
2. Controller 不直接写复杂业务逻辑
3. Biz 不直接操作数据库（不能直接注入mapper，而是注入对应的service），除非是明确归属本域且没有 Service 层的遗留模块改造过程；新代码禁止这样写
4. Service 不直接调用外部系统 API，必须通过 Biz 做防腐层封装（不能注入service，而是mapper ）
5. Mapper 不返回PageResult
6. 外部系统 DTO、内部 DO、前端 VO 必须分离

### 2.3 先复用框架能力，再补自定义代码

当前仓库已经有公共能力：

1. `CommonResult`
2. `PageResult`
3. `ExcelUtils`
4. `ServletUtils`
5. `CollectionUtils`
6. `BigDecimalUtils`
7. `ServiceExceptionUtil`
8. `BaseMapperX`

新代码应优先复用这些能力，禁止重复发明同类工具。

---

## 3. 标准分层规则

## 3.1 Controller 规范

### 3.1.1 职责

Controller 只做以下事情：

1. 声明路由、接口文档、权限注解
2. 接收 `@RequestBody`、`@RequestParam`、分页查询参数
3. 参数校验
4. 调用 `biz` 或 `service`
5. 返回 `CommonResult` 或直接输出文件流

Controller 禁止做以下事情：

1. 写业务判断链
2. 写数据库查询条件组装
3. 直接调用 `mapper`
4. 在方法里拼接复杂返回对象
5. 手写 try-catch 吞异常

### 3.1.2 标准写法

1. `@RestController`
2. `@RequestMapping`
3. `@Validated`
4. `@RequiredArgsConstructor`
5. `private final`

返回值统一：

1. 普通 JSON 接口返回 `CommonResult<T>`
2. 分页接口返回 `CommonResult<PageResult<T>>`
3. 导出接口返回 `void`，通过 `HttpServletResponse` 输出

### 3.1.3 依赖选择

1. 跨模块查询、跨系统适配、口径转换，Controller 调 `biz`
2. 单模块标准增删改查，Controller 调 `service`
3. 一个 Controller 不要同时混调多个 `biz/service` 完成复杂编排，复杂编排下沉到 `biz`

---

## 3.2 Biz 规范

### 3.2.1 Biz 的定位

`biz` 是业务编排层，不是“另一个 service”。满足以下任一条件时应建 `biz`：

1. 需要调用外部模块 API
2. 需要把外部 DTO 转成当前模块 VO
3. 需要聚合多个 service / api 的结果
4. 需要对请求参数做业务口径预处理
5. 需要屏蔽上下游差异，对 Controller 暴露稳定语义

### 3.2.2 Biz 应做的事

1. 参数预处理
2. 业务编排
3. 外部系统调用
4. 聚合结果
5. 视图对象转换
6. 对外部能力做防腐层封装

### 3.2.3 Biz 不应做的事

1. 大量直接 SQL 操作
2. 本域数据写入细节散落
3. 把 Mapper 当 Repository 直接使用
4. 与 Controller 共用同一个 VO 进行内部流转和落库

### 3.2.4 事务边界

1. 纯查询型 Biz 不加事务
2. 跨多个本域 Service 写操作的编排型 Biz，可以加 `@Transactional(rollbackFor = Exception.class)`
3. 若事务只涉及单个本域 Service，则事务优先放在 Service 层

结论：事务边界要以“最小可闭环业务动作”为准，不以层名机械决定。

---

## 3.3 Service 规范

### 3.3.1 职责

Service 是本域核心业务规则层，负责：

1. 本模块核心校验
2. 状态流转
3. 新增、修改、删除
4. 多表一致性
5. 乐观锁、幂等、并发更新控制
6. 与 Mapper 协调完成持久化

### 3.3.2 Service 设计要求

1. 接口与实现分离：`XXXService` + `XXXServiceImpl`
2. 实现类使用 `@Service`、`@Validated`
3. 写操作默认评估是否需要 `@Transactional(rollbackFor = Exception.class)`
4. Service 不返回前端 VO，优先返回 DO / DTO / 领域结果对象

### 3.3.3 Service 中允许的逻辑

1. 状态校验
2. 唯一性校验
3. 乐观锁更新
4. 批量汇总计算
5. 本域数据修正
6. 事务内写库顺序控制

例如 ：

1. 先聚合同 SKU 数据
2. 再按版本号更新
3. 更新失败时立即断言
4. 最后落明细日志

这类“本域一致性逻辑”应保留在 Service，不应上推到 Controller。

---

## 3.4 Mapper 规范

### 3.4.1 Mapper 职责

Mapper 只负责数据访问：

1. 单表 CRUD
2. 基于条件构建查询
3. 必要的定制 SQL
4. 批量写入

### 3.4.2 标准写法

默认继承：

1. `BaseMapperX<DO>`

默认使用：

1. `@Mapper`
2. MyBatis Plus `Wrappers.lambdaQuery/lambdaUpdate`
3. MPJ（mybatis-plus-join）进行多表联查

### 3.4.3 Mapper 禁止项

1. 在 XML 或注解 SQL 中硬编码业务状态语义说明
2. 在 Mapper 层做 if/else 业务判断
3. 返回前端 VO
4. 混入跨模块业务口径

### 3.4.4 XML 使用原则

能用 `BaseMapperX + Wrapper` 解决的，优先不用 XML。

只有以下情况才写 XML：

1. 多表复杂联查
2. 动态列聚合
3. 统计报表型 SQL
4. MyBatis Plus Wrapper 可读性明显下降

写 XML 时必须：

1. `namespace` 与 Mapper 接口保持一致
2. SQL 只描述数据获取逻辑，不承载业务规则说明
3. 条件字段和排序字段显式写清楚

---

## 4. 对象模型规范

## 4.1 对象分层

对象必须按职责区分：

1. `ReqVO / CreateReqVO / UpdateReqVO / PageReqVO / ExportReqVO`：Controller 入参
2. `RespVO / BaseVO / InfoVO / ExcelVO`：Controller 出参
3. `DO`：数据库实体
4. `DTO`：跨模块或外部系统传输对象
5. `Params`：远程调用或查询条件封装

禁止：

1. 用 DO 直接做前端返回
2. 用 VO 直接入库
3. 用外部系统 DTO 在 Controller 直接透出

## 4.2 转换规范

当前仓库同时存在两类转换方式：

1. 简单平铺字段转换：`BeanUtil`
2. 稳定批量转换、分页转换：MapStruct `Convert.INSTANCE`

统一规则如下：

1. 单对象、字段几乎一致、一次性适配，可用 `BeanUtil.toBean/copyProperties`
2. 列表、分页、结构稳定、后续会演化的转换，优先使用 MapStruct Convert
3. Controller 层返回分页对象时，优先使用 `Convert.INSTANCE.convertPage(...)`
4. 不允许在 Controller 方法体中手工 `new VO()` 挨个 set 大量字段

结论：短平快转换用 `BeanUtil`，正式边界转换用 `MapStruct`。

---

## 5. 注入规范

## 5.1 默认规范

业务代码默认统一使用构造器注入：

1. `@RequiredArgsConstructor`
2. `private final`

适用层：

1. `controller`
2. `biz`
3. `service`
4. `api impl`
5. `job`
6. 大部分 `component`

原因：

1. 依赖清晰
2. 可测试性更好
3. 避免字段注入隐藏依赖
4. 与当前半成品模块既有写法一致

## 5.2 允许使用 `@Resource` 的场景

以下场景可以保留或使用 `@Resource`：

1. 框架 SPI 或特殊生命周期注入
2. 多实现按名称注入
3. 老模块已大面积采用字段注入，且本次改动不触达整体重构

但新写业务模块默认不要再用 `@Autowired` 字段注入。

统一要求：

1. 新增业务代码禁止使用 `@Autowired` 字段注入
2. 没有特殊原因时不要混用 `@Resource` 和 `@RequiredArgsConstructor`

---

## 6. 参数校验与异常规范

## 6.1 参数校验

1. Controller 类上加 `@Validated`
2. `@RequestBody` 参数使用 `@Valid`
3. 基础空值、列表为空、非法状态校验，优先在进入业务逻辑早期完成

职责划分：

1. 协议层合法性：Controller + Bean Validation
2. 业务合法性：Biz / Service

## 6.2 异常规范

统一原则：

1. 业务异常优先使用 `ServiceExceptionUtil.exception(...)`
2. 基础断言可使用 `Assert.notEmpty`、`Assert.isTrue`
3. 不要在业务代码里随意 `throw new RuntimeException("xxx")`

推荐口径：

1. 可复用、需统一错误码的场景，定义 `ErrorCodeConstants`
2. 临时性、本地断言型失败，可使用 `Assert`
3. Controller 不主动 try-catch 包装业务异常，交给全局异常处理器

## 6.3 日志与异常配合

1. 能抛异常结束流程的，不要先 `log.error` 再继续执行
2. 吞异常后返回空对象只适用于明确允许降级的外部调用
3. 外部系统调用失败若被降级，必须打印足够上下文

---

## 7. 工具类统一使用规范

## 7.1 集合处理

优先级如下：

1. 空判：`CollUtil` / `CollectionUtils`
2. 转 List/Set/Map：`CollectionUtils`
3. 固定小集合构造：`ListUtil.of`

规范：

1. 公共批量转换优先复用 `CollectionUtils.convertList/convertMap/convertSet`
2. 只有在 merge 规则特殊时才手写 `stream().collect(...)`
3. 业务代码里不要重复封装一套同义集合工具

## 7.2 BigDecimal 处理

金额、数量、重量等数值计算统一优先使用 `BigDecimalUtils`：

1. 加法：`BigDecimalUtils.add`
2. 减法：`BigDecimalUtils.subtract`
3. 乘法：`BigDecimalUtils.multiply`
4. 除法：`BigDecimalUtils.divide`
5. 判空判零：`isNullOrZero / nonNullAndZero`

禁止：

1. `new BigDecimal(double)`
2. 同一模块里混用不同舍入规则且没有注释
3. 每个 Service 自己写一套金额工具

## 7.3 JSON 处理

优先使用项目已存在的 JSON 能力；当前半成品对外接口已使用 Hutool JSON，则同一链路内可以保持一致。

规则：

1. 同一业务链路不要同时混用 2 到 3 套 JSON 风格
2. 对外协议对象优先定义 DTO，不要长期依赖裸 `JSONObject`
3. `JSONObject` 只适合外部接口临时过渡层

## 7.4 Bean 转换

1. 简单复制用 `BeanUtil`
2. 正式转换用 `Convert`
3. 不要在多个类里复制同一段字段映射代码

## 7.5 Servlet / 导出 / 下载

统一使用：

1. JSON 响应：`ServletUtils.writeJSON`
2. 二进制附件：`ServletUtils.writeAttachment`
3. Excel 导出：`ExcelUtils.write / writeWithTitle / writeEmptyZero`

禁止在 Controller 中手动拼响应头、自己操作复杂导出细节，除非框架能力无法满足。

## 7.6 字符串处理

1. 使用`StrUtil`

## 7.7 判断处理

1. 简单判断使用`Optional`

---

## 8. 流式处理规范

## 8.1 当前项目已落地的流式处理定义

当前仓库没有形成 `SseEmitter`、`WebFlux Flux`、`StreamingResponseBody` 这类实时流式接口规范。

当前项目内“流式处理”主要指：

1. Excel 导出写 `response.getOutputStream()`
2. 文件下载写 `response.getOutputStream()`
3. 外部 HTTP 响应流读取

所以新代码必须先遵循现有输出流规范，而不是随意引入新的实时流技术栈。

## 8.2 导出下载规范

1. 优先走 `ExcelUtils` / `ServletUtils`
2. Controller 只负责调用导出服务并把结果写出
3. 不要在业务方法里关闭 `response` 的输出流
4. `EasyExcel` 一类工具必须设置 `autoCloseStream(false)`，由 Servlet 容器托管响应流
5. 导出接口必须考虑大数据量场景，避免先把超大 byte[] 全量堆内存再返回

## 8.3 外部流读取规范

1. 必须设置超时时间
2. 必须在 `finally` 或 try-with-resources 中关闭输入流
3. 不允许把密钥、Token、AppCode 硬编码在 Service 里
4. 新代码优先使用统一 HTTP Client，不再新增 `HttpURLConnection` 风格直连代码

## 8.4 若后续新增实时流接口

若未来确实引入 SSE、长连接推送、AI 流式返回，必须新增专项规范，至少包含：

1. 连接超时和心跳
2. 客户端断连处理
3. 消息幂等与重放策略
4. 线程模型
5. 限流和资源回收

在专项规范落地前，业务模块禁止自行引入实时流式接口。

---

## 9. 事务、并发与状态一致性规范

## 9.1 事务使用规则

默认规则：

1. 只读查询不加事务
2. 单个业务动作涉及多次写库时，加 `@Transactional(rollbackFor = Exception.class)`
3. 事务尽量放在真正完成业务闭环的方法上

禁止：

1. 为了“保险”在整个类上乱加事务
2. 在 Controller 上加事务
3. 事务方法内部再用同类 `this.xxx()` 期待事务生效

## 9.2 并发更新

涉及库存、汇总、状态推进时，必须显式考虑并发。

优先顺序：

1. 乐观锁版本号
2. 条件更新
3. 分布式锁
4. 唯一索引兜底

像 `PickingSummaryServiceImpl` 这种按 `version` 更新并校验返回行数的写法，是推荐模式。

## 9.3 状态变更规则

1. 状态流转必须在 Service / Biz 中集中处理
2. 状态可进入集合要显式枚举，不要靠注释约定
3. 非法状态变更必须中断流程并抛业务异常

---

## 10. 日志规范

1. 入口级关键业务动作打印 `info`
2. 外部调用失败打印 `error`
3. 日志必须带关键业务主键，如单号、ID、skuNo、transferNo
4. 不要打印整块超大对象、二进制内容、敏感信息

推荐格式：

1. 动作 + 主键 + 关键参数
2. 外部调用 + endpoint + requestId + 结果摘要

禁止：

1. 无意义日志，如“进入方法”“执行完成”
2. 大循环内逐条刷屏日志
3. 把密码、密钥、token 打到日志里

---

## 11. 命名规范

## 11.1 类命名

1. Controller：`XXXController`
2. Biz：`XXXBusiness`
3. Service 接口：`XXXService`
4. Service 实现：`XXXServiceImpl`
5. Mapper：`XXXMapper`
6. 转换器：`XXXConvert`
7. 数据对象：`XXXDO`
8. 传输对象：`XXXDTO`
9. 请求对象：`XXXReqVO / XXXCreateReqVO / XXXUpdateReqVO / XXXPageReqVO`
10. 返回对象：`XXXRespVO / XXXBaseVO / XXXInfoVO / XXXExcelVO`

## 11.2 方法命名

查询类：

1. `getById`
2. `getByNo`
3. `getInfo`
4. `getPage`
5. `listByIds`

写操作类：

1. `create`
2. `update`
3. `delete`
4. `receive`
5. `dismiss`
6. `sync`
7. `deduction`
8. `increase`

命名要求：

1. 用业务语义，不用 `doSomething`
2. 批量方法名体现批量语义，如 `getInfos`、`listBy...`

---

## 12. 禁止项清单

以下写法在新代码中禁止出现：

1. Controller 直接调用 Mapper
2. Controller 写复杂业务编排
3. VO 直接入库，DO 直接出参
4. `@Autowired` 字段注入
5. Service 中硬编码密钥、AppCode、URL
6. 直接使用 `HttpURLConnection` 新增外部调用
7. 金额用 `double/float` 运算
8. 每个模块自己写重复工具类
9. 大量手工 set 字段替代统一 Convert
10. 吞异常后静默返回成功
11. 无事务保护的多表写操作
12. Mapper/XML 中承载业务规则分支

---

## 13. 遗留代码整改优先级

结合当前仓库现状，建议按以下优先级逐步收敛：

### P1

1. 清理 Service 中硬编码密钥、AppCode、外部 URL
2. 外部 HTTP 调用统一收口到 client/config 层
3. 新增业务代码全面切到构造器注入

### P2

1. 把稳定转换逻辑从 `BeanUtil` 迁移到 `MapStruct Convert`
2. 把导出下载写法统一收敛到 `ExcelUtils / ServletUtils`
3. 把重复 stream 转 map/list 逻辑收敛到公共工具

### P3

1. 为 Biz/Service 边界补充模块级说明文档
2. 为状态流转复杂的业务补充状态机文档
3. 为外部系统对接补充防腐层规范

---

## 14. 推荐代码模板

## 14.1 Controller 模板

```java
@Tag(name = "管理后台 - XXX")
@RestController
@RequestMapping("/xxx")
@Validated
@RequiredArgsConstructor
public class XxxController {

    private final XxxBusiness xxxBusiness;

    @GetMapping("/page")
    public CommonResult<PageResult<XxxRespVO>> getPage(@Valid XxxPageReqVO reqVO) {
        return CommonResult.success(xxxBusiness.getPage(reqVO));
    }
}
```

## 14.2 Biz 模板

```java
@Service
@Slf4j
@RequiredArgsConstructor
public class XxxBusiness {

    private final XxxApi xxxApi;

    public PageResult<XxxRespVO> getPage(XxxPageReqVO reqVO) {
        XxxPageParam param = BeanUtil.copyProperties(reqVO, XxxPageParam.class);
        PageResult<XxxDTO> page = xxxApi.getPage(param);
        return XxxConvert.INSTANCE.convertPage(page);
    }
}
```

## 14.3 Service 模板

```java
@Service
@Validated
@RequiredArgsConstructor
public class XxxServiceImpl implements XxxService {

    private final XxxMapper xxxMapper;

    @Override
    @Transactional(rollbackFor = Exception.class)
    public void updateStatus(Long id, Integer status) {
        XxxDO data = validateExists(id);
        data.setStatus(status);
        int count = xxxMapper.updateById(data);
        Assert.isTrue(count > 0, "更新状态失败");
    }
}
```

---

## 15. 结论

后续半成品业务及同类模块开发，统一按以下主线执行：

1. Controller 薄
2. Biz 负责编排
3. Service 负责本域规则和事务
4. Mapper 只做数据访问
5. 工具类优先复用框架已有能力
6. 注入统一构造器注入
7. 导出下载统一走现有输出流工具
8. 外部调用统一收口，禁止继续扩散硬编码和直连写法
9. 避免`this.`写法，不美观
10. 函数内变量非必要不使用`final`修饰
11. 对编码的代码块进行格式化（美观）（不要格式旧代码）

如果后续要在团队内作为正式规范落地，建议再配套两项动作：

1. 新建模块模板代码脚手架
2. 在 CR 清单中加入本文档对应检查项
