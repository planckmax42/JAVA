作为后端初学者，我来帮你制定一个循序渐进的阅读计划，从基础到复杂，每个阶段都有明确的目标。

苍穹外卖项目阅读计划
第一阶段：前置知识确认（先决条件）
在读代码之前，确保你对以下概念有基本了解，否则读代码会很吃力：

 Java 基础 — 类、接口、继承、注解、泛型
 Maven — 什么是 pom.xml、模块依赖关系
 HTTP 基础 — GET/POST/PUT/DELETE、请求头、状态码
 SQL 基础 — SELECT、INSERT、UPDATE、JOIN
 Spring Boot 核心概念 — @Component、@Autowired、@RestController、@Service、@Repository
第二阶段：理解项目骨架（1-2天）
目标：搞清楚项目是什么、由哪些部分组成。

 读 sky-take-out/pom.xml — 了解三个模块的关系和所有依赖库
 读 sky-server/src/main/resources/application.yml — 了解项目配置
 读 sky-server/src/main/resources/application-dev.yml — 了解开发环境配置（数据库、Redis、OSS 等）
 读 SkyApplication.java — 入口类，注意三个注解的含义
 浏览 sky-pojo 目录结构 — 区分 Entity / DTO / VO 三类数据对象的职责差异
第三阶段：读懂数据模型（2-3天）
目标：搞清楚系统操作哪些数据、数据长什么样。

 读所有 Entity（实体类） — Employee、User、Dish、Orders 等，理解数据库表结构
 理解 DTO vs VO vs Entity 的区别：
Entity = 数据库表的映射
DTO = 前端发来的请求数据
VO = 后端返回给前端的数据
 读 Result.java 和 PageResult.java（sky-common）— 所有接口的统一返回格式
第四阶段：读懂基础设施代码（2天）
目标：理解框架层面的"脚手架"代码。

 读 WebMvcConfiguration.java — 了解 MVC 配置：拦截器注册、Swagger 配置、消息转换器
 读 JwtTokenAdminInterceptor.java + JwtTokenUserInterceptor.java — 理解 JWT 鉴权流程
 读 BaseContext.java — 理解 ThreadLocal 如何在请求中传递用户 ID
 读 JwtUtil.java — 理解 JWT 的生成和解析
 读 RedisConfiguration.java — 了解 Redis 客户端配置
 读 GlobalExceptionHandler.java — 理解统一异常处理
第五阶段：跟读一个完整业务流程（3-4天）
目标：从头到尾追踪一个请求，理解分层架构。

推荐从"员工登录"开始（最简单的完整流程）：

 EmployeeController.login() — 接收请求，调用 Service
 EmployeeServiceImpl.login() — 业务逻辑：查数据库、校验密码、生成 JWT
 EmployeeMapper.getByUsername() — 接口定义
 EmployeeMapper.xml — 实际的 SQL 语句
然后读"员工分页查询"（涉及分页）：

 EmployeeController.page() → EmployeeServiceImpl.pageQuery() → EmployeeMapper.pageQuery()
 理解 PageHelper.startPage() 如何拦截 SQL 实现分页
第六阶段：理解 AOP 自动填充（1天）
目标：理解面向切面编程（AOP）在项目中的实际应用。

 读 AutoFill.java（自定义注解）
 读 AutoFillAspect.java — 理解切面如何拦截 Mapper 方法，自动设置时间和用户字段
 在任意 Mapper 接口中找到带 @AutoFill 的方法，对照理解
第七阶段：读核心业务模块（按顺序，5-7天）
目标：理解主要业务逻辑，每个模块都按 Controller → Service → Mapper → XML 的顺序读。

 分类管理（Category）— 简单的增删改查，适合入门
 菜品管理（Dish）— 涉及多表关联（菜品 + 口味），有文件上传
 套餐管理（Setmeal）— 涉及缓存（Redis @Cacheable）
 购物车（ShoppingCart）— 理解用户端业务逻辑
 用户登录（User）— 理解微信小程序登录流程（OAuth）
 地址簿（AddressBook）— 简单的用户数据管理
第八阶段：读最复杂的订单模块（3-4天）
目标：理解核心业务流程，这是项目中最复杂的部分。

 下单流程 — OrdersSubmitDTO → OrderServiceImpl.submitOrder() → 扣库存、生成订单、清购物车
 支付回调 — PayNotifyController → 微信支付回调处理
 订单状态流转 — 待付款 → 待接单 → 已接单 → 派送中 → 已完成/已取消
 定时任务 — OrderTask.java 如何处理超时未付款、超时未接单的订单
 WebSocket 推送 — WebSocketServer.java 如何向管理端实时推送新订单通知
第九阶段：读统计报表模块（2天）
目标：理解数据聚合查询。

 ReportController + ReportServiceImpl — 营业额统计、用户统计、订单统计
 WorkSpaceController + WorkspaceServiceImpl — 工作台今日数据
 理解 Apache POI 如何生成 Excel 报表文件
第十阶段：回顾与总结（1-2天）
 画出整个系统的模块依赖图
 画出一个完整订单从创建到完成的时序图
 总结项目中用到的设计模式（分层架构、AOP、ThreadLocal、统一响应等）
 尝试在本地运行项目，访问 http://localhost:8080/doc.html 调用接口验证理解
建议： 每读完一个文件，在文件顶部用注释写下你的理解，或者单独记笔记。遇到不懂的注解或类，先搜索其官方文档含义，再回到代码中理解用法。
