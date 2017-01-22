---
title: 播放内容 | Azure
description: 本主题列出了你可以用来播放内容的现有播放器。
services: media-services
documentationCenter: 
authors: Juliako
manager: erikre
editor: 

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2016
wacn.date: 11/21/2016
ms.author: juliako
---

#使用现有播放器播放内容

Azure 媒体服务支持多种常用的流式处理格式，如平滑流、HTTP 实时流和 MPEG-Dash。本主题列出了可用于测试流的现有播放器。

>[!NOTE]若要播放动态打包或动态加密的内容，请确保获取你计划从中传送内容的流式处理终结点的至少一个流式处理单元。有关缩放流式处理单元的信息，请参阅：[如何缩放流式处理单元](./media-services-manage-origins.md#scale_streaming_endpoints)。

###Azure 经典管理门户媒体服务内容播放器

**Azure 经典管理门户**提供了可用于测试视频的内容播放器。

单击所需的视频（确保它已[发布](./media-services-manage-content.md#publish)），然后单击门户底部的“播放”按钮。

请注意以下事项：

- **媒体服务内容播放器**从默认的流式处理终结点播放。如果要从非默认流式处理终结点播放，请使用其他播放器。例如 [Azure 媒体播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。

![AMSPlayer][AMSPlayer]

###Azure 媒体播放器

使用 [Azure 媒体播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html) 以下列任意格式播放你的内容（清除或受保护）：

- 平滑流
- MPEG DASH
- HLS
- 渐进式 MP4

###Flash Player

####带令牌的 AES 加密

[http://aestoken.azurewebsites.net](http://aestoken.azurewebsites.net)

###Silverlight 播放器

####监视

[http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)

####带令牌的 PlayReady

[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### DASH 播放器

[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

###其他

若要测试 HLS URL，还可以使用：

- iOS 设备上的“Safari”或
- Windows 上的“3ivx HLS 播放器”。

##开发视频播放器

有关如何开发自己的播放器的信息，请参阅[开发视频播放器](./media-services-develop-video-players.md)

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png

<!---HONumber=Mooncake_1114_2016-->