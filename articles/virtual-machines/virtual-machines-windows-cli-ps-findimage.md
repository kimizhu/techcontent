<properties
   pageTitle="导航和选择 Windows VM 映像 | Azure"
   description="了解在使用资源管理器部署模型创建 Windows 虚拟机时如何确定映像的确定发布者、产品和 SKU。"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-windows"
   ms.date="06/06/2016"
   wacn.date="07/28/2016"/>

# 使用 Windows PowerShell 和 Azure CLI 来浏览和选择 Windows 虚拟机映像

## 常用 Windows 映像表

| PublisherName | 产品 | SKU |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| MicrosoftWindowsServer           | WindowsServer                              | 2012-Datacenter               |
| MicrosoftWindowsServer           | WindowsServer                              | 2012-R2-Datacenter |
| MicrosoftWindowsServer           | WindowsServer                              | 2008-R2-SP1 |
| MicrosoftWindowsServer           | WindowsServer                              | Windows-Server-Technical-Preview |
| MicrosoftWindowsServerHPCPack    | WindowsServerHPCPack                       | 2012R2                           |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=Mooncake_0118_2016-->