---
title: "使用 Azure 门户创建服务总线命名空间 | Microsoft Docs"
description: "为开始使用服务总线，你将需要一个命名空间。 下面介绍了如何使用 Azure 门户创建一个。"
services: service-bus
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/22/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a341d32149a84b9959afb6c3c1796c66c4d593cd


---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>使用 Azure 门户创建服务总线命名空间。
命名空间是一个适用于所有消息传送组件的公用容器。 多个队列和主题可以位于一个命名空间中，命名空间通常用作应用程序容器。 目前有两种不同方法可用来创建服务总线命名空间。

1. Azure 门户（这篇文章）
2. [Resource Manager 模板][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>在 Azure 门户中创建命名空间
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

祝贺你！ 现在已创建一个服务总线消息传送命名空间。

## <a name="next-steps"></a>后续步骤
签出 [GitHub 示例][github-samples]，它们演示了 Azure 服务总线消息传送中的某些更高级的功能。

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples



<!--HONumber=Nov16_HO2-->


