# 入门示例

鉴于大部分项目使用**Spring Boot**开发，因此以**Spring Boot**为基础编写样例。相关环境信息：

- Java 8
- Spring Boot 2.7.12

> **在你开始之前**
>
> 在阅读本章节前请确保认真阅读了[概念参考](Reference.md)章节，以便于能理解[wecom-sdk](https://gitee.com/felord/wecom-sdk)
> 的设计。
>
> 完整的示例可通过 [spring-boot-sample](https://gitee.com/felord/wecom-sdk/tree/release/samples/spring-boot-sample) 获取。
> {style="note"}

接下来你将学会如何使用[wecom-sdk](https://gitee.com/felord/wecom-sdk)调用企业微信api，我们开始吧。

## 初始化配置 {id="init"}

在开始之前，开发者需要引入[wecom-sdk](https://gitee.com/felord/wecom-sdk)
最新版本的依赖，并完成初始化API入口[WorkWechatApi](Reference.md#workwechatapi)的配置和回调配置。

```Java
/**
 * Wecom Sdk 配置
 *
 * @author dax
 * @since 2023 /5/30
 */
@Configuration(proxyBeanMethods = false)
public class WecomSdkConfiguration {

    /**
     * 企微token，ticket 缓存中继，这里使用ehcache作为例子，你可以无缝替换为redis等缓存中间件
     *
     * @return the we com token cacheable
     */
    @Bean
    WeComTokenCacheable weComTokenCacheable() {
        return new EhcacheWeComTokenCacheable();
    }

    /**
     * 回调配置缓存
     *
     * @return the ehcache we com callback settings cache
     */
    @Bean
    EhcacheWeComCallbackSettingsCache ehcacheWeComCallbackSettingsCache() {
        return new EhcacheWeComCallbackSettingsCache();
    }


    /**
     * 初始化 企微API客户端，这个是平常我们发起调用的入口
     * <p>
     * 例子参见 test 测试包下的 SpringBootWecomSdkTests
     *
     * @param weComTokenCacheable the we com token cacheable
     * @return the work we chat api
     */
    @Bean
    WorkWeChatApi workWeChatApi(WeComTokenCacheable weComTokenCacheable) {
        return new WorkWeChatApi(weComTokenCacheable);
    }

    /**
     * 回调配置
     *
     * @param wecomCallbackEventService 统一消费回调数据
     * @param settingsService           统一加载回调配置
     * @return the callback crypto
     */
    @Bean
    public CallbackCrypto callbackCrypto(WecomCallbackEventService wecomCallbackEventService, WecomCallbackSettingsService settingsService) {
        return new CallbackCryptoBuilder(wecomCallbackEventService::handlerEventBody)
                .build(settingsService::loadCallbackSettings);
    }
}
```

{collapsible="true" collapsed-title="WorkWechatApi初始化"}

当[初始化配置](#init)完成后就可以像引用其它Spring Bean一样使用`WorkWechatApi`来调用企业微信API了。

## 企微机器人

目前已经支持所有格式的企业微信机器人消息，区别于其它API机器人采用的是`WorkWeChatApi`静态API`webhookApi()`。

<tabs>
    <tab title="Markdown">
        <code-block lang="java">
         <![CDATA[
        String content = "## wecom-sdk \n" +
                "<font color=\"comment\">开源java企微SDK</font> \n" +
                "> `wecom-sdk`真的很好用\n" +
                "项目地址：[wecom-sdk](https://gitee.com/felord/wecom-sdk)";
        WebhookBody markdownBody = WebhookMarkdownBody.from(content);
        WeComResponse weComResponse = WorkWeChatApi.webhookApi().send("机器人key", markdownBody);
]]>
        </code-block>
    </tab>
    <tab title="文本">
        <code-block lang="java">
         <![CDATA[
        WebhookBody textBody = WebhookTextBody.from("这里为纯文本");
        WeComResponse weComResponse = WorkWeChatApi.webhookApi().send("机器人key", textBody);
]]>
        </code-block>
    </tab>
    <tab title="图文">
        <code-block lang="java">
         <![CDATA[
        WebhookArticle article = new WebhookArticle("这里为标题", "这里为图文链接")
                .picurl("这里为封面图链接")
                .description("这里为摘要信息");
        WebhookBody newsBody = WebhookNewsBody.from(Collections.singletonList(article));
        WeComResponse weComResponse = WorkWeChatApi.webhookApi().send("机器人key", newsBody);
]]>
        </code-block>
    </tab>
    <tab title="图片">
        <code-block lang="java">
         <![CDATA[
         //  从base64发图片
        String base64 = "图片base64";
        String md5 = "图片base64的md5";
        WebhookBody imageBody1 = WebhookImageBody.from(base64, md5);
        WeComResponse weComResponse = WorkWeChatApi.webhookApi().send("机器人key", imageBody1);
         //  从流发送图片
        String path = "C:\\Users\\Administrator\\Desktop\\0.png";
        InputStream inputStream = Files.newInputStream(Paths.get(path));
        WebhookBody imageBody2 = WebhookImageBody.from(inputStream);
        WeComResponse weComResponse = WorkWeChatApi.webhookApi().send("机器人key", imageBody2);
]]>
        </code-block>
    </tab>
    <tab title="文件">
        <code-block lang="java">
         <![CDATA[
        WebhookBody fileBody = WebhookFileBody.from("素材上传mediaid");
        WeComResponse weComResponse = WorkWeChatApi.webhookApi().send("机器人key", fileBody);
]]>
        </code-block>
    </tab>
    <tab title="语音">
        <code-block lang="java">
         <![CDATA[
        WebhookBody voiceBody = WebhookVoiceBody.from("素材上传mediaid");
        WeComResponse weComResponse = WorkWeChatApi.webhookApi().send("机器人key", voiceBody);
]]>
        </code-block>
    </tab>
    <tab title="卡片">
        <code-block lang="java">
         <![CDATA[
        //  以文本卡片为例
        MainTitle mainTitle = new MainTitle("欢迎使用wecom-sdk", "最优雅的企业微信Java SDK");
        UrlCardAction cardAction = new UrlCardAction("https://gitee.com/felord/wecom-sdk");
        EmphasisContent emphasisContent = new EmphasisContent();
        emphasisContent.setTitle("197");
        emphasisContent.setDesc("Gitee Star");
        CardSource source = new CardSource();
        source.setIconUrl("https://wework.qpic.cn/wwpic/252813_jOfDHtcISzuodLa_1629280209/0");
        source.setDesc("WECOM");
        source.setDescColor(CardSource.DescColor.GREY);
        TextTemplateCard textTemplateCard = new TextTemplateCard(mainTitle, cardAction)
                .emphasisContent(emphasisContent)
                .source(source)
                .jumpList(Collections.singletonList(new UrlJump("wecom文档", "https://felord.cn/wecom")));
        WebhookTemplateCardBody textCardBody = WebhookTemplateCardBody.from(textTemplateCard);
        WeComResponse weComResponse = WorkWeChatApi.webhookApi().send("机器人key", textCardBody);
]]>
        </code-block>
    </tab>
</tabs>

以相对复杂的文本卡片消息为例，最终的效果是这样的：

![机器人卡片效果](cardbotsample.png)

## 应用消息

[应用消息](https://developer.work.weixin.qq.com/document/path/90236)
也是常用的触达企业成员功能，[wecom-sdk](https://gitee.com/felord/wecom-sdk)
对应用消息进行了全面的支持，目前已经支持全部应用消息类型。鉴于应用消息的类型比较丰富，这里以相对复杂的卡片消息为例：

```Java
    /**
     * 比较复杂的应用文本卡片模版消息，其它卡片模版使用{@link TemplateCardBuilders}构建
     */
    @Test
    void sendAgentMsg() {
        CardSource source = new CardSource();
        source.setDesc("XXX系统通知");
        source.setDescColor(CardSource.DescColor.GREY);
        source.setIconUrl("这里替换方形系统小logo链接");
        MainTitle mainTitle = new MainTitle("主标题", "次标题");

        TextHorizontalContent no = new TextHorizontalContent("订单编号", "xxxxxxxxxxxxxxxxxxxx");
        TextHorizontalContent address = new TextHorizontalContent("订单地址", "大王庄二郎庙");
        // 使用ID转录
        TextHorizontalContent orgName = new TextHorizontalContent("公司名称", "$departmentName=" + 1 + "$");
        // 员工组件 使用ID转录
        AtStaffHorizontalContent staff = AtStaffHorizontalContent.withTransUserId("推送人员", "这里放企微成员的userid");
        String now = LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));
        TextHorizontalContent time = new TextHorizontalContent("推送日期", now);

        TextMessageTemplateCard textMessageTemplateCard = TemplateCardBuilders.textMessageTemplateCardBuilder(new UrlCardAction("这里放面板路径"))
                .source(source)
                .mainTitle(mainTitle)
                .horizontalContentList(Arrays.asList(no, address, orgName, staff, time))
                .jumpList(Collections.singletonList(new UrlJump("查看详情", "https://baidu.com")))
                .build();

        TemplateCardMessageBody<TextMessageTemplateCard> cardMessageBody = MessageBodyBuilders.templateCardMessageBodyBuilder(textMessageTemplateCard)
                .touser("发送的目标成员，多个使用|隔开")
                // 开启ID转录  不然上面的组件会报错
                .enableIdTrans(BoolEnum.TRUE)
                // 开启重复检查
                .enableDuplicateCheck(BoolEnum.TRUE)
                .build();

        DefaultAgent defaultAgent = new DefaultAgent("企微企业id", "企微应用密钥", "企微应用id");
        MessageResponse send = workWeChatApi.agentApi(defaultAgent)
                .agentMessageApi()
                .send(cardMessageBody);
        Assertions.assertTrue(send.isSuccessful());
    }
```

{collapsible="true" collapsed-title="应用消息推送文本卡片消息"}

> 应用消息部分场景下支持ID转译，用户id、部门id等可自动转换为具体的名称显示，无需开发者手动转换，ID转译[相关文档](https://developer.work.weixin.qq.com/document/path/90236#id%E8%BD%AC%E8%AF%91%E8%AF%B4%E6%98%8E)
> {style="note"}

最终的效果是这样的：

![应用卡片消息](agentmsg.png)

## 上传素材

上传临时素材也是比较常用的基础功能，也是企业微信API封装的难点之一。使用[wecom-sdk](https://gitee.com/felord/wecom-sdk)
后，上传素材将变得非常简单。这里以上传临时素材为例：

```Java
    /**
     * 上传临时素材
     * <p>
     * 其它上传类似
     *
     * @throws IOException the io exception
     */
    @Test
    void uploadMedia() throws IOException {

        AgentDetails agentDetails = new DefaultAgent("企业ID", "应用密钥", "应用id");
        MediaApi mediaApi = workWeChatApi.mediaApi(agentDetails);
        InputStream inputStream = Files.newInputStream(Paths.get("C:\\Users\\Administrator\\Pictures\\wecom.png"));
        MultipartResource resource = new MultipartResource("wecom.png", inputStream);
        MediaResponse mediaResponse = mediaApi.uploadMedia(MediaTypeEnum.IMAGE, resource);
        Assertions.assertTrue(mediaResponse.isSuccessful());
    }
```

{collapsible="true" collapsed-title="上传临时素材"}

> **注意**
>
> `MultipartResource`对象的入参`fileName`必须是带**扩展名**的文件名，必须符合正则表达式`\\.([^.]+)$`。
> {style="warning"}

## 客户沟通

客户沟通是企业微信的核心竞争力，企业微信最大的特点就是可以和微信无缝打通。[wecom-sdk](https://gitee.com/felord/wecom-sdk)
对该功能也进行了封装，以客户群发为例：

```Java
    /**
     * 客户群发和客户群群发
     * <p>
     * 给微信客户推送小程序促销消息
     */
    @Test
    void sendToUsers() {

        // 引导文案 要尽量吸引眼球
        String context = "🎉🎉🎉10元生鲜拼团最后一天\n" +
                "👉现在下单，福利多多，有几率获得以下礼品一份\n" +
                "🎁新鲜小台芒一斤\n" +
                "🎁西红柿一斤\n" +
                "快来点击小程序拼团吧😀";

        // 标题长度 64
        String title = "企微客户生鲜福利最后一天啦";
        // 小程序appid
        String appid = "xxxxxxxxxxxxxxxx";
        // 临时素材上传
        String picMeidaId = "3UF5V8DXrsjsSsvDE9HXDGcxoHZ78MPX-mS6aAaASPew7cgPGXtWcarAdWDdu0NRf";

        // 小程序活动页面
        String page = "store/pages/hots/detail.html?sku=xd233243&state=QWPT2342";
        MiniprogramMsgAttachment o = new MiniprogramMsgAttachment(title, appid, picMeidaId, page);

        MsgTemplateRequest request = MsgTemplateRequest.single("员工企微id", context, Collections.singletonList(o));

        AgentDetails nativedAgent = DefaultAgent.nativeAgent("企业id", "外部联系人密钥", NativeAgent.EXTERNAL);
        MsgTemplateResponse msgTemplateResponse = workWeChatApi.externalContactManager(nativedAgent)
                .messageApi()
                .addMsgTemplate(request);
        Assertions.assertTrue(msgTemplateResponse.isSuccessful());
    }
```

{collapsible="true" collapsed-title="客户群发和客户群群发"}

群发执行后，员工在企业微信将收到如下的提示消息，获得一个推送任务。

![员工任务消息](chatmsg.png)

执行后，该消息就会立即触达给该员工跟进的外部联系人客户或者客户群。

![群发任务消息](chatmsg2.png)

<warning>为了避免过度骚扰微信客户，企业微信对客户群发进行了频率限制，请根据企业微信的相关功能进行开发。另外本SDK只封装了官方的开放API，并不使用其它非正常渠道的技术来实现一些突破企微规定之外的功能</warning>

## 日程

企业微信日程可以帮助企业创建协作计划，[wecom-sdk](https://gitee.com/felord/wecom-sdk)对相关的API也进行了封装，接下来我们通过代码来创建日程。

### 日程关联应用

首先要关联一个自建应用才能使用日程API。具体操作如图：

![日程关联应用](schedule.png)

### 通过关联应用调用创建日程API

```Java
    /**
     * 企业微信创建日程
     */
    @Test
    void schedule() {

        AgentDetails agentDetails = new DefaultAgent("企业ID", "应用密钥，该应用需要关联到日程功能", "应用id");

        Instant now = Instant.now();
        Instant startTime = now.plus(10, ChronoUnit.MINUTES);
        Instant endTime = now.plus(40, ChronoUnit.MINUTES);
        ScheduleRequestBody schedule = new ScheduleRequestBody(startTime, endTime)
                .summary("wecom-sdk 项目需求评审")
                .description("对下半年新需求的评审研讨")
                .location("xxx市xxx区xxx路28号A座703")
                .attendees(Arrays.asList("参与人1 USERID", "参与人2 USERID", "参与人3 USERID"))
                .reminders(new Reminders()
                        .remind(RemindBeforeEventSecs.MIN_5)
                        .timezone(8));
        ScheduleAddRequest request = new ScheduleAddRequest(schedule);

        ScheduleApi scheduleApi = workWeChatApi.scheduleApi(agentDetails);

        GenericResponse<String> genericResponse = scheduleApi.add(request);
        Assertions.assertTrue(genericResponse.isSuccessful());
    }
```

{collapsible="true" collapsed-title="日程API调用"}

调用成功后，参与日程的企业成员将收到一个日程消息：

![日程消息](schmsg.png){ width="700" }

## 更多示例

请参考[wecom-sdk](https://gitee.com/felord/wecom-sdk)
提供的示例项目[samples/spring-boot-sample](https://gitee.com/felord/wecom-sdk/tree/release/samples/spring-boot-sample)。