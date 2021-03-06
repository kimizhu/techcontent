---
title: 管理 Azure SQL 数据仓库中的计算能力 (REST) | Azure
description: 用于管理计算能力的 PowerShell 任务。通过调整 DWU 缩放计算资源。或者，暂停和恢复计算资源来节省成本。
services: sql-data-warehouse
documentationCenter: NA
authors: barbkess
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
wacn.date: 12/12/2016
ms.author: barbkess;sonyama
---

# 管理 Azure SQL 数据仓库中的计算能力 (REST)

> [!div class="op_single_selector"]
- [概述](./sql-data-warehouse-manage-compute-overview.md)
- [Azure 门户预览](./sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](./sql-data-warehouse-manage-compute-powershell.md)
- [REST](./sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](./sql-data-warehouse-manage-compute-tsql.md)

通过扩大计算资源和内存来提升性能，从而满足工作负荷不断变化的需求。通过在非高峰时段缩减资源或同时暂停计算来节省成本。

此任务集合使用 Azure 门户预览实现：

- 缩放计算
- 暂停计算
- 恢复计算

若要了解相关信息，请参阅[管理计算概述][]。

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## 缩放计算能力

[!INCLUDE [SQL Data Warehouse scale DWUs description（SQL 数据仓库缩放 DWU 说明）](../../includes/sql-data-warehouse-scale-dwus-description.md)]

若要更改 DWU，请使用[创建或更新数据库][] REST API。以下示例将托管在服务器 MyServer 上的数据库 MySQLDW 的服务级别目标设置为 DW1000。该服务器位于名为 ResourceGroup1 的 Azure 资源组中。

```
PUT https://management.chinacloudapi.cn/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/MyServer/databases/MySQLDW?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

<a name="pause-compute-bk"></a>

## 暂停计算

[!INCLUDE [SQL Data Warehouse pause description（SQL 数据仓库暂停说明）](../../includes/sql-data-warehouse-pause-description.md)]

若要暂停数据库，请使用[暂停数据库][] REST API。以下示例将暂停 Server01 服务器上托管的 Database02 数据库。该服务器位于名为 ResourceGroup1 的 Azure 资源组中。

```
POST https://management.chinacloudapi.cn/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## 恢复计算

[!INCLUDE [SQL Data Warehouse resume description（SQL 数据仓库恢复说明）](../../includes/sql-data-warehouse-resume-description.md)]

若要启动数据库，请使用[恢复数据库][] REST API。以下示例将启动 Server01 服务器上托管的 Database02 数据库。该服务器位于名为 ResourceGroup1 的 Azure 资源组中。

```
POST https://management.chinacloudapi.cn/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## 后续步骤

有关其他管理任务，请参阅[管理概述][]。

<!--Image references-->

<!--Article references-->
[管理概述]: ./sql-data-warehouse-overview-manage.md
[管理计算概述]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[暂停数据库]: https://msdn.microsoft.com/zh-cn/library/azure/mt718817.aspx
[恢复数据库]: https://msdn.microsoft.com/zh-cn/library/azure/mt718820.aspx
[创建或更新数据库]: https://msdn.microsoft.com/zh-cn/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure portal]: http://portal.azure.cn/

<!---HONumber=Mooncake_1205_2016-->