---
title: 首次使用 Azure 多重身份验证登录
description: 本页介绍用户首次登录时的体验。
services: multi-factor-authentication
keywords: 如何使用 azure 目录, 云中的 active directory, active directory 教程
documentationCenter: 
authors: billmath
manager: stevenp
editor: curtland

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
wacn.date: 12/30/2016
ms.author: kgremban
---

# Azure 多重身份验证的设置体验

 当管理员已将你的帐户配置为要求必须同时使用密码和手机响应以验证你的身份时，将使用其他安全性验证设置。如果管理员已将你的帐户配置为需要其他安全性验证，则**你在完成自动注册过程之前将无法登录**。

## 确定如何使用多重身份验证

 在配置帐户后首次登录时，系统将提示你开始进行自动注册过程。可以通过单击“立即设置”开始执行此过程。

![设置](./media/multi-factor-authentication-end-user-first-time/first.png)

使用注册过程，你可以指定首选的验证方法。该方法可以是下表中所列的任何一种。如需更多信息（包括演练），只要单击其中一种方法即可。

方法|说明
:------------- | :------------- | 
[移动电话呼叫](./multi-factor-authentication-end-user-first-time-mobile-phone.md)| 向身份验证电话拨打自动语音呼叫。用户接听电话，并按电话键盘上的 # 进行身份验证。此电话号码将不会同步到本地 Active Directory。
[手机短信](./multi-factor-authentication-end-user-first-time-mobile-phone.md)|向用户发送包含验证码的短信。系统会提示用户使用验证码回复短信或在登录界面中输入验证码。
[办公电话呼叫](./multi-factor-authentication-end-user-first-time-office-phone.md)|向用户拨打自动语音呼叫。用户接听电话，并按电话键盘上的 # 进行身份验证。
[移动应用](./multi-factor-authentication-end-user-first-time-mobile-app.md)|将通知推送到用户的智能手机或平板电脑上的 Microsoft Authenticator 应用。用户在应用中点击“验证”进行身份验证。此外，该应用还可以用作进行脱机身份验证的 OTP 令牌。用户在登录屏幕上输入该令牌进行身份验证。<br><p> Microsoft Authenticator 应用可以使用 2 种不同模式运行，以提供多重身份验证服务所能提供的附加安全性。这两种模式是：<li>**通知** - 在此模式下，Microsoft Authenticator 应用可防止对帐户进行未经授权的访问并停止欺诈性事务。此功能是使用推送到你的手机或已注册设备上的推送通知来完成的。你可以直接查看通知，如果该通知是合法的，则选择“身份验证”。否则，你可以选择“拒绝”或选择拒绝并报告欺诈性通知。有关报告欺诈性通知的信息，请参阅“如何针对多重身份验证使用拒绝并报告欺诈功能”。</li><p><li>**一次性密码** - 在此模式下，Microsoft Authenticator 应用可用作生成 OATH 验证码所需的软件令牌。然后可以将此验证码与用户名和密码一起输入，进行第二种形式的身份验证。</li><br><p> Microsoft Authenticator 应用可用于 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073)。

<!---HONumber=Mooncake_1107_2016-->
