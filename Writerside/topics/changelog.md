# 更新历史

## 1.2.x

### 1.2.2

* 实现上下游API
* 实现企业互联API
* 实现安全管理API
* 实现汇报API
* 审批API中审批选项控件现在支持关联控件和外部选项
* 增加UserAgent请求头以标识客户端，例如`User-Agent: Wecom/1.2.2/Java/1.8.0_372`
* 回调解析增加`doDecryptMsg`方法以支持不校验`receiveid`
* samples实例项目增加常用的样例
* 优化统一异常处理以提升性能
* JOSE类库升级到 9.36

### 1.2.1

* 实现打卡API
* 实现公费电话API
* 实现企微客服知识库API
* 企微回调支持日程相关操作事件
* 企业群发接口，支持指定群ID，也支持按客户标签进行筛选
* 通过接口获取微信客服消息时，可获取聊天记录类型消息和视频号类型消息
* 打卡规则中补卡可指定异常类型
* 企业微信机器人API优化，更加友好地组织参数
* 优化企业微信审批参数
* 修复读取响应体数据为空的问题
* 增加企业微信机器人图片消息样例

### 1.2.0

* 部分审批、朋友圈参数简化
* 审批假期余额相关接口实现
* 增加微信支付模块和公众号模块（实验性）
* 其它一些底层优化

## 1.1.x

### 1.1.6

* 新增花名册获取员工字段配置
* 新增花名册获取员工花名册信息
* 新增花名册更新员工信息
* 新增获客助手查询剩余使用量接口，支持查询每天过期的额度
* 新增获客助手查询获客链接的使用详情
* 新增获客助手回调删除获客链接事件
* 新增获客助手回调获客额度即将过期事件
* 企微客服获取接待人员列表接口支持获取「暂时挂起」状态
* 企微客服接收消息和事件接口支持获取「暂时挂起」状态
* 收集表读取答案接口，支持获取部门题、成员题、时长题答案
* 创建收集表接口，问题类型新增部门、成员和时长，新增问题的额外设置能力
* 编辑收集表接口，问题类型新增部门、成员和时长，新增问题的额外设置能力
* 日程支持设置为全天日程以及多次提醒

### 1.1.5

* 现在接口显式抛出 `WeComException` 异常
* 修复审批评论异常的问题
* 优化审批金额类型，现在强制保留两位小数

### 1.1.4

* 电子发票相关接口的实现
* 文档权限相关接口的实现
* 支持自建审批回调
* 增强xml解析的安全性
* 兼容1.2.10版本jackson
* 修复删除日历URI错误的问题
* 其它一些向下兼容的底层优化

### 1.1.3

* 文档-管理文档实现
* 增加tmp_external_userid的转换接口
* 增加审批数据填充工具
* 修复获客助手响应无法解析的问题
* 修复字节数据追加被覆盖的异常
* 🐞 部分参数的简化优化 [#I7JY43](https://gitee.com/felord/wecom-sdk/issues/I7JY43)

### 1.1.2

* 小程序对外收款
* 回调优化

### 1.1.1

* 优化了回调处理、完善了部分回调事件
* 优化了应用管理-应用详情API
* 实现企业支付-对外收款账户管理API
* 增加回调最佳实践实例

### 1.1.0

* 移除 Spring Boot 全面使用Retrofit作为Rest引擎
* 实现企业支付中的企业红包、向员工付款、对外收款
* XML转换优化
* 🐞 回调请求体参数错误修复 [#I7ERVA](https://gitee.com/felord/wecom-sdk/issues/I7ERVA)
* 媒体上传更正参数类型
* 移除无用依赖
* Okhttp 升级到 4.11.0
* Jackson 升级到 2.15.2
* 增加新的samples样例

## 1.0.x

### 1.0.14

* 客服模块增加客服消息接收与回调API相关实现
* 客服模块增加升级客户服务API相关实现
* 客服模块增加客服统计API相关实现
* 完善审批API的实现
* 实现客服回调、客服消息、客服事件消息
* 回调解析增加签名返回作为唯一性校验依据
* 增加Token失效重试功能
* 拦截器采用OKHTTP框架的拦截器，以避免无法共享连接池
* 回调解析增加签名返回作为唯一性校验依据
* 抽取公共模块以支持模块化
* （试验）增加响应式模块rx-wecom-sdk，使用retrofit2、rxjava3实现
* （试验) 增加开启HTTP调用日志能力，以方便调试
* 增加发送应用消息示例
* 其它一些底层优化

### 1.0.13

* 新增医疗行业紧急通知API
* XStream 性能以及安全优化
* 修复回调解析出现的类型转换异常

### 1.0.12

1. 增加获客助手相关API，利用获客助手拉新更加方便了
2. 增加了 Spring Boot 的示例
3. 增加了对低版本jackson的兼容
4. 补充了公费电话、会议室、健康上报、微信客服的应用ID
5. 审批模板相关参数优化

### 1.0.11

* 由Http Client 切换到 okhttp
* 增加审批相关接口
* 补充被动回复消息回调格式，暂时未实现通知型卡片
* 简化API实现，删除大量样板代码
* 优化随机数生成器
* 获取客户详情接口，新增视频号留资服务添加场景
* 支持设置通知范围成员权限

### 1.0.10

* 优化应用卡片消息参数
* 日历更新API参数优化
* 现在回调可以处理更加丰富的类型

### 1.0.9

* 完善收集表API

### 1.0.8

* 增加收集表实现

### 1.0.7

* 兼容spring boot 2.1.0低版本
* 修复发送消息返回值反序列化错误
* 优化统计API

### 1.0.6

* 部分通讯录API优化完善

### 1.0.5

* 增加离职继承
* 升级部分依赖版本
* 授权相关接口旧字段优化
* 通讯录回调完善
* 增加更新模板卡片接口以及回调监听

### 1.0.4

* 优化了异常处理
* 修复回调报错问题
* 对现有API的一些参数变动进行了补充
* 其它一些优化

### 1.0.3

* 客户沟通相关API例行维护
* 优化了部分API参数
* 修复json多态序列化问题
* 优化了回调实现

### 1.0.2

* 实现应用消息相关API
* 优化回调
* 部分bugfix

### 1.0.1

* 优化了部分API并修复了一些BUG

### 1.0.0

* 实现通讯录管理部分API
* 实现客户联系部分API
* 实现素材管理部分API