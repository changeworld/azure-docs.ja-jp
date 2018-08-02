---
title: Azure Active Directory でエンタープライズ アプリからユーザーまたはグループの割り当てを削除する | Microsoft Docs
description: Azure Active Directory でエンタープライズ アプリケーションからユーザーまたはグループのアクセスの割り当てを削除する方法
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: fde6d5fa2488d86af542f409df7c5b76d2510f08
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368650"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory でエンタープライズ アプリからユーザーまたはグループの割り当てを削除する
Azure Active Directory (Azure AD) で、エンタープライズ アプリのいずれかに割り当てられているアクセスからユーザーまたはグループを簡単に削除できます。 エンタープライズ アプリを管理するには、適切なアクセス許可が必要です。また、ディレクトリの全体管理者である必要があります。

> [!NOTE]
> Microsoft のアプリケーション (Office 365 アプリなど) では、PowerShell を使用してエンタープライズ アプリのユーザーを削除します。

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Azure Portal でエンタープライズ アプリに対するユーザーまたはグループの割り当てを削除する方法
1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[その他のサービス]** を選択し、テキスト ボックスに「**Azure Active Directory**」と入力して、**Enter** キーを押します。
3. **[Azure Active Directory - *directoryname*]** ページ (管理対象のディレクトリの Azure AD ページ) で、**[エンタープライズ アプリケーション]** を選択します。

    ![エンタープライズ アプリケーションを開く](./media/remove-user-or-group-access-portal/open-enterprise-apps.png)
4. **[エンタープライズ アプリケーション]** ページで、**[すべてのアプリケーション]** を選択します。 管理できるアプリケーションの一覧が表示されます。
5. **[エンタープライズ アプリケーション - すべてのアプリケーション]** ページで、アプリを選択します。
6. [***appname***] ページ (選択したアプリの名前がタイトルに含まれるページ) で、**[ユーザーとグループ]** を選択します。

    ![ユーザーまたはグループを選択する](./media/remove-user-or-group-access-portal/remove-app-users.png)
7. **[***appname*** - ユーザーとグループの割り当て]** ページで、1 人以上のユーザーまたは 1 つ以上のグループを選択し、**[削除]** をクリックします。 確認メッセージが表示されたら、削除を確定します。

    ![[削除] をクリックする](./media/remove-user-or-group-access-portal/remove-users.png)

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>PowerShell を使用してエンタープライズ アプリに対するユーザーまたはグループの割り当てを削除する方法
1. 管理者特権での Windows PowerShell コマンド プロンプトを開きます。

    >[!NOTE] 
    > Azure AD モジュールをインストールする必要があります (`Install-Module -Name AzureAD` コマンドを使用してください)。 NuGet モジュールまたは新しい Azure Active Directory V2 PowerShell モジュールをインストールするように求められたら「Y」と入力し、ENTER キーを押します。

2. `Connect-AzureAD` を実行してグローバル管理者ユーザー アカウントでサインインします。
3. 次のスクリプトを使用して、アプリケーションにユーザーとロールを割り当てます。

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ``` 
## <a name="next-steps"></a>次の手順

- [自分のグループをすべて表示する](../fundamentals/active-directory-groups-view-azure-portal.md)
- [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](assign-user-or-group-access-portal.md)
- [エンタープライズ アプリケーションのユーザー サインインを無効にする](disable-user-sign-in-portal.md)
- [エンタープライズ アプリケーションの名前またはロゴを変更する](change-name-or-logo-portal.md)
