---
title: カスタム Azure AD アプリのトークン有効期間の既定値を変更する
description: Azure AD で開発しているアプリケーションのトークンの有効期間ポリシーを更新する方法
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 7eb01ccda3c3e13827a8977b8ee0e244aef6b0be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613240"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>独自に開発したアプリケーションのトークン有効期間の既定値を変更する方法

この記事では、Azure AD PowerShell を使用して、トークンの有効期間ポリシーを設定する方法を示します。 Azure AD Premium では、非機密クライアントに対して発行されたトークンの有効期間をアプリ開発者とテナント管理者が構成できます。 トークンの有効期間ポリシーは、テナント全体またはアクセスされるリソースに対して設定されます。

トークンの有効期間ポリシーを設定するには、[Azure AD PowerShell モジュール](https://www.powershellgallery.com/packages/AzureADPreview)をダウンロードする必要があります。
**Connect-AzureAD -Confirm** コマンドを実行します。

ここでは、ユーザーが、Web アプリで頻繁に認証を必要とするポリシーの例を示します。 このポリシーは、アクセス トークンと ID トークンの有効期間と、多要素セッション トークンの最長有効期間を Web アプリのサービス プリンシパルに設定します。 ポリシーを作成し、これをサービス プリンシパルに割り当てます。 サービス プリンシパルの ObjectId も取得する必要があります。

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>次のステップ

* 所属組織内のすべてのアプリ、マルチテナント アプリ、または所属組織内の特定のサービス プリンシパルに対するトークンの有効期間を設定する方法を含む、Azure AD によって発行されたトークンの有効期間を構成する方法については、[Azure AD で構成可能なトークンの有効期間](./active-directory-configurable-token-lifetimes.md)に関するページを参照してください。 
* [Azure AD のトークン リファレンス](./id-tokens.md)