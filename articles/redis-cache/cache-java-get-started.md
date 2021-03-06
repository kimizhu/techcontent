---
title: 如何将 Azure Redis 缓存与 Java 配合使用 | Azure
description: 开始将 Azure Redis 缓存与 Java 配合使用
services: redis-cache
documentationcenter: ''
author: steved0x
manager: douge
editor: ''

ms.assetid: 29275a5e-2e39-4ef2-804f-7ecc5161eab9
ms.service: cache
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 01/06/2017
wacn.date: 02/10/2017
ms.author: sdanie
---

# 如何将 Azure Redis 缓存与 Java 配合使用
> [!div class="op_single_selector"]
- [.NET](./cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](./cache-web-app-howto.md)
- [Node.js](./cache-nodejs-get-started.md)
- [Java](./cache-java-get-started.md)
- [Python](./cache-python-get-started.md)

[!INCLUDE [azure-sdk-developer-differences](../../includes/azure-sdk-developer-differences.md)]

Azure Redis 缓存可让你访问 Azure.cn 管理的专用 Redis 缓存。可从 Azure 内部的任何应用程序访问缓存。

本主题说明如何将 Azure Redis 缓存与 Java 配合使用。

## 先决条件
[Jedis](https://github.com/xetorthio/jedis) - Redis 的 Java 客户端

本教程使用 Jedis，但你可以使用 [http://redis.io/clients](http://redis.io/clients) 中列出的任何 Java 客户端。

## 在 Azure 上创建 Redis 缓存
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a> 检索主机名和访问密钥
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## 使用 SSL 安全地连接到缓存
最新版本的 [jedis](https://github.com/xetorthio/jedis) 支持使用 SSL 连接到 Azure Redis 缓存。以下示例显示了如何使用 SSL 终结点 6380 连接到 Azure Redis 缓存。将 `<name>` 替换为缓存名称，将 `<key>` 替换为主密钥或辅助密钥，如前面的[检索主机名和访问密钥](#retrieve-the-host-name-and-access-keys)部分中所述。

```
boolean useSsl = true;
/* In this line, replace <name> with your cache name: */
JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.chinacloudapi.cn", 6379, useSsl);
shardInfo.setPassword("<key>"); /* Use your access key. */
```

## 在缓存中添加一些内容并检索此内容
```
package com.mycompany.app;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisShardInfo;

public class App
{
  public static void main( String[] args )
  {
    boolean useSsl = true;
    /* In this line, replace <name> with your cache name: */
    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.chinacloudapi.cn", 6379, useSsl);
    shardInfo.setPassword("<key>"); /* Use your access key. */
    Jedis jedis = new Jedis(shardInfo);
    jedis.set("foo", "bar");
    String value = jedis.get("foo");
  }
}
```

## 后续步骤
* [启用缓存诊断](./cache-how-to-monitor.md#EnableDiagnostics)，以便可以[监视](./cache-how-to-monitor.md)缓存的运行状况。
* 阅读官方 [Redis 文档](http://redis.io/documentation)。

<!---HONumber=Mooncake_0206_2017-->
<!--Update_Description: change the python code to use ssl instead of nonssl-->