---
title: Azure Active Directory でエンタープライズ アプリにユーザーまたはグループを割り当てる | Microsoft Docs
description: Azure Active Directory プレビューでエンタープライズ アプリケーションを選択し、このアプリケーションにユーザーまたはグループを割り当てる方法
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
ms.date: 06/06/2018
ms.author: barbkess
ms.reviewer: luleon
ms.openlocfilehash: f23c9976dacc1ca696772d6bf02b5d59e3e0b4d5
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368690"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory でエンタープライズ アプリにユーザーまたはグループを割り当てる
ユーザーまたはグループをエンタープライズ アプリに割り当てるには、適切なアクセス許可でエンタープライズ アプリを管理する必要があります。また、ディレクトリの全体管理者である必要があります。

> [!NOTE]
> この記事で説明する機能には、Azure Active Directory Premium P1 または Premium P2 のライセンスが必要です。 詳しくは、「[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory)」ページをご覧ください。

> [!NOTE]
> Microsoft のアプリケーション (Office 365 アプリなど) では、PowerShell を使用してエンタープライズ アプリにユーザーを割り当てます。


## <a name="how-do-i-assign-user-access-to-an-enterprise-app-in-the-azure-portal"></a>Azure Portal でユーザーにエンタープライズ アプリケーションへのアクセス許可を割り当てる方法
1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]** を選択し、テキスト ボックスに「Azure Active Directory」と入力して、**Enter** キーを押します。
3. **Azure Active Directory - *ディレクトリ名*** ブレード (管理対象のディレクトリの Azure AD ブレード) で、**エンタープライズ アプリケーション** を選択します。

    ![エンタープライズ アプリケーションを開く](./media/assign-user-or-group-access-portal/open-enterprise-apps.png)
4. **[エンタープライズ アプリケーション]** ブレードで、**[すべてのアプリケーション]** を選択します。 これにより、管理できるアプリが一覧で表示されます。
5. **[Enterprise applications - All applications (エンタープライズ アプリケーション - すべてのアプリケーション)]** ブレードで、アプリケーションを選択します。
6. ***[アプリケーション名]*** ブレード (タイトルが選択したアプリケーションの名前であるブレード) で、**[ユーザーとグループ]** を選択します。

    ![[すべてのアプリケーション] を選択する](./media/assign-user-or-group-access-portal/select-app-users.png)
7. ***[アプリケーション名*** **- User & Group Assignment (ユーザーとグループの割り当て)]** ブレードで、**[追加]** を選択します。
8. **[Add Assignment (割り当ての追加)]** ブレードで **[ユーザーとグループ]** を選択します。

    ![アプリケーションにユーザーまたはグループを割り当てる](./media/assign-user-or-group-access-portal/assign-users.png)
9. **[ユーザーとグループ]** ブレードで一覧から 1 つまたは複数のユーザーまたはグループを選択し、ブレードの下部にある **[選択]** をクリックします。
10. **[Add Assignment (割り当ての追加)]** ブレードで **[ロール]** を選択します。 次に、**[ロールを選択する]** ブレードで、選択したユーザーまたはグループに適用するロールを選択して、ブレードの下部にある **[OK]** をクリックします。
11. **[Add Assignment (割り当ての追加)]** ブレードで、下部にある **[割り当て]** をクリックします。 割り当て対象のユーザーまたはグループに、選択したエンタープライズ アプリケーションのロールにより定義されるアクセス許可が付与されます。

## <a name="how-do-i-assign-a-user-to-an-enterprise-app-using-powershell"></a>PowerShell を使用してエンタープライズ アプリケーションにユーザーを割り当てる方法

1. 管理者特権での Windows PowerShell コマンド プロンプトを開きます。

    >[!NOTE] 
    > Azure AD モジュールをインストールする必要があります (`Install-Module -Name AzureAD` コマンドを使用してください)。 NuGet モジュールまたは新しい Azure Active Directory V2 PowerShell モジュールをインストールするように求められたら「Y」と入力し、ENTER キーを押します。

2. `Connect-AzureAD` を実行してグローバル管理者ユーザー アカウントでサインインします。
3. 次のスクリプトを使用して、アプリケーションにユーザーとロールを割り当てます。

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"
    
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```     

アプリケーション ロールにユーザーを割り当てる方法の詳細については、「[AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)」のドキュメントをご覧ください。

グループをエンタープライズ アプリに割り当てるには、`Get-AzureADUser` を `Get-AzureADGroup` に置き換える必要があります。

### <a name="example"></a>例

この例では PowerShell を使用して、ユーザー Britta Simon を [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) アプリケーションに割り当てます。

1. PowerShell で、変数 $username、$app_name および $app_role_name に対応する値を割り当てます。 

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

2. この例では、Britta Simon に割り当てるアプリケーション ロールの正確な名前はわかりません。 次のコマンドを実行し、ユーザーの UPN とサービス プリンシパル表示名を使用してユーザー ($user) と サービス プリンシパル ($sp) を取得します。

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
3. `$sp.AppRoles` コマンドを実行して、Workplace Analytics アプリケーションで使用できるロールを表示します。 この例では、Britta Simon にアナリスト (制限付きアクセス) のロールを割り当てます。
    
    ![Workplace Analytics ロール](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

4. `$app_role_name` 変数にロール名を割り当てます。
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

5. 次のコマンドを実行して、アプリのロールにユーザーを割り当てます。

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>次の手順
* [自分のグループをすべて表示する](../fundamentals/active-directory-groups-view-azure-portal.md)
* [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](remove-user-or-group-access-portal.md)
* [エンタープライズ アプリケーションのユーザー サインインを無効にする](disable-user-sign-in-portal.md)
* [エンタープライズ アプリケーションの名前またはロゴを変更する](change-name-or-logo-portal.md)
