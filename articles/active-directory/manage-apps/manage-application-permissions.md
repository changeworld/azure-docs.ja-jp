---
title: ユーザーと管理者のアクセス許可を管理する - Azure Active Directory | Microsoft Docs
description: Azure AD でアプリケーションのアクセス許可を確認および管理する方法について説明します。 たとえば、アプリケーションに付与されているすべてのアクセス許可を取り消す場合などです。
services: active-directory
author: mimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d878c7b2f78d037e89235f3bb30c02fd11a7ae
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277602"
---
# <a name="take-action-on-overpriviledged-or-suspicious-application-in-azure-active-directory"></a>Azure Active Directory で、過剰な特権が与えられている、または疑わしいアプリケーションに対してアクションを実行する

アプリケーションのアクセス許可を確認および管理する方法について説明します。 この記事では、シナリオに基づいて、アプリケーションをセキュリティで保護するために実行できるさまざまなアクションについて説明します。 これは、ユーザーまたは管理者の同意によって Azure Active Directory (Azure AD) テナントに追加されたすべてのアプリケーションに適用されます。

アプリケーションに同意する行為の詳細については、「[Azure Active Directory 同意フレームワーク](../develop/consent-framework.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

以下のアクションを実行できるようになるには、グローバル管理者、アプリケーション管理者、またはクラウド アプリケーション管理者としてサインインする必要があります。

アプリケーションへのアクセスを制限するには、ユーザーの割り当てが必要で、その後、アプリケーションにユーザーまたはグループを割り当てる必要があります。  詳細については、[ユーザーとグループの割り当て方法](methods-for-assigning-users-and-groups.md)に関するページを参照してください。

アクションを実行するためのコンテキスト PowerShell スクリプトを取得するには、Azure AD ポータルにアクセスします。
 
1. 全体管理者、アプリケーション管理者、またはクラウド アプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に選択します。
3. アクセスを制限するアプリケーションを選択します。
4. **[アクセス許可]** を選択します。 コマンド バーで **[アクセス許可の確認]** を選択します。

![アクセス許可の確認](./media/manage-application-permissions/review-permissions.png)

## <a name="i-want-to-control-access-to-an-application"></a>アプリケーションへのアクセスを制御したい

このアプリケーションへのアクセスは、ユーザー割り当て設定を有効にすることによって制限することをお勧めします。

1. 全体管理者、アプリケーション管理者、またはクラウド アプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に選択します。
3. アクセスを制限するアプリケーションを選択します。
4. **[プロパティ]** を選択し、[ユーザー要件の必須設定] を [はい] に設定します。
5. **[ユーザーとグループ]** を選択し、アプリケーションに割り当てられている不要なユーザーを削除します。
6. アプリケーションにユーザーまたはグループを割り当てます。

省略可能。PowerShell を使用して、アプリケーションに割り当てられているすべてのユーザーを削除できます。

## <a name="i-want-to-revoke-all-permissions-for-an-application"></a>アプリケーションのすべてのアクセス許可を取り消したい

PowerShell を使用すると、このアプリケーションに許可されているすべてのアクセス許可が取り消されます。

> [!NOTE]
> 現在許可されているアクセス許可を取り消しても、ユーザーがアプリケーションに再参加するのを止めることはできません。 ユーザーがアプリケーションを同意できないようにする場合は、「[エンド ユーザーがアプリケーションに同意する方法を構成する](configure-user-consent.md)」をご覧ください。

省略可能。アプリケーションを無効にして、ユーザーがアプリにアクセスできないようにし、アプリケーションがデータにアクセスするのをブロックできます。

1. 全体管理者、アプリケーション管理者、またはクラウド アプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に選択します。
3. アクセスを制限するアプリケーションを選択します。
4. **[プロパティ]** を選択し、[ユーザーのサインインが有効になっていますか?] を [いいえ] に設定します。

## <a name="application-is-suspicious-and-i-want-to-investigate"></a>アプリケーションが疑わしいため調査したい

ユーザー割り当て設定を有効にしてこのアプリケーションへのアクセスを制限し、ユーザーと管理者がアプリケーションに付与したアクセス許可を確認することをお勧めします。

1. 全体管理者、アプリケーション管理者、またはクラウド アプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。
3. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に選択します。
5. アクセスを制限するアプリケーションを選択します。
6. **[プロパティ]** を選択し、[ユーザー要件の必須設定] を [はい] に設定します。
7. **[アクセス許可]** を選択し、管理者とユーザーが同意したアクセス許可を確認します。

省略可能。次のことができます。

- PowerShell を使用して、アプリケーションにサインインできないように割り当てられているすべてのユーザーを削除します。
- PowerShell を使用して、アプリケーションにアクセスできるユーザーの更新トークンを無効にします。
- PowerShell を使用して、このアプリケーションのすべてのアクセス許可を取り消します
- アプリケーションを無効にして、ユーザーのアクセスをブロックし、このアプリケーションのデータへのアクセスを停止します。


## <a name="application-is-malicious-and-im-compromised"></a>アプリケーションは悪意があり、侵害されている

アプリケーションを無効にして、ユーザーがアプリにアクセスできないようにし、アプリケーションがデータにアクセスするのをブロックすることをお勧めします。 代わりにアプリケーションを削除すると、エンド ユーザーはアプリケーションに再度同意して、データへのアクセスを許可できてしまいます。

1. 全体管理者、アプリケーション管理者、またはクラウド アプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に選択します。
3. アクセスを制限するアプリケーションを選択します。
4. **[プロパティ]** を選択し、オブジェクト ID をコピーします。

### <a name="powershell-commands"></a>PowerShell コマンド


サービス プリンシパル オブジェクト ID を取得する

1. 全体管理者、アプリケーション管理者、またはクラウド アプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に選択します。
3. アクセスを制限するアプリケーションを選択します。
4. **[プロパティ]** を選択し、オブジェクト ID をコピーします。

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
アプリケーションに割り当てられているすべてのユーザーを削除します。
 ```powershell
    Connect-AzureAD

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

アプリケーションに付与されたアクセス許可を取り消す

```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get all delegated permissions for the service principal
    $spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

    # Remove all delegated permissions
    $spOAuth2PermissionsGrants | ForEach-Object {
        Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
    }

    # Get all application permissions for the service principal
    $spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

    # Remove all delegated permissions
    $spApplicationPermissions | ForEach-Object {
        Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
    }
```
更新トークンを無効にする
```powershell
        Connect-AzureAD

        # Get Service Principal using objectId
        $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

        # Get Azure AD App role assignments using objectID of the Service Principal
        $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

        # Revoke refresh token for all users assigned to the application
        $assignments | ForEach-Object {
            Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
        }
```
## <a name="next-steps"></a>次のステップ
- [アプリケーションの同意の管理と同意要求の評価](manage-consent-requests.md)
- [ユーザーの同意の構成](configure-user-consent.md)
- [管理者の同意ワークフローの構成](configure-admin-consent-workflow.md)
