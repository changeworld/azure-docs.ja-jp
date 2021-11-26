---
title: ユーザーがサインインする方法を無効にする
titleSuffix: Azure AD
description: Azure Active Directory で、ユーザーがサインインできないようにエンタープライズ アプリケーションを無効にする方法
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/23/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72f0dc346e23d78ac9725b388b99f1a440136672
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548408"
---
# <a name="disable-how-a-user-signs-in-for-an-application"></a>アプリケーションにユーザーがサインインする方法を無効にする

この記事では、ユーザーが Azure Active Directory でアプリケーションにサインインする方法を無効にします。

## <a name="prerequisites"></a>前提条件

ユーザーがサインインする方法を無効にするには、以下が必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。

## <a name="disable-how-a-user-signs-in"></a>ユーザーがサインインする方法を無効にする

1. ディレクトリのグローバル管理者として [Azure portal](https://portal.azure.com) にサインインします。
1. **Azure Active Directory** を検索して選択します。
1. **[エンタープライズ アプリケーション]** を選択します。
1. ユーザーがサインインすることを無効にするアプリケーションを検索し、そのアプリケーションを選択します。
1. **[プロパティ]** を選択します。
1. **[ユーザーのサインインが有効になっていますか?]** では **[いいえ]** を選択します。
1. **[保存]** を選択します。

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Azure AD PowerShell を使用して、一覧にないアプリを無効にする

AzureAD モジュールをインストール済みであることを確認します (コマンド Install-Module-Name AzureAD を使用します)。 NuGet モジュールまたは新しい Azure Active Directory V2 PowerShell モジュールをインストールするように求められたら、「Y」と入力して Enter キーを押します。

Enterprise アプリの一覧に表示されていないアプリの AppId が分かっている場合 (たとえば、アプリを削除したか、アプリが Microsoft によって事前承認されているためにサービスプリンシパルがまだ作成されていないため)、アプリのサービス プリンシパルを手動で作成してから、[AzureAD PowerShell コマンドレット](/powershell/module/azuread/New-AzureADServicePrincipal)を使用して無効にすることができます。

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>次のステップ

- [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](./assign-user-or-group-access-portal.md)
