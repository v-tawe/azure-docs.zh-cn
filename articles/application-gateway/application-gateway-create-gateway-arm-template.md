
---
title: 使用 Azure Resource Manager 模板创建应用程序网关 | Microsoft Docs
description: 本页提供有关使用 Azure Resource Manager 模板创建 Azure 应用程序网关的说明
documentationcenter: na
services: application-gateway
author: georgewallace
manager: carmonm
editor: tysonn

ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/06/2016
ms.author: gwallace

---
# 使用 Azure Resource Manager 模板创建应用程序网关
Azure 应用程序网关是第 7 层负载平衡器。它在不同服务器之间提供故障转移和性能路由 HTTP 请求，而不管它们是在云中还是本地。应用程序网关具有以下应用程序传递功能：HTTP 负载平衡、基于 Cookie 的会话相关性和安全套接字层 (SSL) 卸载。

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure 经典 PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager 模板](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)
> 
> 

了解如何从 GitHub 下载并修改现有 Azure Resource Manager 模板，以及如何通过 GitHub、PowerShell 和 Azure CLI 部署该模板。

如果你只是直接从 GitHub 部署 Azure Resource Manager 模板，而不进行任何更改，请跳到“从 GitHub 部署模板”。

## 方案
在此方案中，你将：

* 创建包含两个实例的应用程序网关。
* 创建名为 VirtualNetwork1 且包含 10.0.0.0/16 保留 CIDR 块的虚拟网络。
* 创建名为 Appgatewaysubnet 且使用 10.0.0.0/28 作为其 CIDR 块的子网。
* 为需要用来进行流量负载平衡的 Web 服务器设置两个先前已配置的后端 IP。在此模板示例中，后端 IP 是 10.0.1.10 和 10.0.1.11。

> [!NOTE]
> 这些设置是适用于此模板的参数。若要自定义模板，你可以更改规则、侦听程序，以及用于打开 azuredeploy.json 的 SSL。
> 
> 

![方案](./media/application-gateway-create-gateway-arm-template/scenario-arm.png)

## 下载 Azure Resource Manager 模板并对其进行了解
可以从 GitHub 下载用于创建虚拟网络和两个子网的现有 Azure Resource Manager 模板，进行任何所需的更改，然后重用该模板。为此，请按照以下步骤操作：

1. 导航到 [Create Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-create)（创建应用程序网关）。
2. 单击 **azuredeploy.json**，然后单击 **RAW**。
3. 将该文件保存到你计算机上的本地文件夹。
4. 如果你熟悉 Azure Resource Manager 模板，则跳到步骤 7。
5. 打开保存的文件，并查看 **parameters** 下第 5 行中的内容。Azure Resource Manager 模板参数提供了在部署过程中可以填充的值的占位符。
   
   | 参数 | 说明 |
   | --- | --- |
   | **位置** |将创建应用程序网关的 Azure 区域 |
   | **VirtualNetwork1** |新虚拟网络的名称 |
   | **addressPrefix** |虚拟网络的地址空间，采用 CIDR 格式 |
   | **ApplicationGatewaysubnet** |应用程序网关子网的名称 |
   | **subnetPrefix** |应用程序网关子网的 CIDR 块 |
   | **skuname** |SKU 实例大小 |
   | **容量** |实例数 |
   | **backendaddress1** |第一个 Web 服务器的 IP 地址 |
   | **backendaddress2** |第二个 Web 服务器的 IP 地址 |

    >[AZURE.IMPORTANT] 在 GitHub 中维护的 Azure Resource Manager 模板可能随着时间的推移发生变化。请确保在使用该模板之前对其进行检查。

1. 查看 **resources** 下的内容，并注意以下项：
   
   * **type**。模板创建的资源的类型。在本例中，该类型为 **Microsoft.Network/applicationGateways**，表示应用程序网关。
   * **name**。资源的名称。请注意 **[parameters('applicationGatewayName')]** 的使用，这意味着在部署过程中将通过用户输入或参数文件输入的方式提供该名称。
   * **properties**。资源的属性列表。此模板在应用程序网关创建期间，使用虚拟网络与公共 IP 地址。
2. 导航回到 [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create)。
3. 单击 **azuredeploy-paremeters.json**，然后单击 **RAW**。
4. 将该文件保存到你计算机上的本地文件夹。
5. 打开保存的文件并编辑参数的值。使用以下值部署本方案中所述的应用程序网关。
   
       {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       {
       "location" : {
       "value" : "West US"
       },
       "addressPrefix": {
       "value": "10.0.0.0/16"
       },
       "subnetPrefix": {
       "value": "10.0.0.0/24"
       },
       "skuName": {
       "value": "Standard_Small"
       },
       "capacity": {
       "value": 2
       },
       "backendIpAddress1": {
       "value": "10.0.1.10"
       },
       "backendIpAddress2": {
       "value": "10.0.1.11"
       }
       }
6. 保存文件。可以使用联机 JSON 验证工具（例如 [JSlint.com](http://www.jslint.com/)）测试 JSON 模板和参数模板。

## 使用 PowerShell 部署 Azure Resource Manager 模板
如果从未使用过 Azure PowerShell，请参阅 [How to install and configure Azure PowerShell](../powershell-install-configure.md)（如何安装和配置 Azure PowerShell），并按照说明进行操作，以登录到 Azure 并选择订阅。

### 步骤 1
    Login-AzureRmAccount

### 步骤 2
检查该帐户的订阅。

    Get-AzureRmSubscription

系统将提示用户使用凭据进行身份验证。<BR>

### 步骤 3
选择要使用的 Azure 订阅。<BR>

    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### 步骤 4
如有必要，请使用 **New-AzureResourceGroup** cmdlet 创建资源组。在以下示例中，在“美国东部”位置创建名为 AppgatewayRG 的资源组。

    New-AzureRmResourceGroup -Name AppgatewayRG -Location "East US"

运行 **New-AzureRmResourceGroupDeployment** cmdlet，使用在前面下载并修改的模板和参数文件部署新虚拟网络。

    New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
         -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json

## 使用 Azure CLI 部署 Azure Resource Manager 模板
若要使用 Azure CLI 部署下载的 Azure Resource Manager 模板，请执行以下步骤：

### 步骤 1
如果从未使用过 Azure CLI，请参阅[安装和配置 Azure CLI](../xplat-cli-install.md)，并按照说明进行操作，直到选择 Azure 帐户和订阅。

### 步骤 2
运行 **azure config mode** 命令以切换到资源管理器模式，如下所示。

    azure config mode arm

下面是上述命令的预期输出：

    info:    New mode is arm

### 步骤 3
如有必要，请运行 **azure group create** 命令创建新资源组，如下所示。请注意命令的输出。在输出后显示的列表说明了所用的参数。有关资源组的详细信息，请访问 [Azure Resource Manager overview](../resource-group-overview.md)（Azure Resource Manager 概述）。

    azure group create -n appgatewayRG -l eastus

**-n（或 --name）**。新资源组的名称。在本方案中为 *appgatewayRG*。

**-l（或 --location）**。将创建新资源组的 Azure 区域。在本方案中为 *eastus*。

### 步骤 4
运行 **azure group deployment create** cmdlet，使用在前面下载并修改的模板和参数文件部署新虚拟网络。在输出后显示的列表说明了所用的参数。

    azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

## 使用“单击部署”来部署 Azure Resource Manager 模板
“单击部署”是另一种使用 Azure Resource Manager 模板的方式。这是将模板与 Azure 门户配合使用的简便方法。

### 步骤 1
转到[创建使用公共 IP 的应用程序网关](https://azure.microsoft.com/documentation/templates/101-application-gateway-public-ip/)。

### 步骤 2
单击**“部署到 Azure”**。

![部署到 Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### 步骤 3
在门户上填写部署模板的参数，然后单击“确定”。

![Parameters](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### 步骤 4
选择“法律条款”，然后单击“购买”。

### 步骤 5
在“自定义部署”边栏选项卡上，单击“创建”。

## 后续步骤
如果你要配置 SSL 卸载，请参阅 [Configure an application gateway for SSL offload](application-gateway-ssl.md)（配置应用程序网关以进行 SSL 卸载）。

如果你想要将应用程序网关配置为与内部负载平衡器配合使用，请参阅 [Create an application gateway with an internal load balancer (ILB)](application-gateway-ilb.md)（创建具有内部负载平衡器 (ILB) 的应用程序网关）。

如需负载平衡选项的其他常规信息，请参阅：

* [Azure 负载平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure 流量管理器](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0921_2016-->