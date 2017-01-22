---
title: IoT 中心开发人员指南主题 | Azure
description: Azure IoT 中心开发人员指南，其中介绍了 IoT 中心终结点、安全性、设备标识注册表、设备管理和消息传送
services: iot-hub
documentationCenter: .net
authors: dominicbetts
manager: timlt
editor: 

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
wacn.date: 01/04/2017
ms.author: dobett
---

# Azure IoT 中心开发人员指南

Azure IoT 中心是一项完全托管的服务，有助于在数百万个 IoT 设备和一个应用程序后端之间实现安全可靠的双向通信。

Azure IoT 中心提供：

* 使用每个设备的安全凭据和访问控制来保护通信安全。
* 提供可靠的设备到云和云到设备的超大规模消息传送。
* 通过最流行语言和平台的设备库来方便建立设备连接。

## 后续步骤

本 IoT 中心开发人员指南包括以下文章：

- [使用 IoT 中心发送和接收消息][devguide-messaging]说明 IoT 中心公开的消息传送功能（设备到云和云到设备）。此文章还包括以下主题的相关信息：消息格式和支持的通信协议以及这些协议使用的端口号等。
- [设备到云通信指南][lnk-d2c-guidance]可帮助在设备到云消息、设备孪生的报告属性和文件上传之间做出选择。
- [云到设备通信指南][lnk-c2d-guidance]可帮助在直接方法、设备孪生的所需属性和云到设备消息之间做出选择。
- [从设备上载文件][devguide-upload]介绍如何从设备上载文件。此文章还包括有关上载过程可以发送的通知等主题的信息。
- [管理 IoT 中心中的设备标识][devguide-identities]说明各 IoT 中心的设备标识注册表存储的信息，以及访问和修改这些信息的方式。
- [控制对 IoT 中心的访问][devguide-security]说明用于向设备和云组件授予 IoT 中心功能访问权限的安全模型。此文章包括有关使用令牌和 X.509 证书的信息，以及可以授予的权限的详细信息。
- [使用设备孪生同步状态和配置][devguide-device-twins]说明*设备孪生*概念，以及它公开的功能，如使用设备孪生来同步设备。此文章包括有关设备孪生中存储的数据的信息。
- [在设备上调用直接方法][devguide-directmethods]说明直接方法的生命周期，介绍有关如何从后端应用调用设备方法以及在设备上处理直接方法的信息。
- [在多台设备上计划作业][devguide-jobs]介绍如何在多台设备上计划作业。此文章介绍如何提交作业，以在执行直接方法，使用设备孪生更新设备时执行任务。它还介绍如何查询作业的状态。
- [参考 - IoT 中心终结点][devguide-endpoints]说明了每个 IoT 中心针对运行时和管理操作公开的各种终结点。此文章还介绍如何使用现场网关使某些设备能够连接到 IoT 中心终结点。
- [参考 - 设备孪生、方法和作业的查询语言][devguide-query]说明了可用于从中心检索有关设备孪生和作业的信息的查询语言。
- [参考 - 配额和限制][devguide-quotas]总结了 IoT 中心服务中设置的配额，以及当超过配额时可以预期看到的限制行为。
- [参考 - 设备和服务 SDK][devguide-sdks] 列出了在开发与 IoT 中心交互的设备和服务应用程序时可以使用的 SDK。此文章包括指向联机 API 文档的链接。
- [参考 - IoT 中心 MQTT 支持][devguide-mqtt]详细介绍了 IoT 中心如何支持 MQTT 协议。此文章介绍对内置到 SDK 的 MQTT 协议的支持，并提供有关直接使用 MQTT 协议的信息。
- [词汇表][devguide-glossary]是与 IoT 中心相关的常见术语的列表。

[devguide-messaging]: ./iot-hub-devguide-messaging.md
[devguide-upload]: ./iot-hub-devguide-file-upload.md
[devguide-identities]: ./iot-hub-devguide-identity-registry.md
[devguide-security]: ./iot-hub-devguide-security.md
[devguide-device-twins]: ./iot-hub-devguide-device-twins.md
[devguide-directmethods]: ./iot-hub-devguide-direct-methods.md
[devguide-jobs]: ./iot-hub-devguide-jobs.md
[devguide-endpoints]: ./iot-hub-devguide-endpoints.md
[devguide-quotas]: ./iot-hub-devguide-quotas-throttling.md
[devguide-query]: ./iot-hub-devguide-query-language.md
[devguide-sdks]: ./iot-hub-devguide-sdks.md
[devguide-mqtt]: ./iot-hub-mqtt-support.md
[devguide-glossary]: ./iot-hub-devguide-glossary.md
[lnk-c2d-guidance]: ./iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: ./iot-hub-devguide-d2c-guidance.md

<!---HONumber=Mooncake_Quality_Review_1230_2016-->