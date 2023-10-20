# 概念参考

在正式使用之前依然建议开发者掌握[wecom-sdk](https://gitee.com/felord/wecom-sdk)的一些设计概念，这非常有助于你定制自己的业务。

## API入口类

`WorkWeChatApi`是企业微信API的唯一入口，推荐注入**Spring IoC**（如果你集成了Spring生态）：

```Java
    @Bean
    WorkWeChatApi workWeChatApi(WeComTokenCacheable weComTokenCacheable) {
        return new WorkWeChatApi(weComTokenCacheable);
    }
```

然后就可以通过它的一系列方法来实现企业微信提供的功能了。例如通过手机号查询成员的企业微信userid:

```java
AgentDetails agent=DefaultAgent.nativeAgent("企业ID","通讯录secret",NativeAgent.CONTACT);

        GenericResponse<String> userIdByMobile=wecom.contactBookManager(agent)
        .userApi()
        .getUserIdByMobile("这里为手机号");
        String userId=userIdByMobile.getData();

```

> WorkWeChatApi的初始化和使用依赖于后面两个关键的概念 [AgentDetails](#agentdetails) 和 [缓存中继](#wecomtokencacheable)。

## AgentDetails

`cn.felord.AgentDetails`
是对应用的抽象描述，包含了企业ID [corpId](https://developer.work.weixin.qq.com/document/path/90665#corpid)
，应用ID [agentId](https://developer.work.weixin.qq.com/document/path/90665#agentid)
以及密钥 [secret](https://developer.work.weixin.qq.com/document/path/90665#secret)
，绝大多数接口的调用会用到它，默认实现是`DefaultAgent`。

### 分类

- 自建应用。开发者在[企微控制台](https://work.weixin.qq.com/wework_admin/loginpage_wx)自行创建的应用
- 企业微信内置的系统应用。例如，通讯录应用、审批应用、外部联系人应用

<note>系统应用的agentid是和企业无关是固定的，而自建应用的agentid是开发者创建时生成的。</note>

### 初始化

如果是自建应用你可以这样初始化：

```java
AgentDetails agent=new DefaultAgent("企业ID","应用密钥","应用ID");
```

如果是内置系统应用，比如外部联系人应用：

```java
AgentDetails external=DefaultAgent.nativeAgent("企业ID","应用密钥",NativeAgent.EXTERNAL);
```

通常这些应用参数会存入数据库，然后我们需要通过**企业ID**和**应用ID**实现应用配置检索服务（可以增加缓存来降低数据库的检索压力）：

``` Kotlin
  (corpId,agentId)->AgentDetails
```

## WeComTokenCacheable

`WeComTokenCacheable`定义了企业微信所需要的 [accessToken](https://developer.work.weixin.qq.com/document/path/91039)、 [corpticket](https://developer.work.weixin.qq.com/document/path/90506)
和 [agentticket](https://developer.work.weixin.qq.com/document/path/90506)
的中继缓存。这里我使用了**Spring
Cache**
实现，你可以根据自己的实际情况选择具体的缓存实现，但是要自行保证中继服务器数据一致性，通常情况下它们的有效期都是<shortcut>
7200</shortcut>秒。

```java
public static class RedisWeComCacheable implements WeComTokenCacheable {
  private static final String QYWX_TOKEN_CACHE = "token::qywx";
  private static final String QYWX_CORP_TICKET_CACHE = "ticket::qywx::corp";
  private static final String QYWX_AGENT_TICKET_CACHE = "ticket::qywx::agent";

  @CachePut(value = {QYWX_TOKEN_CACHE}, key = "#corpId.concat('::').concat(#agentId)")
  @Override
  public String putAccessToken(@NotNull String corpId, @NotNull String agentId, @NotNull String accessToken) {
    return accessToken;
  }

  @Cacheable(value = {QYWX_TOKEN_CACHE}, key = "#corpId.concat('::').concat(#agentId)")
  @Override
  public String getAccessToken(@NotNull String corpId, @NotNull String agentId) {
    return null;
  }

  @CachePut(value = {QYWX_CORP_TICKET_CACHE}, key = "#corpId.concat('::').concat(#agentId)")
  @Override
  public String putCorpTicket(@NotNull String corpId, @NotNull String agentId, @NotNull String corpTicket) {
    return corpTicket;
  }

  @Cacheable(value = {QYWX_TOKEN_CACHE}, key = "#corpId.concat('::').concat(#agentId)")
  @Override
  public String getCorpTicket(@NotNull String corpId, @NotNull String agentId) {
    return null;
  }

  @CachePut(value = {QYWX_AGENT_TICKET_CACHE}, key = "#corpId.concat('::').concat(#agentId)")
  @Override
  public String putAgentTicket(@NotNull String corpId, @NotNull String agentId, @NotNull String agentTicket) {
    return agentTicket;
  }

  @Cacheable(value = {QYWX_TOKEN_CACHE}, key = "#corpId.concat('::').concat(#agentId)")
  @Override
  public String getAgentTicket(@NotNull String corpId, @NotNull String agentId) {
    return null;
  }
}
```

{collapsible="true" collapsed-title="Spring Cache缓存实现"}

## 回调

企业微信回调是自建系统及时响应企业微信操作事件的重要手段。为了安全起见，企业微信对回调对接进行了一系列定义，这也是企业微信开发的难点之一。`wecom-sdk`对企业微信回调进行了统一封装，屏蔽了验签、解密、异步处理等技术难点，让开发者能够快速地对接企业微信回调。
> 尽管[wecom-sdk](https://gitee.com/felord/wecom-sdk)简化了企业微信回调开发，但是仍然建议开发者认真阅读关于企业微信回调的[相关技术文档](https://developer.work.weixin.qq.com/document/path/90930)。 

### CallbackCrypto
企业微信回调的处理由`CallbackCrypto`来负责，它提供了一系列的方法用于加密解密、加签验签。我们会在后面的应用环节来具体讲述如何使用它。

### CallbackCryptoBuilder
顾名思义，`CallbackCryptoBuilder`用来构建`CallbackCrypto`。 它有几个关键点：

- `XmlReader` XML解析的抽象，框架默认提供了**XStream**实现，不喜欢的可以重新实现注入。
- `Consumer<CallbackEventBody>` 消费事件函数，用来处理回调数据。
- `CallbackSettingsService` 企业微信回调配置检索，用来检索回调的[配置参数](https://developer.work.weixin.qq.com/document/path/90930)。
- `ExecutorService` 回调处理线程池，回调数据的处理是异步的，这里默认提供了一个名称前缀为`WECOM-CALLBACK-THREAD-POOL`
  的线程池，你也可以自定义一个符合你实际配置的线程池。