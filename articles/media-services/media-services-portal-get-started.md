---
title: 开始使用 Azure 经典管理门户按需传送内容 | Azure
description: 本教程介绍了使用 Azure 媒体服务和 Azure 经典管理门户实施视频点播 (VoD) 内容传送应用程序的步骤。
services: media-services
documentationCenter: ''
authors: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.date: 06/22/2016
wacn.date: 12/27/2016
---

# 开始使用 Azure 经典管理门户按需传送内容

[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

本教程介绍了使用 Azure 经典管理门户实施基本视频点播 (VoD) 内容传送应用程序的步骤。

> [!NOTE]
> 若要完成本教程，你需要一个 Azure 帐户。有关详细信息，请参阅 [Azure 免费试用](https://www.azure.cn/pricing/1rmb-trial/?WT.mc_id=A261C142F)。

本教程包括以下任务：

1.  创建 Azure 媒体服务帐户。
2.  配置流式处理终结点。
1.  上传视频文件。
1.  将源文件编码为一组自适应比特率 MP4 文件。
1.  发布资产并获取流式处理和渐进式下载 URL。
1.  播放内容。

## 创建 Azure 媒体服务帐户

1. 在 [Azure 经典管理门户](https://manage.windowsazure.cn/)中，依次单击“新建”、“媒体服务”和“快速创建”。

    ![媒体服务快速创建](./media/media-services-portal-get-started/wams-QuickCreate.png)

2. 在“名称”中，输入新帐户的名称。媒体服务帐户名称由小写字母或数字构成（不含空格），长度为 3 到 24 个字符。

3. 在“区域”中，选择将用于存储媒体服务帐户的元数据记录的地理区域。下拉列表中仅显示可用的媒体服务区域。

4. 在“存储帐户”中，选择一个存储帐户以便为媒体服务帐户中的媒体内容提供 Blob 存储。可以选择位于媒体服务帐户所在的地理区域内的现有存储帐户，也可以创建一个新的存储帐户。将在同一区域内创建一个新的存储帐户。

5. 如果已创建了新的存储帐户，请在“新建存储帐户名称”中输入该存储帐户的名称。适用于存储帐户名的规则对媒体服务帐户同样适用。

6. 单击窗体底部的“快速创建”。

    可以在窗口底部的消息区域中监视过程的状态。

    成功创建帐户后，状态将更改为“活动”。

    在页面底部，将出现“管理密钥”按钮。单击此按钮时，会显示一个对话框，其中包含媒体服务帐户名以及主密钥和辅助密钥。需要帐户名和主密钥信息，才能以编程方式访问媒体服务帐户。

    ![“媒体服务”页](./media/media-services-portal-get-started/wams-mediaservices-page.png)

    双击帐户名时，默认情况下将显示“快速启动”页。可从此页执行某些管理任务，而这些管理任务也可从该门户的其他页执行。例如，可以从此页上传视频文件，也可以从“内容”页执行此操作。

## 使用门户配置流式处理终结点

使用 Azure 媒体服务最常见的方案之一是将自适应比特率流传送至客户端。通过自适应比特率流，客户端可以在视频显示时，根据当前网络带宽、CPU 利用率和其他因素，切换至较高或较低的比特率流。媒体服务支持以下自适应比特率流式处理技术：HTTP 实时流式处理 (HLS)、平滑流式处理、MPEG DASH 和 HDS（仅适用于 Adobe PrimeTime/Access 许可证持有人）。

媒体服务提供的动态打包允许以媒体服务支持的流格式（MPEG DASH、HLS、Smooth Streaming、HDS）传送自适应比特率 MP4 或平滑流编码内容，而无须重新打包成这些流格式。

若要使用动态打包，必须执行下列操作：

- 将夹层（源）文件编码成一组自适应比特率 MP4 文件或自适应比特率平滑流文件（本教程稍后将演示编码步骤）。
- 针对要传送内容的*流式处理终结点*，获取至少一个流式处理单位。

通过动态打包，只需要存储及支付一种存储格式的文件，媒体服务将会根据客户端的要求创建并提供适当的响应。

若要更改流式处理保留单元数，请执行以下操作：

1. 在 [Azure 经典管理门户](https://manage.windowsazure.cn/)中单击“媒体服务”。然后，单击媒体服务的名称。

2. 选择“流式处理终结点”页。然后，单击要修改的流式处理终结点。

3. 若要指定流式处理单元数，请选择“缩放”选项卡并移动“保留容量”滑块。

    ![“缩放”页](./media/media-services-portal-get-started/media-services-origin-scale.png)

4. 单击“保存”按钮保存更改。

    分配所有新的单元大约需要 20 分钟才能完成。

    >[!NOTE]
    > 当前，将流式处理单位的任何正值设置回“无”可将流式处理功能禁用最多 1 小时。
    >
    > 为 24 小时期间指定的最大单位数将用于计算成本。有关定价详细信息，请参阅 [媒体服务定价详细信息](https://www.azure.cn/pricing/details/media-services/)。

## 上传内容

1. 在 [Azure 经典管理门户](http://manage.windowsazure.cn)中，单击“媒体服务”，然后单击媒体服务帐户名。
2. 选择“内容”页。
3. 单击该页上或者门户底部的“上传”按钮。
4. 在“上传内容”对话框中，浏览到所需的资产文件。单击该文件，然后单击“打开”或按 Enter。

    ![UploadContentDialog][uploadcontent]

5. 在“上传内容”对话框中，单击复选按钮以接受“文件”和“内容名称”。
6. 随后将开始上传，可从门户底部跟踪进度。

    ![JobStatus][status]

上传完成后，内容列表中会列出新的资产。根据约定，名称的末尾将附加“**-Source**”，以便将新内容作为编码任务的源内容进行跟踪。

![ContentPage][contentpage]

如果在上传过程停止后未更新文件大小值，请选择“同步元数据”按钮。这会将资产文件大小与存储中的实际文件大小同步，并刷新“内容”页上的值。

## 对内容进行编码

### 概述

若要通过 Internet 传送数字视频，必须对媒体进行压缩。媒体服务提供了一个媒体编码器，可让你指定如何为内容编码（例如，要使用的编解码器、文件格式、分辨率和比特率。）

使用 Azure 媒体服务最常见的方案之一是将自适应比特率流传送至客户端。通过自适应比特率流，客户端可以在视频显示时，根据当前网络带宽、CPU 利用率和其他因素，切换至较高或较低的比特率流。媒体服务支持以下自适应比特率流式处理技术：HTTP 实时流式处理 (HLS)、平滑流式处理、MPEG DASH 和 HDS（仅适用于 Adobe PrimeTime/Access 许可证持有人）。

媒体服务所提供的动态打包可让你以媒体服务支持的流格式（MPEG DASH、HLS、Smooth Streaming 或 HDS）传送自适应比特率 MP4 或平滑流编码内容，而无须重新打包成这些流格式。

若要使用动态打包，必须执行下列操作：

- 将夹层（源）文件编码成一组自适应比特率 MP4 文件或自适应比特率平滑流文件（本教程稍后将演示编码步骤）。
- 针对要传送内容的流式处理终结点，获取至少一个按需流式处理单位。有关详细信息，请参阅[如何缩放按需流式处理保留单元](./media-services-manage-origins.md#scale_streaming_endpoints)。

通过动态打包，只需要存储及支付一种存储格式的文件，媒体服务将会根据客户端的要求创建并提供适当的响应。

请注意，除了能够使用动态打包功能以外，点播流保留单元还提供可购买的专用流出容量（以 200 Mbps 为增量来购买）。默认情况下，按需流式处理在共享实例模型中配置，该模型的服务器资源（例如计算或出口容量）与所有其他用户共享。若要增加按需流式处理吞吐量，建议购买按需流式处理保留单元。

### 编码

本部分介绍通过 Azure 经典管理门户使用 Media Encoder Standard 为内容编码时可以执行的步骤。

1.  选择要编码的文件。如果此文件类型支持编码，则“内容”页底部将启用“处理”按钮。
4. 在“处理”对话框中，选择“Media Encoder Standard”处理器。
5. 选择其中一个“编码配置”。

    ![Process2][process2]  

    [媒体编码器标准版的任务预设字符串](https://msdn.microsoft.com/zh-cn/library/mt269960)主题说明了每个预设的含义。

5. 然后，输入所需的友好输出内容名称或接受默认值。然后，单击复选按钮开始编码操作。可在门户底部跟踪进度。
6. 选择“确定”。

    完成编码后，“内容”页将包含已编码的文件。

    若要查看编码作业的进度，请切换到“作业”页。

    如果在完成编码后未更新文件大小值，请选择“同步元数据”按钮。这会将输出资产文件大小与存储中的实际文件大小同步，并刷新“内容”页上的值。

## 发布内容

### 概述

若要为用户提供可用于流式传输内容或下载内容的 URL，首先需要通过创建定位符来“发布”资产。定位符提供对资产中所含文件的访问权限。媒体服务支持两种类型的定位符：用于流媒体（例如 MPEG DASH、HLS 或平滑流式处理）的 OnDemandOrigin 定位符，以及用于下载媒体文件的访问签名 (SAS) 定位符。

使用 Azure 经典管理门户发布资产时，系统将创建定位符并提供基于 OnDemand 的 URL（如果资产包含 .ism 文件）或 SAS URL。

SAS URL 采用以下格式。

```
{blob container name}/{asset name}/{file name}/{SAS signature}
```

流式处理 URL 采用以下格式，你可以用它来播放平滑流资产。

```
{streaming endpoint name-media services account name}.streaming.mediaservices.chinacloudapi.cn/{locator ID}/{filename}.ism/Manifest
```

若要生成 HLS 流式处理 URL，请将 (format=m3u8-aapl) 附加到 URL。

```
{streaming endpoint name-media services account name}.streaming.mediaservices.chinacloudapi.cn/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)
```

若要生成 MPEG DASH 流式处理 URL，请将 (format=mpd-time-csf) 追加到 URL。

```
{streaming endpoint name-media services account name}.streaming.mediaservices.chinacloudapi.cn/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)
```

定位符附带到期日期。使用门户发布资产时，会创建到期日期在 100 年后的定位符。

>[!NOTE]
> 如果使用门户在 2015 年 3 月之前创建了定位符，则会创建到期日期在两年后的定位符。

若要更新定位符的到期日期，请使用 [REST](https://docs.microsoft.com/zh-cn/rest/api/media/operations/locator) 或 [.NET](https://msdn.microsoft.com/zh-cn/library/azure/microsoft.windowsazure.mediaservices.client.ilocator.update(v=azure.10).aspx) API。请注意，更新 SAS 定位符的到期日期时，URL 会发生变化。

### 发布

若要使用门户发布资产，请执行以下操作：

1. 选择资源。
2. 然后单击“发布”按钮。

 ![PublishedContent][publishedcontent]  

## 从门户播放内容

Azure 经典管理门户提供了可用于测试视频的内容播放器。

单击所需的视频，然后单击门户底部的“播放”按钮。

请注意以下事项：

- 确保视频已发布。
- **媒体服务内容播放器**从默认的流式处理终结点播放。如果要从非默认流式处理终结点播放，请使用其他播放器。例如 [Azure 媒体服务播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。

![AMSPlayer][AMSPlayer]  

<!-- Anchors. -->

<!-- URLs. -->
[Azure Management Portal]: http://manage.windowsazure.cn/

<!-- Images -->
[portaloverview]: ./media/media-services-portal-get-started/media-services-content-page.png
[publishedcontent]: ./media/media-services-portal-get-started/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-portal-get-started/UploadContent.png
[status]: ./media/media-services-portal-get-started/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-portal-get-started/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-portal-get-started/media-services-process-video2.png
[encrypt]: ./media/media-services-manage-content/media-services-encrypt-content.png
[AMSPlayer]: ./media/media-services-portal-get-started/media-services-portal-player.png

<!---HONumber=Mooncake_Quality_Review_1118_2016-->