---
title: Azure Analysis Services でのサーバー管理者の管理 | Microsoft Docs
description: この記事では、Azure portal、PowerShell、または REST API を使用し、Azure Analysis Services サーバーのサーバー管理者を管理する方法について説明します。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f7c57a5751f2ff34abb26b7653070ce4ee5010fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572625"
---
# <a name="manage-server-administrators"></a>サーバー管理者の管理

サーバー管理者は、サーバーが存在するテナントについて、Azure Active Directory (Azure AD) 内で有効なユーザーまたはセキュリティ グループである必要があります。 Azure portal でご利用のサーバーの **[Analysis Services 管理者]** を使用するか、SSMS、PowerShell、または REST API のサーバー プロパティを使用して、サーバー管理者を管理できます。 

**セキュリティ グループ**は、[ プロパティが ](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups) に設定された`MailEnabled`メール対応`True`である必要があります。 メール アドレスでグループを指定する場合は、`obj:groupid@tenantid` を使用します。

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Azure Portal を使用してサーバー管理者を追加するには

1. ポータルで、サーバーの **[Analysis Services 管理者]** をクリックします。
2. **[\<サーバー名> - Analysis Services 管理者]** で、 **[追加]** をクリックします。
3. **[サーバー管理者を追加します]** で、Azure AD からユーザー アカウントを選択するか、メール アドレスで外部のユーザーを招待します。

    ![Azure Portal のサーバー管理者](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>SSMS を使用してサーバー管理者を追加するには

1. サーバーを右クリックして、 **[プロパティ]** をクリックします。
2. **[分析サーバーのプロパティ]** で **[セキュリティ]** をクリックします。
3. **[追加]** をクリックし、Azure AD のユーザーまたはグループのメール アドレスを入力します。
   
    ![SSMS でのサーバー管理者の追加](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

新しいサーバーの作成時に Administrator パラメーターを指定するには、[New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) コマンドレットを使用します。 <br>
既存のサーバーに対する Administrator パラメーターを変更するには、[Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) コマンドレットを使用します。

## <a name="rest-api"></a>REST API

新しいサーバーの作成時に asAdministrator プロパティを指定して、[Create](https://docs.microsoft.com/rest/api/analysisservices/servers/create) を使用します。 <br>
既存のサーバーの変更時に asAdministrator プロパティを指定して、[Update](https://docs.microsoft.com/rest/api/analysisservices/servers/update) を使用します。 <br>



## <a name="next-steps"></a>次のステップ 

[認証とユーザーのアクセス許可](analysis-services-manage-users.md)  
[データベース ロールとユーザーの管理](analysis-services-database-users.md)  
[ロールベースのアクセス制御](../role-based-access-control/overview.md)  

