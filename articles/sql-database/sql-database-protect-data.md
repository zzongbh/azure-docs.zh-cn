---
title: "Azure SQL 数据库数据保护概述 | Microsoft 文档"
description: "了解如何保护 Azure SQL 数据库中的数据。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 12/21/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: f4712d70c0323e607ddcc021809f8097a621730d
ms.openlocfilehash: 67b32b224d86e8d0ed06d8248c3a2c5f5569c5cb


---
# <a name="protecting-data-within-your-sql-database"></a>保护 SQL 数据库中的数据

SQL 数据库使用加密来保护数据。  

## <a name="overview"></a>概述

SQL 数据库可以保护数据。对于动态数据，它使用[传输层安全性](https://support.microsoft.com/en-us/kb/3135244)提供加密；对于静态数据，使用[透明数据加密](http://go.microsoft.com/fwlink/?LinkId=526242)提供加密；对于使用中的数据，将使用 [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) 提供加密。 有关 SQL 数据库中这些数据保护功能的用法介绍，请参阅[数据保护和安全性](sql-database-protect-data.md)。

若要通过其他方法加密数据，请考虑：

* 使用[单元格级加密](https://msdn.microsoft.com/library/ms179331.aspx)，借助不同的加密密钥来加密特定的数据列甚至数据单元格。
* 如果需要硬件安全模块或需要加密密钥层次结构的集中管理，请考虑[对 Azure VM 中的 SQL Server 使用 Azure 密钥保管库](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)。


SQL 数据库通过提供审核和威胁检测功能来保护数据。 

### <a name="auditing"></a>审核
Azure SQL 数据库审核可跟踪数据库活动，通过将数据库事件记录到 Azure 存储帐户中的审核日志，帮助用户保持合规性。 使用审核可以了解正在进行的数据库活动，以及分析和调查历史活动，标识潜在威胁或可疑的滥用行为与安全违规。 有关更多信息，请参阅 [SQL 数据库审核入门](sql-database-auditing-get-started.md)。  

### <a name="threat-detection"></a>威胁检测
威胁检测是审核的补充，它在 Azure SQL 数据库服务中提供一个内置的附加安全智能层。 它会全天候探查、分析和检测异常数据库活动。 出现可疑活动、潜在漏洞、 SQL 注入攻击和异常数据库访问模式时，它会发出警报。 用户可以遵照提供的参考说明与可行的说明对警报做出响应。 有关详细信息，请参阅 [SQL 数据库威胁检测入门](sql-database-threat-detection-get-started.md)。  

## <a name="connection-security"></a>连接安全性
连接安全性是指如何使用防火墙规则和连接加密来限制和保护数据库连接。

服务器和数据库使用防火墙规则来拒绝源自未明确列入允许列表的 IP 地址的连接企图。 若要允许应用程序或客户端计算机的公共 IP 地址尝试连接到新数据库，必须先使用 Azure 经典门户、REST API 或 PowerShell 创建服务器级防火墙规则。 作为最佳实践，应该尽量通过服务器防火墙来限制允许的 IP 地址范围。 有关详细信息，请参阅 [Azure SQL 数据库防火墙](https://msdn.microsoft.com/library/ee621782)。

在与数据库相互“传输”数据时，与 Azure SQL 数据库建立的所有连接都需要经过加密 (SSL/TLS)。 必须在应用程序连接字符串中指定用于加密连接的参数，而*不要*信任服务器证书（服务器证书用于将连接字符串复制到 Azure 经典门户外部），否则，连接无法验证服务器的身份，并且容易受到“中间人”攻击。 例如，对于 ADO.NET 驱动程序，这些连接字符串参数为 **Encrypt=True** 和 **TrustServerCertificate=False**。 有关详细信息，请参阅 [Azure SQL 数据库连接加密和证书验证](https://msdn.microsoft.com/library/azure/ff394108#encryption)。

## <a name="authentication"></a>身份验证
身份验证是指连接到数据库时如何证明你的身份。 SQL 数据库支持两种类型的身份验证：

* **SQL 身份验证**，使用用户名和密码
* **Azure Active Directory 身份验证**，使用 Azure Active Directory 管理的标识，并支持托管域和集成域

在为数据库创建逻辑服务器时，你已指定一个包含用户名和密码的“服务器管理员”登录名。 通过这些凭据，你可以使用数据库所有者（即“dbo”）的身份通过服务器上任何数据库的身份验证。 如果想要使用 Azure Active Directory 身份验证，则必须创建名为“Azure AD 管理员”的另一个服务器管理员，用于管理 Azure AD 用户和组。 此管理员还能执行普通服务器管理员可以执行的所有操作。 有关如何创建 Azure AD 管理员以启用 Azure Active Directory 身份验证的演练，请参阅[通过使用 Azure Active Directory 身份验证连接到 SQL 数据库](sql-database-aad-authentication.md)。

作为最佳实践，应用程序应使用不同的帐户进行身份验证 – 这样，就可以限制授予应用程序的权限，并在应用程序代码容易受到 SQL 注入攻击的情况下降低恶意活动的风险。 建议的方法是创建[包含的数据库用户](https://msdn.microsoft.com/library/ff929188)，使应用程序能够直接向数据库进行身份验证。 可以通过执行以下 T-SQL 命令，在以服务器管理员身份登录连接到用户数据库时，创建使用 SQL 身份验证的包含数据库用户：

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

如果创建了 Azure AD 管理员，可以通过执行以下 T-SQL 命令，在以 Azure AD 管理员身份登录连接到用户数据库时，创建使用 Azure Active Directory 身份验证的包含数据库用户：

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

在任一情况下，应用程序的连接字符串应该指定这些用户凭据而不是服务器管理员登录名以连接到数据库。

有关在 SQL 数据库上进行身份验证的详细信息，请参阅[在 Azure SQL 数据库中管理数据库和登录名](sql-database-manage-logins.md)。

## <a name="authorization"></a>授权
授权是指可以在 Azure SQL 数据库中执行哪些操作，这由你的用户帐户角色成员身份和权限来控制。 作为最佳实践，应向用户授予所需的最低权限。 Azure SQL 数据库允许在 T-SQL 中使用角色方便管理这种权限：

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

用于连接的服务器管理员帐户是 db_owner 所有者的成员，该帐户有权在数据库中执行任何操作。 请保存此帐户，以便部署架构升级并执行其他管理操作。 权限受到更多限制的“ApplicationUser”帐户可让用户使用应用程序所需的最低权限从应用程序连接到数据库。

有许多方式可以进一步限制用户通过 Azure SQL 数据库可以执行的操作：

* 除 db_datareader 和 db_datawriter 以外的[数据库角色](https://msdn.microsoft.com/library/ms189121)可用于创建权限较大的应用程序用户帐户或权限较小的管理帐户。
* 通过细化[权限](https://msdn.microsoft.com/library/ms191291)可控制能对数据库中单个列、表、视图、过程和其他对象执行的操作。
* [模拟](https://msdn.microsoft.com/library/vstudio/bb669087)和[模块签名](https://msdn.microsoft.com/library/bb669102)可用于安全地暂时提升权限。
* [行级安全性](https://msdn.microsoft.com/library/dn765131)可用于限制用户可访问的行。
* [数据屏蔽](sql-database-dynamic-data-masking-get-started.md)可用于限制敏感数据的公开。
* [存储过程](https://msdn.microsoft.com/library/ms190782)可用于限制可对数据库执行的操作。

从 Azure 经典门户或使用 Azure Resource Manager API 管理数据库和逻辑服务器的操作将会根据你的门户用户帐户的角色分配进行控制。 有关此主题的详细信息，请参阅 [Azure 门户中基于角色的访问控制](../active-directory/role-based-access-control-configure.md)。

## <a name="encryption"></a>加密
Azure SQL 数据库将会帮助你通过使用[透明数据加密](http://go.microsoft.com/fwlink/?LinkId=526242)来加密处于“静止”状态或存储在数据库文件的数据，从而保护数据。 若要加密数据库，请以数据库所有者身份连接，然后执行：

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

如需通过其他方法加密数据机密，请考虑：

* 使用[单元格级加密](https://msdn.microsoft.com/library/ms179331.aspx)，借助不同的加密密钥来加密特定的数据列甚至数据单元格。
* 如果需要硬件安全模块或需要加密密钥层次结构的集中管理，请考虑[对 Azure VM 中的 SQL Server 使用 Azure 密钥保管库](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)。
* [始终加密](https://msdn.microsoft.com/library/mt163865.aspx)（预览版）使得加密对于应用程序来说是透明的，从而让客户端能够加密客户端应用程序中的敏感数据，不必与 SQL 数据库共享加密密钥。

## <a name="auditing"></a>审核
审核和跟踪数据库事件可帮助你保持合规性，以及识别可疑活动。 SQL 数据库审核可让你将数据库中的事件记录到 Azure 存储帐户中的审核日志。 SQL 数据库审核还与 Microsoft Power BI 集成，以帮助向下钻取报告和分析数据。 有关详细信息，请参阅 [SQL 数据库审核入门](sql-database-auditing-get-started.md)。

SQL 数据库威胁检测除审核之外，还提供额外的安全层。 通过提供对异常活动的安全警报，它使你可以在威胁发生时就对其作出响应。 有关详细信息，请参阅 [SQL 数据库威胁检测入门](sql-database-threat-detection-get-started.md)。  

## <a name="compliance"></a>合规性
除了上述可帮助应用程序符合各项安全法规要求的特性和功能以外，Azure SQL 数据库还定期参与审核，并已通过许多法规标准的认证。 有关详细信息，请参阅 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/)，你可以从中找到 [SQL 数据库法规认证](https://azure.microsoft.com/support/trust-center/services/)的最新列表。

## <a name="next-steps"></a>后续步骤

- 有关所有 SQL 数据库安全功能的概述，请参阅 [SQL 安全概述](sql-database-security-overview.md)。
- 有关使用 SQL 数据库中的访问控制功能的介绍，请参阅[控制访问](sql-database-control-access.md)。
- 有关主动监视的介绍，请参阅 [SQL 数据库审核入门](sql-database-auditing-get-started.md)和 [SQL 数据库威胁检测入门](sql-database-threat-detection-get-started.md)。




<!--HONumber=Dec16_HO4-->

