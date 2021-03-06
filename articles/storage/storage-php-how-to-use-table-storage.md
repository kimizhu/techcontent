---
title: 如何通过 PHP 使用表存储 | Azure
description: 了解如何通过 PHP 使用表服务来创建和删除表以及插入、删除和查询表。
services: storage
documentationcenter: php
author: mmacy
manager: timlt
editor: tysonn

ms.assetid: 1e57f371-6208-4753-b2a0-05db4aede8e3
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 12/08/2016
wacn.date: 01/06/2017
ms.author: marsma
---

# 如何通过 PHP 使用表存储
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## 概述
本指南演示如何使用 Azure 表服务执行常见任务。示例是用 PHP 编写的，并使用了 [Azure SDK for PHP][download]。所涉及的任务包括创建和删除表以及在表中插入、删除和查询条目。有关 Azure 表服务的详细信息，请参阅[后续步骤](#next-steps)部分。

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 创建 PHP 应用程序
创建访问 Azure 表服务的 PHP 应用程序的唯一要求是从代码中引用 Azure SDK for PHP 中的类。可使用任何开发工具（包括“记事本”）创建应用程序。

在本指南中，将使用表服务功能，可在 PHP 应用程序中本地调用，或在 Azure Web 角色、辅助角色或网站中运行的代码内调用这些功能。

## 获取 Azure 客户端库
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## 配置应用程序以访问表服务
若要使用 Azure 表服务 API，需执行以下操作：

1. 使用 [require\_once][require_once] 语句引用 autoloader 文件，并
2. 引用所用的任意类。

下面的示例演示了如何包括 autoloader 文件并引用 **ServicesBuilder** 类。

> [!NOTE]
> 本文的示例假定用户已通过 Composer 安装了用于 Azure 的 PHP 客户端库。如果手动安装了这些库，需要引用 <code>WindowsAzure.php</code> autoloader 文件。

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

在下面的示例中，将始终显示 `require_once` 语句，但只会引用执行该示例所需的类。

## 设置 Azure 存储连接
若要实例化 Azure 表服务客户端，必须首先具有有效的连接字符串。表服务连接字符串的格式如下：

若要访问实时服务：

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey];EndpointSuffix=core.chinacloudapi.cn
```

若要访问模拟器存储：

```php
UseDevelopmentStorage=true
```

若要创建任何 Azure 服务客户端，需要使用 **ServicesBuilder** 类。可执行以下操作：

* 将连接字符串直接传递给此类或
* 使用 **CloudConfigurationManager (CCM)** 检查多个外部源以获取连接字符串：
  * 它默认支持一个外部源 - 环境变量
  * 你可通过扩展 **ConnectionStringSource** 类来添加新源

在此处列出的示例中，将直接传递连接字符串。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
```

## 创建表
利用 **TableRestProxy** 对象，可以使用 **createTable** 方法创建表。创建表时，可以设置表服务超时。（有关表服务超时的详细信息，请参阅[为表服务操作设置超时][table-service-timeouts]。）

```php
require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try	{
    // Create table.
    $tableRestProxy->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // http://msdn.microsoft.com/zh-cn/library/azure/dd179438.aspx
}
```

有关表名称的限制的信息，请参阅[了解表服务数据模型][table-data-model]。

## 向表中添加条目

若要将实体添加到表，请创建一个新的 **Entity** 对象并将其传递到 **TableRestProxy->insertEntity**。请注意，在创建实体时，你必须指定 `PartitionKey` 和 `RowKey`。这些值是条目的唯一标识符，查询它们比查询其他条目属性快得多。系统使用 `PartitionKey` 自动将表的实体分发到多个存储节点上。具有相同 `PartitionKey` 的实体存储在同一个节点上。（对存储在同一节点上的多个条目执行操作，执行效果优于对存储在不同节点上的条目执行的操作。） `RowKey` 是实体在分区中的唯一 ID。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                     EdmType::DATETIME,
                     new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableRestProxy->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/zh-cn/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

有关表属性和类型的信息，请参阅[了解表服务数据模型][table-data-model]。

**TableRestProxy** 类提供了用于插入实体的两个替代方法：**insertOrMergeEntity** 和 **insertOrReplaceEntity**。若要使用这些方法，请创建一个新的 **Entity**，并将其作为参数传递到上述任一方法。如果条目不存在，则每种方法都将插入条目。在实体已存在的情况下，如果属性已存在，则 **insertOrMergeEntity** 将更新属性值；如果属性不存在，则该方法将添加新属性，而 **insertOrReplaceEntity** 将完全替换现有实体。下面的示例演示如何使用 **insertOrMergeEntity**。如果实体具有 `PartitionKey`“tasksSeattle”并且 `RowKey`“1”不存在，则将插入该实体。但是，如果之前已插入该实体（如上面的示例所示），则将更新 `DueDate` 属性并添加 `Status` 属性。系统还将更新 `Description` 和 `Location` 属性，但使用的值实际上会使其保持不变。如果并非如示例所示添加后两个属性，而这两个数学已存在于目标条目上，则其现有值将保持不变。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try	{
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableRestProxy->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/zh-cn/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## 检索单个条目
利用 **TableRestProxy->getEntity** 方法，可以通过查询实体的 `PartitionKey` 和 `RowKey` 来检索它。在以下示例中，分区键 `tasksSeattle` 和行键 `1` 传递给 **getEntity** 方法。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try	{
    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/zh-cn/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## 检索分区中的所有条目
使用筛选器来构造实体查询（有关详细信息，请参阅[查询表和实体][filters]）。若要检索分区中的所有实体，请使用筛选器“PartitionKey eq *partition\_name* ”。下面的示例演示如何通过将筛选器传递到 **queryEntities** 方法来检索 `tasksSeattle` 分区中的所有实体。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try	{
    $result = $tableRestProxy->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/zh-cn/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## 检索分区中条目的子集
可使用上一示例中所用模式来检索分区中条目的子集。检索的实体子集将由所使用的筛选器确定（有关详细信息，请参阅[查询表和实体][filters]）。下面的示例演示如何使用筛选器检索具有特定的 `Location` 和早于指定日期的 `DueDate` 的所有实体。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try	{
    $result = $tableRestProxy->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/zh-cn/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## 检索条目属性的子集
查询可检索条目属性的子集。此方法称为“投影”，可减少带宽并提高查询性能，尤其适用于大型实体。若要指定要检索的属性，请将该属性的名称传递到 **Query->addSelectField** 方法。可以多次调用此方法来添加更多属性。执行 **TableRestProxy->queryEntities** 后，返回的实体将仅具有选定的属性。（若要返回表中条目的子集，请使用筛选器，如以上查询中所示。）

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try	{
    $result = $tableRestProxy->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/zh-cn/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## 更新条目
可通过对现有实体使用 **Entity->setProperty** 和 **Entity->addProperty** 方法并调用 **TableRestProxy->updateEntity** 来更新该实体。下面的示例将检索一个条目、修改一个属性、删除另一个属性并添加一个新属性。请注意，通过将属性的值设为 **null** 可删除该属性。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();

$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.

$entity->setPropertyValue("Location", null); //Removed Location.

$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try	{
    $tableRestProxy->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/zh-cn/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## 删除条目
若要删除实体，请将表名称以及实体的 `PartitionKey` 和 `RowKey` 传递到 **TableRestProxy->deleteEntity** 方法。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try	{
    // Delete entity.
    $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/zh-cn/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

请注意，为了进行并发检查，可以使用 **DeleteEntityOptions->setEtag** 方法并将 **DeleteEntityOptions** 对象作为第四个参数传递到 **deleteEntity**，来为要删除的实体设置 Etag。

## 批量进行表操作
利用 **TableRestProxy->batch** 方法，你可以通过一个请求执行多个操作。此处的模式涉及将操作添加到 **BatchRequest** 对象，然后将 **BatchRequest** 对象传递到 **TableRestProxy->batch** 方法。若要将操作添加到 **BatchRequest** 对象，可以多次调用以下任一方法：

* **addInsertEntity**（添加 insertEntity 操作）
* **addUpdateEntity**（添加 updateEntity 操作）
* **addMergeEntity**（添加 mergeEntity 操作）
* **addInsertOrReplaceEntity**（添加 insertOrReplaceEntity 操作）
* **addInsertOrMergeEntity**（添加 insertOrMergeEntity 操作）
* **addDeleteEntity**（添加 deleteEntity 操作）

下面的示例演示了如何通过单个请求执行 **insertEntity** 和 **deleteEntity** 操作：

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

 // Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                      EdmType::DATETIME,
                      new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try	{
    $tableRestProxy->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/zh-cn/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

有关对表操作进行批处理的详细信息，请参阅[执行实体组事务][entity-group-transactions]。

## 删除表
最后，若要删除表，请将表名传递到 **TableRestProxy->deleteTable** 方法。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try	{
    // Delete table.
    $tableRestProxy->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/zh-cn/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

##<a name="next-steps"></a> 后续步骤

既已了解 Azure 表服务的基础知识，可单击以下链接了解有关更复杂的存储任务的详细信息。

- 访问 [Azure 存储空间团队博客](http://blogs.msdn.com/b/windowsazurestorage/)

有关详细信息，另请参阅 [PHP 开发人员中心](/develop/php/)。

[download]: ../php-download-sdk.md
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/zh-cn/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/zh-cn/library/azure/dd179338.aspx
[filters]: http://msdn.microsoft.com/zh-cn/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/zh-cn/library/azure/dd894038.aspx

<!---HONumber=Mooncake_0103_2017-->