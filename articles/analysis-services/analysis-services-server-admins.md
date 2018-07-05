---
title: Azure Analysis Services でのサーバー管理者の管理 | Microsoft Docs
description: Azure で Analysis Services サーバーのサーバー管理者を管理する方法について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ec1630f4de70f77c13e335c68aff16180e524c12
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307810"
---
# <a name="manage-server-administrators"></a>サーバー管理者の管理
サーバー管理者は、サーバーが存在するテナントについて、Azure Active Directory (Azure AD) 内で有効なユーザーまたはセキュリティ グループである必要があります。 Azure Portal でサーバーの **[Analysis Services 管理者]** を使用するか、SSMS のサーバー プロパティを使用して、サーバー管理者を管理できます。 

> [!NOTE]
> セキュリティ グループでは、`MailEnabled` プロパティが `True` に設定されている必要があります。

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Azure Portal を使用してサーバー管理者を追加するには
1. ポータルで、サーバーの、**[Analysis Services 管理者]** をクリックします。
2. **[\<サーバー名> - Analysis Services 管理者]** で、**[追加]** をクリックします。
3. **[サーバー管理者を追加します]** で、Azure AD からユーザー アカウントを選択するか、メール アドレスで外部のユーザーを招待します。

    ![Azure Portal のサーバー管理者](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>SSMS を使用してサーバー管理者を追加するには
1. サーバーを右クリックして、**[プロパティ]** をクリックします。
2. **[分析サーバーのプロパティ]** で **[セキュリティ]** をクリックします。
3. **[追加]** をクリックし、Azure AD のユーザーまたはグループのメール アドレスを入力します。
   
    ![SSMS でのサーバー管理者の追加](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>次の手順 
[認証とユーザーのアクセス許可](analysis-services-manage-users.md)  
[データベース ロールとユーザーの管理](analysis-services-database-users.md)  
[ロールベースのアクセス制御](../role-based-access-control/overview.md)  

