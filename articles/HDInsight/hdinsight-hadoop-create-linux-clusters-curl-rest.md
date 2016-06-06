<!-- not suitable for Mooncake -->

<properties
   	pageTitle="在 HDInsight 中使用 cURL 和 Azure REST API 创建基于 Linux 的 Hadoop、HBase 或 Storm 群集 | Azure"
   	description="了解如何使用 cURL、Azure Resource Manager 模板和 Azure REST API 创建基于 Linux 的 HDInsight 群集。可以指定群集类型（Hadoop、HBase 或 Storm），或使用脚本来安装自定义组件。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.date="05/16/2016"
	wacn.date=""/>

#在 HDInsight 中使用 cURL 和 Azure REST API 创建基于 Linux 的群集

[AZURE.INCLUDE [选择器](../includes/hdinsight-selector-create-clusters.md)]

Azure REST API 允许你对托管在 Azure 平台中的服务执行管理操作，包括创建新资源（例如基于 Linux 的 HDInsight 群集）。在本文档中，你将学习如何创建 Azure Resource Manager 模板来配置 HDInsight 群集和关联的存储，然后使用 cURL 将模板部署到 Azure REST API，以创建新的 HDInsight 群集。

> [AZURE.IMPORTANT] 本文档中的步骤对 HDInsight 群集使用默认数目（4 个）的辅助角色节点。如果你计划使用 32 个以上的工作节点（在创建群集时或是在创建之后通过扩展群集进行），则必须选择至少具有 8 个核心和 14GB ram 的头节点大小。
>
> 有关节点大小和相关费用的详细信息，请参阅 [HDInsight pricing（HDInsight 定价）](/home/features/hdinsight/#price)。

##先决条件

[AZURE.INCLUDE [delete-cluster-warning](../includes/hdinsight-delete-cluster-warning.md)]


- **一个 Azure 订阅**。请参阅[获取 Azure 试用版](/pricing/1rmb-trial/)。

- __Azure CLI__。Azure CLI 用于创建服务主体，为针对 Azure REST API 的请求生成身份验证令牌时需要使用此主体。

    [AZURE.INCLUDE [use-latest-version](../includes/hdinsight-use-latest-cli.md)]

- __cURL__。可通过包管理系统获取此实用工具，也可以从 [http://curl.haxx.se/](http://curl.haxx.se/) 下载此实用工具。

    > [AZURE.NOTE] 如果使用 PowerShell 运行本文档中的命令，则必须先删除默认创建的 `curl` 别名。当你从 PowerShell 提示符使用 `curl` 命令时，此别名使用 Invoke-WebRequest PowerShell cmdlet 而不是 cURL，这会造成本文档中使用的许多命令返回错误。
    > 
    > 若要删除此别名，请从 PowerShell 提示符使用以下命令：
    >
    > `Remove-item alias:curl`
    >
    > 删除别名后，你应该能够使用系统上安装的 cURL 版本。

##创建模板

Azure 资源管理模板是描述__资源组__及其包含的所有资源（例如 HDInsight）的 JSON 文档。 基于此模板的方法可让你在一个模板中定义 HDInsight 所需的所有资源，并通过可将更改应用到组的__部署__，来统一整体管理组的更改。

模板通常划分为两个部分：模板本身，以及一个 parameters 文件，你将在其中填充特定于配置的值，例如，群集名称、管理员名称和密码。直接使用 REST API 时，必须将这些值合并到一个文件中。此 JSON 文档的格式为：

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

例如，下面是来自 [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password) 的模板与 parameters 文件的组合形式，它将创建基于 Linux 的群集，并使用密码来保护 SSH 用户帐户。

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["China North",
                        "China East",
                        "China East",
                        "Japan East",
                        "China East",
                        "China North",
                        "China East",
                        "China North",
                        "West Europe",
                        "China North"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.chinacloudapi.cn')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            },
                            {
                                "name": "workernode",
                                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "China North"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

本文档中的步骤将使用此示例。必须将文档末尾的 __Parameters__ 节中的占位符 _values_ 替换为要用于群集的值。

##登录到你的 Azure 订阅

遵循 [Connect to an Azure subscription from the Azure Command-Line Interface (Azure CLI)（从 Azure 命令行接口 (Azure CLI) 连接到 Azure 订阅）](/documentation/articles/xplat-cli-connect)中所述的步骤，并使用 `azure login` 连接到你的订阅。

##创建服务主体

> [AZURE.NOTE] 这些步骤是 [Authenticating a service principal with Azure Resource Manager（通过 Azure Resource Manager 对服务主体进行身份验证）](/documentation/articles/resource-group-authenticate-service-principal#authenticate-service-principal-with-password---azure-cli)文档的 _Authenticate service principal with a password - Azure CLI_（使用密码对服务主体进行身份验证 - Azure CLI）部分中提供的信息的缩减版本。这些步骤将创建一个新的服务主体，使用该服务主体可以对用于创建 HDInsight 群集等 Azure 资源的 REST API 请求进行身份验证。

1. 通过命令提示符、终端会话或 shell，使用以下命令列出你的 Azure 订阅。

        azure account list
        
    在列表中，选择你要使用的订阅并记下 __Id__ 列。这是__订阅 ID__，将在本文档的大多数步骤中用到。

2. 在 Azure Active Directory 中创建新应用程序。

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    将 `--name`、`--home-page` 和 `--identifier-uris` 的值替换为你自己的值。为新的 Active Directory 条目提供密码。
    
    > [AZURE.NOTE] 由于创建此应用程序的目的是通过服务主体进行身份验证，因此，`--home-page` 和 `--identifier-uris` 值无需引用托管在 Internet 上的实际网页；它们只需是唯一的 URI 即可。
    
    在返回的数据中，保存 __AppId__ 值。
    
        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK
    
3. 使用前面返回的 __AppId__ 值创建一个服务主体。

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
     在返回的数据中，保存__对象 ID__ 值。
     
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
4. 使用前面返回的__对象 ID__ 值向服务主体分配__所有者__角色。还必须使用前面获取的__订阅 ID__。
    
        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Owner -c /subscriptions/{SubscriptionID}/
        
    完成此命令后，服务主体能够以所有者的身份访问指定的订阅 ID。

##获取身份验证令牌

1. 使用以下命令查找订阅的__租户 ID__。

        azure account show -s <subscription ID>
        
    在返回的数据中，找到__租户 ID__。
    
        info:    Executing command account show
        data:    Name                        : MyAzureAccount
        data:    ID                          : 45a1014d-0f27-25d2-b838-b8f373d6d52e
        data:    State                       : Enabled
        data:    Tenant ID                   : 22f988bf-56f1-41af-91ab-3d7cd011db47
        data:    Is Default                  : true
        data:    Environment                 : AzureCloud
        data:    Has Certificate             : No
        data:    Has Access Token            : Yes
        data:    User name                   : myname@contoso.org
        data:    
        info:    account show command OK

2. 使用 Azure REST API 生成新令牌。

        curl -X "POST" "https://login.chinacloudapi.cn/TenantID/oauth2/token" \
        -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
        -H "Content-Type: application/x-www-form-urlencoded" \
        --data-urlencode "client_id=AppID" \
        --data-urlencode "grant_type=client_credentials" \
        --data-urlencode "client_secret=password" \
        --data-urlencode "resource=https://management.chinacloudapi.cn/"
    
    将 __TenantID__、__AppID__ 和 __password__ 替换为前面获取或使用的值。

    如果此请求成功，你将收到 200 系列响应，且响应正文包含一个 JSON 文档。

    此请求返回的 JSON 文档包含名为 __access\_token__ 的元素；此元素的值是访问令牌，必须使用它对本文档后续部分中使用的请求进行身份验证。
    
        {
            "token_type":"Bearer",
            "expires_in":"3599",
            "expires_on":"1463409994",
            "not_before":"1463406094",
            "resource":"https://management.chinacloudapi.cn/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
        }

##创建资源组

使用以下命令创建新的资源组。必须先创建组，才能创建 HDInsight 群集等资源。

* 将 __SubscriptionID__ 替换为创建服务主体时收到的订阅 ID。
* 将 __AccessToken__ 替换为在上一步骤中收到的访问令牌。
* 将 __DataCenterLocation__ 替换为要在其中创建资源组和资源的数据中心。例如“China East”。 
* 将 __ResourceGroupName__ 替换为要用于此组的名称：

```
curl -X "PUT" "https://management.chinacloudapi.cn/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

如果此请求成功，你将收到 200 系列响应，且响应正文包含一个 JSON 文档，其中包含有关组的信息。`"provisioningState"` 元素包含 `"Succeeded"` 值。

##创建部署

使用以下命令将群集配置（模板和参数值）部署到资源组。

* 将 __SubscriptionID__ 和 __AccessToken__ 替换为前面使用的值。 
* 将 __ResourceGroupName__ 替换在上一部分中创建的资源组名称。
* 将 __DeploymentName__ 替换为要用于此部署的名称。

```
curl -X "PUT" "https://management.chinacloudapi.cn/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [AZURE.NOTE] 如果已将包含模板和参数的 JSON 文档保存到某个文件，可以使用以下命令而不是 `-d "{ template and parameters}"`：
>
> `--data-binary "@/path/to/file.json"`

如果此请求成功，你将收到 200 系列响应，且响应正文包含一个 JSON 文档，其中包含有关部署操作的信息。

> [AZURE.IMPORTANT] 请注意，部署已提交但目前尚未完成。部署通常需要大约 15 分钟才能完成。

##检查部署状态

若要检查部署状态，请使用以下命令：

* 将 __SubscriptionID__ 和 __AccessToken__ 替换为前面使用的值。 
* 将 __ResourceGroupName__ 替换在上一部分中创建的资源组名称。

```
curl -X "GET" "https://management.chinacloudapi.cn/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

这将返回包含有关部署操作的信息的 JSON 文档。`"provisioningState"` 元素包含部署状态；如果此元素包含 `"Succeeded"` 值，则表示部署已成功完成。现在，你的群集应可供使用。

##后续步骤

成功创建 HDInsight 群集后，请参考以下主题来了解如何使用群集。

###Hadoop 群集

* [将 Hive 与 HDInsight 配合使用](/documentation/articles/hdinsight-use-hive)
* [将 Pig 与 HDInsight 配合使用](/documentation/articles/hdinsight-use-pig)
* [将 MapReduce 与 HDInsight 配合使用](/documentation/articles/hdinsight-use-mapreduce)

###HBase 群集

* [Get started with HBase on HDInsight（HBase on HDInsight 入门）](/documentation/articles/hdinsight-hbase-tutorial-get-started-v1)
* [Develop Java applications for HBase on HDInsight（为 HBase on HDInsight 开发 Java 应用程序）](/documentation/articles/hdinsight-hbase-build-java-maven-linux)

###Storm 群集

* [Develop Java topologies for Storm on HDInsight（为 Storm on HDInsight 开发 Java 拓扑）](/documentation/articles/hdinsight-storm-develop-java-topology)
* [Use Python components in Storm on HDInsight（在 Storm on HDInsight 中使用 Python 组件）](/documentation/articles/hdinsight-storm-develop-python-topology)
* [Deploy and monitor topologies with Storm on HDInsight（使用 Storm on HDInsight 部署和监视拓扑）](/documentation/articles/hdinsight-storm-deploy-monitor-topology)

<!---HONumber=Mooncake_0530_2016-->