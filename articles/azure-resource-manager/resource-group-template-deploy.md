---
title: 使用 PowerShell 和模板部署资源 | Microsoft Docs
description: 使用 Azure 资源管理器和 Azure PowerShell 将资源部署到 Azure。 资源在 Resource Manager 模板中定义。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2017
ms.author: tomfitz
ms.openlocfilehash: 3378c13934a5a0743aa40ebb19940f1afa71fc71
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>使用 Resource Manager 模板和 Azure PowerShell 部署资源

本文介绍如何配合使用 Azure PowerShell 与资源管理器模板，以将资源部署到 Azure。 如果不熟悉部署和管理 Azure 解决方案的概念，请参阅 [Azure 资源管理器概述](resource-group-overview.md)。

所部署的 Resource Manager 模板可以是计算机上的本地文件，也可以是位于 GitHub 等存储库中的外部文件。 本文中部署的模板可在[示例模板](#sample-template)部分中找到，也可作为 [GitHub 中的存储帐户模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json)。

必要时，请使用 [Azure PowerShell 指南](/powershell/azure/overview)中的说明安装 Azure PowerShell 模块，然后运行 `Login-AzureRmAccount` 创建与 Azure 的连接。

<a id="deploy-local-template" />

## <a name="deploy-a-template-from-your-local-machine"></a>从本地计算机部署模板

将资源部署到 Azure 时，执行以下操作：

1. 登录到 Azure 帐户
2. 创建用作已部署资源的容器的资源组。 资源组名称只能包含字母数字字符、句点、下划线、连字符和括号。 它最多可以包含 90 个字符。 它不能以句点结尾。
3. 将定义了要创建的资源的模板部署到资源组

模板可以包括可用于自定义部署的参数。 例如，可以提供为特定环境（如开发环境、测试环境和生产环境）定制的值。 示例模板定义了存储帐户 SKU 的参数。

以下示例将创建一个资源组，并从本地计算机部署模板：

```powershell
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

部署可能需要几分钟才能完成。 完成后，会看到一条包含结果的消息：

```powershell
ProvisioningState       : Succeeded
```

## <a name="deploy-a-template-from-an-external-source"></a>从外部源部署模板

可能更愿意将 Resource Manager 模板存储在外部位置，而不是将它们存储在本地计算机上。 可以将模板存储在源控件存储库（例如 GitHub）中。 另外，还可以将其存储在 Azure 存储帐户中，以便在组织中共享访问。

若要部署外部模板，请使用 **TemplateUri** 参数。 使用示例中的 URI 从 GitHub 部署示例模板。

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS
```

前面的示例要求模板的 URI 可公开访问，它适用于大多数情况，因为模板应该不会包含敏感数据。 如果需要指定敏感数据（如管理员密码），请以安全参数的形式传递该值。 但是，如果不希望模板可公开访问，可以通过将其存储在专用存储容器中来保护它。 有关部署需要共享访问签名 (SAS) 令牌的模板的信息，请参阅[部署具有 SAS 令牌的专用模板](resource-manager-powershell-sas-token.md)。

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

在 Cloud Shell 中使用以下命令：

```powershell
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile "C:\users\ContainerAdministrator\CloudDrive\templates\azuredeploy.json" -storageAccountType Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>部署到多个资源组或订阅

通常情况下，将模板中的所有资源部署到单个资源组。 不过，在某些情况下，你可能希望将一组资源部署在一起但将其放置在不同的资源组或订阅中。 在单个部署中可以仅部署到五个资源组。 有关详细信息，[将 Azure 资源部署到多个订阅或资源组](resource-manager-cross-resource-group-deployment.md)。

<a id="parameter-file" />

## <a name="parameter-files"></a>参数文件

与在脚本中以内联值的形式传递参数相比，可能会发现使用包含参数值的 JSON 文件更为容易。 参数文件必须采用以下格式：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

请注意，parameters 部分包含与模板中定义的参数匹配的参数名称 (storageAccountType)。 参数文件针对该参数包含了一个值。 此值在部署期间自动传递给模板。 可以针对不同部署方案创建多个参数文件，并传入相应的参数文件。 

复制上面的示例，并将其保存为名为 `storage.parameters.json` 的文件。

若要传递本地参数文件，请使用 **TemplateParameterFile** 参数：

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

若要传递外部参数文件，请使用 **TemplateParameterUri** 参数：

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

可以在同一部署操作中使用内联参数和本地参数文件。 例如，可以在本地参数文件中指定某些值，并在部署期间添加其他内联值。 如果同时为本地参数文件中的参数和内联参数提供值，则内联值优先。

但是，使用外部参数文件时，不能传递是内联值或来自本地文件的其他值。 如果在 **TemplateParameterUri** 参数中指定参数文件，则将忽略所有内联参数。 提供外部文件中的所有参数值。 如果模板包括参数文件中无法包括的敏感值，可将该值添加到密钥保管库，或者以内联方式动态提供所有参数值。

如果模板包括的一个参数与 PowerShell 命令中的某个参数同名，PowerShell 使用后缀 **FromTemplate** 显示模板的参数。 例如，模板中名为 **ResourceGroupName** 的参数与 [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) cmdlet 中的 **ResourceGroupName** 参数冲突。 系统会提示提供 **ResourceGroupNameFromTemplate** 的值。 通常，不应将参数命名为与用于部署操作的参数的名称相同以避免这种混乱。

## <a name="test-a-template-deployment"></a>测试模板部署

若要测试模板和参数值而不实际部署任何资源，请使用 [Test-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment)。 

```powershell
Test-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

如果没有检测到错误，该命令在没有响应的情况下完成。 如果检测到错误，则该命令将返回一条错误消息。 例如，如果尝试为存储帐户 SKU 传递不正确的值，将返回以下错误：

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

如果模板有语法错误，该命令将返回一个错误，指示它无法分析该模板。 该消息会指出分析错误的行号和位置。

```powershell
Test-AzureRmResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

若要使用完整模式，请使用 `Mode` 参数：

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```

## <a name="sample-template"></a>示例模板

本文中的示例使用以下模板。 复制并将其另存为名为 storage.json 的文件。 要了解如何创建此模板，请参阅[创建第一个 Azure 资源管理器模板](resource-manager-create-first-template.md)。  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="next-steps"></a>后续步骤
* 本文中的示例将资源部署到默认订阅中的资源组。 若要使用其他订阅，请参阅[管理多个 Azure 订阅](/powershell/azure/manage-subscriptions-azureps)。
* 有关用于部署模板的完整示例脚本，请参阅 [Resource Manager 模板部署脚本](resource-manager-samples-powershell-deploy.md)。
* 若要了解如何在模板中定义参数，请参阅[了解 Azure 资源管理器模板的结构和语法](resource-group-authoring-templates.md)。
* 有关解决常见部署错误的提示，请参阅[排查使用 Azure 资源管理器时的常见 Azure 部署错误](resource-manager-common-deployment-errors.md)。
* 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](resource-manager-powershell-sas-token.md)。
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。

