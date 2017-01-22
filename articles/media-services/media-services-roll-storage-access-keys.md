---
title: 轮转存储访问密钥后更新媒体服务 | Azure
description: 本文提供有关在轮转存储访问密钥后如何更新媒体服务的指导。
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
ms.date: 09/26/2016
wacn.date: 12/27/2016
ms.author: milangada;cenkdin;juliako
---

#如何：轮转存储访问密钥后更新媒体服务

创建新的 Azure 媒体服务帐户时，系统还会要求选择用于存储媒体内容的 Azure 存储帐户。请注意，可以向媒体服务帐户[添加多个存储帐户](./meda-services-managing-multiple-storage-accounts.md)。

创建新的存储帐户后，Azure 将生成两个 512 位存储访问密钥，用于对对存储帐户的访问进行身份验证。为保持存储连接更加安全，建议定期重新生成并轮转存储访问密钥。将提供两个访问密钥（主密钥和辅助密钥），以便在重新生成其中一个访问密钥时，始终能够使用另一个访问密钥连接到存储帐户。此过程也称为“轮转访问密钥”。

媒体服务依赖于向其提供的存储密钥。具体而言，用于流式处理或下载资产的定位符依赖于指定的存储访问密钥。创建 AMS 帐户后，它默认依赖于主存储访问密钥，但用户可以更新 AMS 的存储密钥。必须遵循本主题中所述的步骤，确保媒体服务知道要使用哪个密钥。此外，轮转存储访问密钥时，需确保更新定位符，以免流式处理服务中断（本主题也介绍了此步骤）。

>[!NOTE]
><p>如果你有多个存储帐户，请对每个存储帐户执行此过程。<p>在对生产帐户执行本主题中所述的步骤之前，请确保对生产前帐户测试这些步骤。

## 步骤 1：重新生成辅助存储访问密钥

首先，请重新生成辅助存储密钥。默认情况下，媒体服务不使用辅助密钥。有关如何轮转存储密钥的信息，请参阅[如何：查看、复制和重新生成存储访问密钥](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。

##<a id="step2"></a>步骤 2：将媒体服务更新为使用新的辅助存储密钥

将媒体服务更新为使用辅助存储访问密钥。可以使用以下两种方法之一，将重新生成的存储密钥同步到媒体服务。

- 使用 Azure 经典管理门户：选择媒体服务帐户，然后单击门户窗口底部的“管理密钥”图标。根据要与媒体服务同步的存储密钥，选择“同步主密钥”或“同步辅助密钥按钮。在本例中，使用辅助密钥。

- 使用媒体服务管理 REST API。

以下代码示例演示了如何构造 https://endpoint/*subscriptionId*/services/mediaservices/Accounts/*accountName*/StorageAccounts/*storageAccountName*/Key 请求，以便将指定的存储密钥与媒体服务同步。在本例中，使用辅助存储密钥值。有关详细信息，请参阅[如何：使用媒体服务管理 REST API](https://docs.microsoft.com/zh-cn/rest/api/media/management/how-to-use-media-services-management-rest-api)。

        public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
        {
            var clientCert = GetCertificate(CertThumbprint);

            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
                Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
            request.Method = "PUT";
            request.ContentType = "application/json; charset=utf-8";
            request.Headers.Add("x-ms-version", "2011-10-01");
            request.Headers.Add("Accept-Encoding: gzip, deflate");
            request.ClientCertificates.Add(clientCert);

            using (var streamWriter = new StreamWriter(request.GetRequestStream()))
            {
                streamWriter.Write("\"");
                streamWriter.Write(storageAccountKey);
                streamWriter.Write("\"");
                streamWriter.Flush();
            }

            using (var response = (HttpWebResponse)request.GetResponse())
            {
                string jsonResponse;
                Stream receiveStream = response.GetResponseStream();
                Encoding encode = Encoding.GetEncoding("utf-8");
                if (receiveStream != null)
                {
                    var readStream = new StreamReader(receiveStream, encode);
                    jsonResponse = readStream.ReadToEnd();
                }
            }
        }

完成此步骤后，按以下步骤中所示，更新（依赖于旧存储密钥的）现有定位符。

>[!NOTE]
>在对媒体服务执行任何操作（例如创建新定位符）之前等待 30 分钟，以防对挂起的作业产生任何影响。

##<a name="step3" id="step-3-update-locators"></a>步骤 3：更新定位符 
>[!NOTE]
>轮转存储访问密钥时，需确保更新现有定位符，以免流式处理服务中断。

将新存储密钥与 AMS 同步后，等待至少 30 分钟。然后，可以重新创建 OnDemand 定位符，使其依赖于指定的存储密钥并保留现有 URL。

请注意，更新（或重新创建）SAS 定位符时，URL 始终会变化。

>[!NOTE]
> 若要确保保留 OnDemand 定位符的现有 URL，需删除现有定位符并新建具相同 ID 的定位符。

以下 .NET 示例演示如何重新创建具相同 ID 的定位符。

    private static ILocator RecreateLocator(CloudMediaContext context, ILocator locator)
    {
        // Save properties of existing locator.
        var asset = locator.Asset;
        var accessPolicy = locator.AccessPolicy;
        var locatorId = locator.Id;
        var startDate = locator.StartTime;
        var locatorType = locator.Type;
        var locatorName = locator.Name;

        // Delete old locator.
        locator.Delete();

        if (locator.ExpirationDateTime <= DateTime.UtcNow)
        {
            throw new Exception(String.Format(
                "Cannot recreate locator Id={0} because its locator expiration time is in the past",
                locator.Id));
        }

        // Create new locator using saved properties.
        var newLocator = context.Locators.CreateLocator(
            locatorId,
            locatorType,
            asset,
            accessPolicy,
            startDate,
            locatorName);

        return newLocator;
    }

##步骤 5：重新生成主存储访问密钥

重新生成主存储访问密钥。有关如何轮转存储密钥的信息，请参阅[如何：查看、复制和重新生成存储访问密钥](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。

##步骤 6：将媒体服务更新为使用新的主存储密钥

使用[步骤 2](./media-services-roll-storage-access-keys.md#step2) 中所述过程，不过此次将新的主存储访问密钥与媒体服务帐户同步。

>[!NOTE]
>在对媒体服务执行任何操作（例如创建新定位符）之前等待 30 分钟，以防对挂起的作业产生任何影响。

##步骤 7：更新定位符  

30 分钟后，可重新创建 OnDemand 定位符，使其依赖于新的主存储密钥并保留现有 URL。

使用[步骤 3](./media-services-roll-storage-access-keys.md#step-3-update-locators) 中所述过程。

<!---HONumber=Mooncake_Quality_Review_1202_2016-->