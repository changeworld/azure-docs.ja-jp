---
title: アプリケーションに付与されるアクセス許可の確認
titleSuffix: Azure AD
description: Azure Active Directory でアプリケーションのアクセス許可を確認および管理する方法について説明します。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2021
ms.author: davidmu
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeb8f00735e9455fd9d6adbdaf9e0cdec6940377
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553175"
---
# <a name="review-permissions-granted-to-applications"></a>アプリケーションに付与されるアクセス許可の確認

この記事では、Azure Active Directory (Azure AD) テナントのアプリケーションに付与されているアクセス許可を確認する方法について説明します。 悪意のあるアプリケーションが検出された場合、またはアプリケーションに必要以上のアクセス許可が付与されている場合は、アクセス許可の確認が必要になることがあります。

この記事のこれらの手順は、ユーザーまたは管理者の同意によって Azure Active Directory (Azure AD) テナントに追加されたすべてのアプリケーションに適用されます。 アプリケーションに同意する行為の詳細については、「[Azure Active Directory 同意フレームワーク](../develop/consent-framework.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

アプリケーションに付与されたアクセス許可を確認するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。

アクションを実行するためのコンテキスト PowerShell スクリプトを取得するには、Azure AD ポータルにアクセスします。

## <a name="review-application-permissions"></a>アプリケーションの許可をレビューする

アプリケーションのアクセス許可を確認するには:

1. 前提条件セクションに記載されているいずれかのロールを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]** を選択し、 **[エンタープライズ アプリケーション]** を選択します。
1. アクセスを制限するアプリケーションを選択します。
1. **[アクセス許可]** を選択します。 コマンド バーで **[アクセス許可の確認]** を選択します。
![[アクセス許可の確認] ウィンドウのスクリーンショット。](./media/manage-application-permissions/review-permissions.png)
1. 「**このアプリケーションのアクセス許可を確認する理由を教えてください。** 」という質問の後に一覧表示されたオプションのいずれかを選択して、アプリケーションのアクセス許可を確認する理由を指定します。

各オプションでは、アプリケーションへのユーザー アクセスを制御し、アプリケーションに付与されたアクセス許可を確認できる PowerShell スクリプトが生成されます。 アプリケーションへのユーザー アクセスを制御する方法については、「[アプリケーションへのユーザー アクセスの削除方法](methods-for-removing-user-access.md)」を参照してください。

## <a name="revoke-permissions-using-powershell-commands"></a>PowerShell コマンドを使用してアクセス許可を取り消す

次の PowerShell スクリプトを使用すると、このアプリケーションに付与されているすべてのアクセス許可が取り消されます。

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

> [!NOTE]
> 現在の付与されているアクセス許可を取り消しても、ユーザーのアプリケーションへの再同意は停止されません。 ユーザーの同意をブロックしたい場合は、[ユーザーがアプリケーションに同意する方法の構成](configure-user-consent.md)に関するページを参照してください。

## <a name="invalidate-the-refresh-tokens"></a>更新トークンを無効にする

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

## <a name="next-steps"></a>次の手順

- [管理者の同意ワークフローの構成](configure-admin-consent-workflow.md)
