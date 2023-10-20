# 快速集成

目前Wecom集成起来非常简单，并且已经同Spring生态解耦，适用于各种Java生态。

> **注意**
>
> 经过测试目前wecom-sdk在*Spring Boot 2.5+*以上运行良好，低版本需要自行修改源码兼容。
{style="note"}

## ✍️技术栈

- Retrofit2，支持最高版本号`2.9.0`
- OkHttp4，支持最高版本号`4.11.0`
- Rxjava3，支持最高版本号`3.0.0`
- Jackson2，支持最高版本号`2.15.2`
- XStream，支持最高版本号`1.4.20`

## 🕸️Maven中央仓库坐标

<tabs>
    <tab title="普通版本">
        <code-block lang="xml">
         <![CDATA[
<dependency>
    <groupId>cn.felord</groupId>
    <artifactId>wecom-sdk</artifactId>
    <version>1.2.2</version>
</dependency>
]]>
        </code-block>
    </tab>
    <tab title="rxjava版本">
        <code-block lang="xml">
            <![CDATA[
<dependency>
    <groupId>cn.felord</groupId>
    <artifactId>rx-wecom-sdk</artifactId>
    <version>1.2.2</version>
</dependency>
]]></code-block>
    </tab>
</tabs>

<note>通常情况下使用普通版本即可，rxjava版本仅用于开发响应式应用，开发者可根据实际情况进行选择。</note>
 