# 概述

![wecom-sdk](wechat-sdk.svg){width=530}


<p align="center">
    <a target="_blank" href="https://github.com/NotFound403/wecom-sdk/blob/release/LICENSE">
     	<img alt="" src="https://img.shields.io/github/license/NotFound403/wecom-sdk"/>
    </a>
    <a target="_blank" href="https://felord.cn">
     	<img alt="" src="https://img.shields.io/badge/java-8+-red"/>
    </a>   
    <a target="_blank" href="https://github.com/square/retrofit">
     	<img alt="" src="https://img.shields.io/badge/retrofit-2-brightgreen"/>
    </a>   
    <a target="_blank" href="https://mvnrepository.com/artifact/cn.felord/wecom-sdk">
     	<img alt="" src="https://img.shields.io/maven-central/v/cn.felord/wecom-sdk.svg?style=flat-square"/>
    </a>   
    <a target="_blank" href="https://github.com/NotFound403/wecom-sdk">
     	<img alt="" src="https://img.shields.io/github/stars/NotFound403/wecom-sdk?style=social"/>
    </a>    
    <a href='https://gitee.com/felord/wecom-sdk/stargazers'>
        <img src='https://gitee.com/felord/wecom-sdk/badge/star.svg?theme=gray' alt='star'/>
    </a>
</p>

## 🏆简介

[wecom-sdk](https://gitee.com/felord/wecom-sdk)是开源的企业微信开放 API 的 Java
实现，是目前开源实现中最完整的Java实现。经过近三年的迭代，目前已经实现了企业微信通讯录管理、上下游、客户管理、微信客服、素材管理、消息推送、企微机器人、身份验证、应用管理、OA办公相关接口，开发人员不需要很高的学习成本就能快速优雅地接入企业微信。

- <img alt="Gitee" height="24" src="gitee.svg" width="24"/>{style=inline}  [ https://gitee.com/felord/wecom-sdk](https://gitee.com/felord/wecom-sdk)
- <img alt="Github" height="24" src="github.svg" width="24"/>{style=inline}  [ https://github.com/NotFound403/wecom-sdk](https://github.com/NotFound403/wecom-sdk)

{style="none"}

> 如果你感觉这个项目不错，请点击[wecom-sdk](https://gitee.com/felord/wecom-sdk)右上角的⭐以鼓励作者，感谢对开源项目的支持🎉。
> {style="note"}

## 🔥特性

- 支持多个企业微信同时配置作业
- 集成方便，适用于各种 Java 生态
- 目前实现企业微信接口**200**多个，能满足大部分场景的需求
- 全参数封装，参数高度语义化封装，再也不担心组织参数、解析参数的问题
- 实现统一回调，所有回调事件可集中异步处理，开发者只需要关心业务逻辑的处理
- 统一异常处理，企业微信API调用异常统一被WeComException管理
- 由 SDK 接管 Token 生命周期，开发者无需关心 Token 的管理。

> **注意**
> 
> 目前wecom-sdk仅仅支持自建应用，暂不支持服务商、第三方代开发，如需支持可自行支持或联系作者定制。
> {style="warning"}

## 📱微信扫码加入交流群

![微信扫码入群](contact_me_qr.png){width=380}


## 🏗️API实现进度

进度说明：

- [x] 已全部实现
- [ ] 未实现或者部分实现

### 基础

- [x] 账号ID
- [x] 通讯录管理
- [x] 身份验证
- [x] 企业互联
- [x] 上下游
- [x] 安全管理
- [x] 消息推送
- [x] 应用管理
- [x] 素材管理
- [x] 电子发票

### 连接微信

- [x] 客户联系
- [x] 微信客服
- [x] 企业支付
- [x] 小程序接入对外收款
- [ ] 会话内容存档
- [ ] 家校沟通
- [ ] 家校应用
- [ ] 政民沟通

### 办公

- [ ] 邮件
- [ ] 文档
    - [ ] 编辑文档
    - [ ] 获取文档数据
- [x] 日程
- [ ] 会议
- [ ] 微盘
- [ ] 直播
- [x] 公费电话
- [x] 打卡
- [x] 审批
- [x] 汇报
- [x] 人事助手
- [ ] 会议室
- [x] 紧急通知应用