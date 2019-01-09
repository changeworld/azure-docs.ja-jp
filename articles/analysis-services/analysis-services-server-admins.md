---
title: Azure Analysis Services でのサーバー管理者の管理 | Microsoft Docs
description: Azure で Analysis Services サーバーのサーバー管理者を管理する方法について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5afc434ccd7a41c6fa1f4fec300941458c84889e
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53629573"
---
# <a name="manage-server-administrators"></a>サーバー管理者の管理

サーバー管理者は、サーバーが存在するテナントについて、Azure Active Directory (Azure AD) 内で有効なユーザーまたはセキュリティ グループである必要があります。 Azure portal でご利用のサーバーの **[Analysis Services 管理者]** を使用するか、SSMS、PowerShell、または REST API のサーバー プロパティを使用して、サーバー管理者を管理できます。 

> [!NOTE]
> セキュリティ グループでは、`MailEnabled` プロパティが `True` に設定されている必要があります。

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Azure Portal を使用してサーバー管理者を追加するには

1. ポータルで、サーバーの **[Analysis Services 管理者]** をクリックします。
2. **[\<サーバー名> - Analysis Services 管理者]** で、**[追加]** をクリックします。
3. **[サーバー管理者を追加します]** で、Azure AD からユーザー アカウントを選択するか、メール アドレスで外部のユーザーを招待します。

    ![Azure Portal のサーバー管理者](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>SSMS を使用してサーバー管理者を追加するには

1. サーバーを右クリックして、**[プロパティ]** をクリックします。
2. **[分析サーバーのプロパティ]** で **[セキュリティ]** をクリックします。
3. **[追加]** をクリックし、Azure AD のユーザーまたはグループのメール アドレスを入力します。
   
    ![SSMS でのサーバー管理者の追加](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

新しいサーバーの作成時に Administrator パラメーターを指定して、[New-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) コマンドレットを使用します。 <br>
既存のサーバーに対する Administrator パラメーターを変更して、[Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver) コマンドレットを使用します。

## <a name="rest-api"></a>REST API

新しいサーバーの作成時に asAdministrator プロパティを指定して、[Create](https://docs.microsoft.com/rest/api/analysisservices/servers/create) を使用します。 <br>
既存のサーバーの変更時に asAdministrator プロパティを指定して、[Update](https://docs.microsoft.com/rest/api/analysisservices/servers/update) を使用します。 <br>



## <a name="next-steps"></a>次の手順 

[認証とユーザーのアクセス許可](analysis-services-manage-users.md)  
[データベース ロールとユーザーの管理](analysis-services-database-users.md)  
[ロールベースのアクセス制御](../role-based-access-control/overview.md)  

