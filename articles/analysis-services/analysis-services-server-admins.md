---
title: "Azure Analysis Services でのサーバー管理者の管理 | Microsoft Docs"
description: "Azure で Analysis Services サーバーのサーバー管理者を管理する方法について説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: d90f1e3df8f5934d5c334ec72b5726f105842ca1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="manage-server-administrators"></a>サーバー管理者の管理
サーバー管理者は、サーバーが存在するテナントについて、Azure Active Directory (Azure AD) 内で有効なユーザーまたはグループである必要があります。 Azure Portal でサーバーの **[Analysis Services 管理者]** を使用するか、SSMS のサーバー プロパティを使用して、サーバー管理者を管理できます。 

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
[ロールベースのアクセス制御](../active-directory/role-based-access-control-what-is.md)  

