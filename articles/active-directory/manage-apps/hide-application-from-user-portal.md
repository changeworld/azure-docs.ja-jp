---
title: エンタープライズ アプリケーションを非表示にする
titleSuffix: Azure AD
description: Azure Active Directory アクセス ポータルまたは Microsoft 365 ランチャーでユーザーのエクスペリエンスからエンタープライズ アプリケーションを非表示にする方法。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/23/2021
ms.author: davidmu
ms.reviewer: lenalepa
ms.collection: M365-identity-device-management
ms.openlocfilehash: f80469924b9a4bbc53ac076751026071be2955ad
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550743"
---
# <a name="hide-an-enterprise-application"></a>エンタープライズ アプリケーションを非表示にする

Azure Active Directory でエンタープライズ アプリケーションを非表示にする方法について説明します。 アプリケーションを非表示にしても、アプリケーションに対するユーザーのアクセス許可は維持されます。

## <a name="prerequisites"></a>前提条件

- My Apps ポータルと Microsoft 365 ランチャーにアプリケーションが表示されないようにするには、アプリケーション管理者特権が必要です。

- すべての Microsoft 365 アプリケーションを非表示にするは、グローバル管理者特権が必要です。

## <a name="hide-an-application-from-the-end-user"></a>エンドユーザーに対してアプリケーションを非表示にする

My Apps ポータルと Microsoft 365 アプリケーション ランチャーにアプリケーションが表示されないようにするには、次の手順を使用します。

1. ディレクトリのグローバル管理者として [Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** を選択します。
1. **[アプリケーションの種類]** で、 **[エンタープライズ アプリケーション]** を選択します (まだ選択されていない場合)。
1. 非表示にするアプリケーションを検索し、アプリケーションを選択します。
1. **[ユーザーに表示しますか?]** という質問に対し、 **[いいえ]** を選択します。
1. **[保存]** を選択します。

> [!NOTE]
> これらの手順は、エンタープライズ アプリケーションにのみ適用されます。

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Azure AD PowerShell を使用してアプリケーションを非表示にする

My Apps ポータルからアプリケーションを非表示にするには、アプリケーションのサービス プリンシパルに HideApp タグを手動で追加します。 次の [AzureAD PowerShell](/powershell/module/azuread/#service_principals) コマンドを実行して、アプリケーションの **[ユーザーに表示しますか?]** プロパティを **[いいえ]** に設定します。

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-my-apps-portal"></a>My Apps ポータルに Microsoft 365 アプリケーションが表示されないようにする

My Apps ポータルに一切の Microsoft 365 アプリケーションが表示されないようにするには、次の手順を使用します。 なお、アプリケーションは Office 365 ポータルには表示されます。

1. ディレクトリのグローバル管理者として [Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]** を選択します。
1. **[ユーザー]** を選択します。
1. **[ユーザー設定]** を選択します。
1. **[エンタープライズ アプリケーション]** で、 **[エンド ユーザーがアプリケーションを起動して表示する方法を管理する]** を選択します。
1. **[ユーザーは Office 365 ポータルでのみ Office 365 アプリを表示できる]** で、 **[はい]** を選択します。
1. **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](./assign-user-or-group-access-portal.md)
