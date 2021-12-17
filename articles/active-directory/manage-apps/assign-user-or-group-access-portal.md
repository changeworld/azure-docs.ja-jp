---
title: ユーザーとグループの割り当て
titleSuffix: Azure AD
description: ID 管理のために Azure Active Directory を使用して、アプリに対してユーザーとグループの割り当ておよび割り当て解除を行う方法について説明します。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/23/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: d06dc25abf6ae83fd975a0d8d7fe38d905290e92
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550781"
---
# <a name="assign-users-and-groups-to-an-application"></a>アプリケーションにユーザーとグループを割り当てる

この記事では、PowerShell を使用して Azure Active Directory (Azure AD) でエンタープライズ アプリケーションにユーザーとグループを割り当てる方法について説明します。 アプリケーションにユーザーを割り当てると、そのアプリケーションが、簡単にアクセスできるようにユーザーの [マイ アプリ] ポータルに表示されます。 アプリケーションでロールが公開されている場合は、ユーザーに特定のロールを割り当てることもできます。

## <a name="prerequisites"></a>前提条件

PowerShell を使用してアプリにユーザーを割り当てるには、以下が必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。
- AzureAD モジュールをまだインストールしていない場合は、インストールします (`Install-Module -Name AzureAD` を使用)。 NuGet モジュールまたは新しい Azure Active Directory V2 PowerShell モジュールをインストールするように求められたら「Y」と入力し、ENTER キーを押します。
- グループ ベースの割り当てのための Azure Active Directory Premium P1 または P2。 この記事で説明されている機能に必要なライセンスの詳細については、[Azure Active Directory の価格のページ](https://azure.microsoft.com/pricing/details/active-directory)を参照してください。
- 省略可能:[アプリの構成](add-application-portal-configure.md)の完了。

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>PowerShell を使用してアプリに対してユーザーとグループを割り当てる

1. 管理者特権での Windows PowerShell コマンド プロンプトを開きます。
1. `Connect-AzureAD` を実行してグローバル管理者ユーザー アカウントでサインインします。
1. 次のスクリプトを使用して、アプリケーションにユーザーとロールを割り当てます。

    ```powershell
    # Assign the values to the variables
    $username = "<Your user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id

To assign a group to an enterprise app, you must replace `Get-AzureADUser` with `Get-AzureADGroup` and replace `New-AzureADUserAppRoleAssignment` with `New-AzureADGroupAppRoleAssignment`.

For more information about how to assign a group to an application role, see the documentation for [New-AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment).

### Example

This example assigns the user Britta Simon to the Microsoft Workplace Analytics application using PowerShell.

1. In PowerShell, assign the corresponding values to the variables $username, $app_name and $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"

1. In this example, we don't know what is the exact name of the application role we want to assign to Britta Simon. Run the following commands to get the user ($user) and the service principal ($sp) using the user UPN and the service principal display names.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"

1. Run the command `$sp.AppRoles` to display the roles available for the Workplace Analytics application. In this example, we want to assign Britta Simon the Analyst (Limited access) Role.
   ![Shows the roles available to a user using Workplace Analytics Role](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
1. Assign the role name to the `$app_role_name` variable.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

1. Run the following command to assign the user to the app role:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>PowerShell を使用して、アプリからユーザーとグループの割り当てを解除する

1. 管理者特権での Windows PowerShell コマンド プロンプトを開きます。
1. `Connect-AzureAD` を実行してグローバル管理者ユーザー アカウントでサインインします。 次のスクリプトを使用して、アプリケーションからユーザーとロールを削除します。

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

## <a name="remove-all-users-who-are-assigned-to-the-application"></a>アプリケーションに割り当てられているすべてのユーザーを削除する

   ```powershell

   #Retrieve the service principal object ID.
   $app_name = "<Your App's display name>"
   $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
   $sp.ObjectId

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectId of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

# Remove all users and groups assigned to the application
$assignments | ForEach-Object {
    if ($_.PrincipalType -eq "User") {
        Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    } elseif ($_.PrincipalType -eq "Group") {
        Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    }
}
```

## <a name="next-steps"></a>次の手順

- [Azure portal からユーザー アカウントを作成して割り当てる](add-application-portal-assign-users.md)
- [アプリへのアクセスを管理する](what-is-access-management.md)。
