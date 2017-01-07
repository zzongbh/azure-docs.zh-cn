---
title: "通过 PowerShell 创建具有 Azure Data Lake Store 的 HDInsight 群集 | Microsoft Docs"
description: "使用 Azure PowerShell 创建和使用包含 Azure Data Lake 的 HDInsight 群集"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 164ada5a-222e-4be2-bd32-e51dbe993bc0
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/18/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 3f8c9b22fb9a7aae97c43e39fe82a610f6b8b374
ms.openlocfilehash: ff693920244316eec9ef25b00e6296e8e68f3d5e


---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-powershell"></a>使用 Azure PowerShell 创建包含 Data Lake Store的 HDInsight 群集
> [!div class="op_single_selector"]
> * [使用门户](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [使用 PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [使用资源管理器](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

了解如何通过 Azure PowerShell 配置具有 Azure Data Lake Store 访问权限的 HDInsight 群集。 对于支持的群集类型，Data Lake Store 用作默认存储或其他存储帐户。 在 Data Lake Store 用作其他存储时，该群集的默认存储帐户仍将是 Azure 存储 Blob (WASB)，与群集相关的文件（例如日志等）仍会写入到默认存储，而要处理的数据可以存储在 Data Lake Store 帐户中。 使用 Data Lake Store 作为其他存储帐户不会影响读/写到此群集的存储的性能或能力。

一些重要注意事项：

* HDInsight 版本 3.5 提供创建 HDInsight 群集（可访问作为默认存储的 Data Lake Store）的选项。

* HDInsight 版本 3.2、3.4 和 3.5 提供创建 HDInsight 群集（可访问作为其他存储的 Data Lake Store）的选项。

* 对于 HBase 群集（Windows 和 Linux），Data Lake Store **不支持**用作存储选项（包括默认存储和其他存储）。


本文中将设置 Hadoop 群集，其中 Data Lake Store 作为其他存储。 有关如何将 Data Lake Store 用作默认存储来创建 Hadoop 群集的说明，请参阅[使用 Azure 门户创建具有 Data Lake Store 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)。

使用 PowerShell 配置 HDInsight 来与 Data Lake Store 一起使用涉及以下步骤：

* 创建 Azure Data Lake Store
* 对 Data Lake Store 设置基于角色访问的身份验证
* 创建具有 Data Lake Store 身份验证的 HDInsight 群集
* 在此群集上运行作业

## <a name="prerequisites"></a>先决条件
在开始阅读本教程前，你必须具有：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure PowerShell 1.0 或更高版本**。 请参阅 [如何安装和配置 Azure PowerShell](../powershell-install-configure.md)。
* **Windows SDK**。 可从[此处](https://dev.windows.com/en-us/downloads)进行安装。 可使用它来创建安全证书。
* **Azure Active Directory 服务主体**。 本教程中的步骤用于指导如何在 Azure AD 中创建服务主体。 但是，只有 Azure AD 管理员才能创建服务主体。 Azure AD 管理员可以跳过此先决条件，继续阅读本教程。

    **如果不是 Azure AD 管理员**，将无法执行创建服务主体所需的步骤。 在这种情况下，Azure AD 管理员必须先创建服务主体，然后才能创建包含 Data Lake Store 的 HDInsight 群集。 此外，必须使用证书创建服务主体，如[使用证书创建服务主体](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)中所述。

## <a name="create-an-azure-data-lake-store"></a>创建 Azure Data Lake Store
按照这些步骤创建 Data Lake Store。

1. 在桌面上，打开一个新的 Azure PowerShell 窗口，并输入下面的代码段。 当系统提示输入登录信息时，请确保以订阅管理员/所有者身份登录：

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > 注册 Data Lake Store 资源提供程序时如果收到类似于 `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` 的错误，则可能是因为未将订阅列为 Azure Data Lake Store 的白名单。 请按照以下[说明](data-lake-store-get-started-portal.md)，确保对 Data Lake Store 公共预览启用 Azure 订阅。
   >
   >
2. Azure Data Lake Store 帐户与 Azure 资源组是相关联的。 首先创建 Azure 资源组。

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![创建 Azure 资源组](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Create an Azure Resource Group")
3. 创建 Azure Data Lake Store 帐户。 指定的帐户名称必须仅包含小写字母和数字。

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![创建 Azure Data Lake 帐户](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Create an Azure Data Lake account")
4. 验证是否已成功创建帐户。

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    此输出应为 **True**。
5. 上传示例数据到 Azure Data Lake。 本文后面将使用这些数据来验证是否可从 HDInsight 群集访问数据。 如果正在查找一些示例数据进行上载，可以从 **Azure Data Lake Git 存储库** 获取 [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)文件夹。

        $myrootdir = "/"
        Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>对 Data Lake Store 设置基于角色访问的身份验证
每个 Azure 订阅都会与 Azure Active Directory 关联。 使用 Azure 经典门户或 Azure Resource Manager API 访问订阅资源的用户和服务必须首先进行 Azure Active Directory 身份验证。 通过在 Azure 资源上为这些用户和服务分配相应角色，向其授予访问权限。  对于服务，服务主体会识别 Azure Active Directory (AAD) 中的服务。 本部分介绍如何通过创建应用程序服务主体和通过 Azure PowerShell 向应用程序服务主体分配角色，向 HDInsight 等应用程序服务授予 Azure 资源（先前创建的 Azure Data Lake Store 帐户）访问权限。

若要设置 Azure Data Lake 的 Active Directory 身份验证，必须执行以下任务。

* 创建自签名证书
* 在 Azure Active Directory 中创建应用程序和服务主体

### <a name="create-a-self-signed-certificate"></a>创建自签名证书
继续进行本部分中的步骤前，请确保已安装有 [Windows SDK](https://dev.windows.com/en-us/downloads)。 还必须创建一个目录（该证书将在其中创建），例如 **C:\mycertdir**。

1. 在 PowerShell 窗口中，导航到安装 Windows SDK 的位置（通常为 `C:\Program Files (x86)\Windows Kits\10\bin\x86`），并使用 [MakeCert][makecert] 实用工具创建一个自签名证书和私钥。 使用以下命令。

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir
        $startDate = (Get-Date).ToString('MM/dd/yyyy')
        $endDate = (Get-Date).AddDays(365).ToString('MM/dd/yyyy')

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -b $startDate -e $endDate -r -len 2048

    系统会提示输入私钥密码。 此命令成功执行后，指定的证书目录中应会出现 **CertFile.cer** 和 **mykey.pvk**。
2. 使用 [Pvk2Pfx][pvk2pfx] 实用工具将 MakeCert 创建的.pvk 和.cer 文件转换为.pfx 文件。 运行以下命令。

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    出现提示时，输入先前指定的私钥密码。 为 **-po** 参数指定的值即是与此 .pfx 文件关联的密码。 此命令成功完成后，指定的证书目录中也应会出现 CertFile.pfx。

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>创建 Azure Active Directory 和服务主体
此部分中将执行以下用于创建 Azure Active Directory 应用程序的服务主体、将角色分配给服务主体，通过提供证书作为服务主体进行身份验证的步骤。 运行以下命令在 Azure Active Directory 中创建应用程序。

1. 将以下 cmdlet 粘贴到 PowerShell 控制台窗口中。 请确保为 **-DisplayName** 属性指定的值唯一。 此外，**-HomePage** 和 **-IdentiferUris** 的值是占位符值，且未进行验证。

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
                    -DisplayName "HDIADL" `
                    -HomePage "https://contoso.com" `
                    -IdentifierUris "https://mycontoso.com" `
                    -KeyValue $credential  `
                    -KeyType "AsymmetricX509Cert"  `
                    -KeyUsage "Verify"  `
                    -StartDate $startDate  `
                    -EndDate $endDate

        $applicationId = $application.ApplicationId
2. 使用应用程序 ID 创建服务主体。

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. 向将从 HDInsight 群集访问的 Data Lake Store 文件/文件夹授予服务主体访问权限。 下面的代码段提供 Data Lake Store 帐户的根的访问权限。

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All

    在提示处，输入 **Y** 进行确认。

## <a name="create-an-hdinsight-cluster-with-authentication-to-data-lake-store"></a>创建具有 Data Lake Store 身份验证的 HDInsight 群集
本部分中创建 HDInsight Hadoop 群集。 对于此版本，HDInsight 群集和 Data Lake Store 必须位于同一位置（美国东部 2）。

1. 首先检索订阅租户 ID。 之后需要此 ID。

        $tenantID = (Get-AzureRmContext).Tenant.TenantId
2. 此版本中，对于 Hadoop 群集，Data Lake Store 仅可用作此群集的其他存储。 默认存储仍是 Azure storage blobs (WASB)。 因此，首先来创建此群集所需的存储帐户和存储容器。

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $containerName -Context $destContext
3. 创建 HDInsight 群集。 使用以下 cmdlet。

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $rdpCredentials = Get-Credential

        New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.2" -RdpCredential $rdpCredentials -RdpAccessExpiry (Get-Date).AddDays(14) -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    此 cmdlet 成功完成后，应出现如下的输出：

        Name                      : hdiadlcluster
        Id                        : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/hdiadlgroup/providers/Mi
                                    crosoft.HDInsight/clusters/hdiadlcluster
        Location                  : East US 2
        ClusterVersion            : 3.2.7.707
        OperatingSystemType       : Windows
        ClusterState              : Running
        ClusterType               : Hadoop
        CoresUsed                 : 16
        HttpEndpoint              : hdiadlcluster.azurehdinsight.net
        Error                     :
        DefaultStorageAccount     :
        DefaultStorageContainer   :
        ResourceGroup             : hdiadlgroup
        AdditionalStorageAccounts :

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>在 HDInsight 群集上运行测试作业以使用 Data Lake Store
配置 HDInsight 群集后，可在该群集上运行测试作业来测试该 HDInsight 群集是否可访问 Data Lake Store。 为此，我们会运行示例 Hive 作业，该作业会使用先前已上传至 Data Lake Store 的示例数据创建一个表。

### <a name="for-a-linux-cluster"></a>对于 Linux 群集
本节中，将连接 SSH 到群集中，然后运行示例 Hive 查询。 Windows 未提供内置的 SSH 客户端。 建议使用可从 [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 下载的 **PuTTY**。

有关使用 PuTTY 的详细信息，请参阅[在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)。

1. 连接后，请使用以下命令启动 Hive 命令行界面 (CLI)。

        hive
2. 使用此 CLI，输入以下语句，通过使用 Data Lake Store 中的示例数据创建一个名为“车辆”的新表：

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    你应该会看到与下面类似的输出：

        1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
        1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
        1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
        1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
        1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
        1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
        1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
        1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
        1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
        1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1

### <a name="for-a-windows-cluster"></a>对于 Windows 群集
使用以下 cmdlet 运行 Hive 查询。 在此查询中，以 Data Lake Store 中的数据创建一个表，然后在创建的表上运行选择查询。

    $queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"

    $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

    $hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

    Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials

应会显示以下输出。 输出中的 **ExitValue** 0 表明此作业已成功完成。

    Cluster         : hdiadlcluster.
    HttpEndpoint    : hdiadlcluster.azurehdinsight.net
    State           : SUCCEEDED
    JobId           : job_1445386885331_0012
    ParentId        :
    PercentComplete :
    ExitValue       : 0
    User            : admin
    Callback        :
    Completed       : done

使用下面的 cmdlet 从作业检索输出：

    Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials

该作业输出如下：

    1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
    1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
    1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
    1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
    1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
    1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
    1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
    1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
    1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
    1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


## <a name="access-data-lake-store-using-hdfs-commands"></a>使用 HDFS 命令访问 Data Lake Store
配置 HDInsight 群集使用 Data Lake Store 后，可使用 HDFS shell 命令访问此存储。

### <a name="for-a-linux-cluster"></a>对于 Linux 群集
本节中，将连接 SSH 到群集中，然后运行 HDFS 命令。 Windows 未提供内置的 SSH 客户端。 建议使用可从 [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 下载的 **PuTTY**。

有关使用 PuTTY 的详细信息，请参阅[在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)。

连接后，使用以下 HDFS 文件系统命令列出 Data Lake Store 中的文件。

    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

这会列出先前上传到 Data Lake Store 中的文件。

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

可使用 `hdfs dfs -put` 命令上传部分文件到 Data Lake Store 中，然后使用 `hdfs dfs -ls` 验证是否已成功上传这些文件。

### <a name="for-a-windows-cluster"></a>对于 Windows 群集
1. 登录到新的 [Azure 门户](https://portal.azure.com)。
2. 单击“浏览”，单击“HDInsight 群集”，然后单击已创建的 HDInsight 群集。
3. 在群集边栏选项卡中，单击“远程桌面”，然后在“远程桌面”边栏选项卡中，单击“连接”。

    ![远程登录到 HDI 群集](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png "Create an Azure Resource Group")

    出现提示时，输入你提供给远程桌面用户的凭据。
4. 在远程会话中，启动 Windows PowerShell，并使用 HDFS 文件系统命令列出 Azure Data Lake Store 中的文件。

         hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

    这会列出先前上传到 Data Lake Store 中的文件。

        15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
        Found 1 items
        -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv

    可使用 `hdfs dfs -put` 命令上传部分文件到 Data Lake Store 中，然后使用 `hdfs dfs -ls` 验证是否已成功上传这些文件。

## <a name="see-also"></a>另请参阅
* [门户：创建 HDInsight 群集以使用 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx



<!--HONumber=Nov16_HO4-->

