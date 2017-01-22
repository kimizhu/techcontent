---
title: 设置开发环境 | Azure
description: 安装运行时、SDK 和工具，并创建本地开发群集。完成此设置后，即可开始生成应用程序。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 

ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/26/2016
wacn.date: 01/03/2017
ms.author: ryanwi
---

# 准备开发环境

[!INCLUDE [azure-sdk-developer-differences](../../includes/azure-sdk-developer-differences.md)]

>[!div class="op_single_selector"]
- [Windows](./service-fabric-get-started.md)
- [Linux](./service-fabric-get-started-linux.md)
- [OSX](./service-fabric-get-started-mac.md)

 若要在开发计算机上生成并运行 [Azure Service Fabric 应用程序][1]，请安装运行时、SDK 和工具。此外，还需执行 SDK 中包含的 Windows PowerShell 脚本。

## 先决条件
### 支持的操作系统版本
支持使用以下操作系统版本进行开发：

- Windows 7
- Windows 8/Windows 8.1
- Windows Server 2012 R2
- Windows 10

>[!NOTE]
> 默认情况下，Windows 7 仅包含 Windows PowerShell 2.0。Service Fabric PowerShell cmdlet 需要 PowerShell 3.0 或更高版本。可以从 Microsoft 下载中心[下载 Windows PowerShell 5.0][powershell5-download]。

## 安装运行时、SDK 和工具
Web 平台安装程序为 Service Fabric 开发提供两种配置：

- [安装适用于 Visual Studio 2015 的 Service Fabric 运行时、SDK 和工具（需要 Visual Studio 2015 Update 2 或更高版本）][full-bundle-vs2015]
- [仅安装 Service Fabric 运行时和 SDK（不安装 Visual Studio 工具）][core-sdk]

##<a name="enable-powershell-script-execution"></a> 启用 PowerShell 脚本执行

Service Fabric 使用 Windows PowerShell 脚本创建本地开发群集和部署 Visual Studio 中的应用程序。默认情况下，Windows 会阻止这些脚本运行。若要启用它们，你必须修改你的 PowerShell 执行策略。以管理员身份打开 PowerShell 并输入以下命令：

    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser

## 后续步骤
完成设置开发环境之后，便可开始生成和运行应用。

- [在 Visual Studio 中创建你的第一个 Service Fabric 应用程序](./service-fabric-create-your-first-application-in-visual-studio.md)
- [了解如何在本地群集上部署和管理应用程序](./service-fabric-get-started-with-a-local-cluster.md)
- [了解编程模型：Reliable Services 和 Reliable Actors](./service-fabric-choose-framework.md)

- [使用 Service Fabric 资源管理器可视化群集](./service-fabric-visualizing-your-cluster.md)

[1]: https://www.azure.cn/home/features/service-fabric "Service Fabric 活动页"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI 链接"
[full-bundle-dev15]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI 链接"
[core-sdk]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI 链接"
[powershell5-download]: https://www.microsoft.com/en-us/download/details.aspx?id=50395

<!---HONumber=Mooncake_Quality_Review_1230_2016-->