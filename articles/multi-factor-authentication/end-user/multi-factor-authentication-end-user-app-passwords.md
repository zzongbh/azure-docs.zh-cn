---
title: "Azure MFA 中的应用密码是什么？ | Microsoft 文档"
description: "此页面将帮助用户了解什么是应用密码，以及在 Azure MFA 中，应用密码有什么作用。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4ff028f88e984f28bc0f4a228aabed1fabc90560
ms.openlocfilehash: c05377a2fd837a8b477e7bce3db7b4df61846f8e


---
# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 中的应用密码是什么？
某些非浏览器应用（例如使用 Exchange Active Sync 的 Apple 本机电子邮件客户端）目前不支持 Multi-Factor Authentication。 Multi-Factor Authentication 是按用户启用的。 这意味着，如果为某个用户启用了 Multi-Factor Authentication，而该用户尝试使用非浏览器应用将会失败。 使用应用密码可以避免这种情况。

> [!NOTE]
> 适用于 Office 2013 客户端的现代身份验证
>
> Office 2013 客户端（包括 Outlook）现在支持新的身份验证协议，并且可以启用对 Multi-Factor Authentication 的支持。  这意味着一旦启用后，就不需要对 Office 2013 客户端使用应用密码。  有关详细信息，请参阅 [Office 2013 现代身份验证公共预览版发布声明](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。
>
>

## <a name="how-to-use-app-passwords"></a>如何使用应用密码
以下是有关如何使用应用密码的一些要点。

* 实际的密码将自动生成，并非由用户提供。 这是因为自动生成的密码会使攻击者更难进行猜测，因而更安全。
* 目前，每个用户有 40 个密码的限制。 如果在达到该限制后尝试创建密码，系统会提示你删除一个现有的应用密码，以便创建新密码。
* 建议按设备而不是按应用程序创建应用密码。 例如，可以为笔记本电脑创建一个应用密码，并将该应用密码用于该笔记本电脑上的所有应用程序。
* 当你首次登录时，系统将为你提供一个应用密码。  如果需要更多的应用密码，你可以创建。

![设置](./media/multi-factor-authentication-end-user-app-passwords/app.png)

创建一个应用密码后，你可使用此密码来取代这些非浏览器应用中的原始密码。  例如，如果你在手机上使用 Multi-Factor Authentication 和 Apple 本机电子邮件客户端。  你可使用应用密码，以便绕过 Multi-Factor Authentication 并继续工作。

## <a name="creating-and-deleting-app-passwords"></a>创建和删除应用密码
在你首次登录期间，系统将为你提供一个可用的应用密码。  另外，以后你还可以创建和删除应用密码。  具体的操作方式取决于你使用 Multi-Factor Authentication 的方式。  请选择最适用你自己的方式。

| 如何使用 Multi-Factor Authentication | 说明 |
|:--- |:--- |
| [我要将它用于 Office 365](#creating-and-deleting-app-passwords-with-office-365) |这意味着你要通过 Office 365 门户创建应用密码。 |
| [我不知道](#creating-and-deleting-app-passwords-with-myapps-portal) |这意味着要通过 [https://myapps.microsoft.com](https://myapps.microsoft.com) 创建应用密码 |
| [我要将它用于 Microsoft Azure](#create-app-passwords-in-the-azure-portal) |这意味着你要通过 Azure 门户创建应用密码。 |

## <a name="creating-and-deleting-app-passwords-with-office-365"></a>使用 Office 365 创建和删除应用密码
如果你在 Office 365 上使用多重身份验证，则需要通过 Office 365 门户创建和删除应用密码。

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>在 Office 365 门户中创建应用密码
- - -
1. 登录 [Office 365 门户](https://login.microsoftonline.com/)。
2. 在右上角选择小组件并选择“Office 365 设置”。
3. 单击“其他安全性验证”。
4. 在右侧，单击**“更新用于帐户安全性的电话号码”链接。**
   ![设置](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. 随后你将转到可以更改设置的页面。
   ![云](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. 在顶部的“其他安全性验证”旁边，单击“应用密码”。
7. 单击“创建” 。
   ![云](./media/multi-factor-authentication-end-user-app-passwords/apppass.png)
8. 输入应用密码的名称，然后单击“下一步”。
   ![创建应用密码](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
9. 将应用密码复制到剪贴板，然后将它粘贴到你的应用。
   ![创建应用密码](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>使用 Office 365 门户删除应用密码
- - -
1. 登录 [Office 365 门户](https://login.microsoftonline.com/)。
2. 在右上角选择小组件并选择“Office 365 设置”。
3. 单击“其他安全性验证”。
4. 在右侧，单击**“更新用于帐户安全性的电话号码”链接。**
   ![设置](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. 随后你将转到可以更改设置的页面。
   ![云](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. 在顶部的“其他安全性验证”旁边，单击“应用密码”。
7. 在要删除的应用密码旁边，单击“删除”。
   ![删除应用密码](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
8. 单击“是”确认删除。
   ![确认删除](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
9. 删除应用密码后，可以单击“关闭”。
   ![关闭](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)

## <a name="creating-and-deleting-app-passwords-with-myapps-portal"></a>使用 Myapps 门户创建和删除应用密码。
如果你不确定多重身份验证的使用方式，你始终可以通过 myapps 门户创建和删除应用密码。

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>使用 Myapps 门户创建应用密码
1. 登录 [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. 在顶部选择配置文件。
3. 选择“其他安全性验证”。
   ![云](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. 随后你将转到可以更改设置的页面。
   ![设置](./media/multi-factor-authentication-end-user-manage/proofup.png)
5. 在顶部的“其他安全性验证”旁边，单击“应用密码”。
6. 单击“创建” 。
   ![创建应用密码](./media/multi-factor-authentication-end-user-app-passwords/create3.png)
7. 输入应用密码的名称，然后单击“下一步”。
   ![创建应用密码](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
8. 将应用密码复制到剪贴板，然后将它粘贴到你的应用。
   ![创建应用密码](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>使用 Myapps 门户删除应用密码
1. 登录 [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. 在顶部选择配置文件。
3. 选择“其他安全性验证”。
   ![云](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. 随后你将转到可以更改设置的页面。
   ![设置](./media/multi-factor-authentication-end-user-manage/proofup.png)
5. 在顶部的“其他安全性验证”旁边，单击“应用密码”。
6. 在要删除的应用密码旁边，单击“删除”。
   ![删除应用密码](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
7. 单击“是”确认删除。
   ![确认删除](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
8. 删除应用密码后，可以单击“关闭”。
   ![关闭](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)

## <a name="create-app-passwords-in-the-azure-portal"></a>在 Azure 门户中创建应用密码
如果你在 Azure 上使用 Multi-Factor Authentication，则需要通过 Azure 门户创建应用密码。

### <a name="to-create-app-passwords-in-the-azure-portal"></a>在 Azure 门户中创建应用密码
1. 登录到 Azure 管理门户。
2. 在顶部，右键单击你的用户名并选择“其他安全性验证”。
3. 在验证页的顶部选择应用密码
4. 单击“创建” 。
5. 输入应用密码的名称，然后单击“下一步”
6. 将应用密码复制到剪贴板，然后将它粘贴到你的应用。
   ![云](./media/multi-factor-authentication-end-user-app-passwords/app2.png)

### <a name="to-delete-app-passwords-in-the-azure-portal"></a>在 Azure 门户中删除应用密码
1. 登录到 Azure 管理门户。
2. 在顶部，右键单击你的用户名并选择“其他安全性验证”。
3. 在顶部的“其他安全性验证”旁边，单击“应用密码”。
4. 在要删除的应用密码旁边，单击“删除”。
5. 单击“是”确认删除。
6. 删除应用密码后，可以单击“关闭”。
   ![关闭](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)



<!--HONumber=Dec16_HO4-->

