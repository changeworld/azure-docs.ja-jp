---
title: 独自に開発したアプリケーションのトークン有効期間の既定値を変更する方法 | Microsoft Docs
description: Azure AD で開発しているアプリケーションのトークンの有効期間ポリシーを更新する方法
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 7895d5d70b9dc264d6adae42060acc5d3fdf3237
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094344"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>独自に開発したアプリケーションのトークン有効期間の既定値を変更する方法

Azure AD Premium では、非機密クライアントに対して発行されたトークンの有効期間をアプリ開発者とテナント管理者が構成できます。 トークンの有効期間ポリシーは、テナント全体またはアクセスされるリソースに対して設定されます。

1. トークンの有効期間ポリシーを設定するには、[Azure AD PowerShell モジュール](https://www.powershellgallery.com/packages/AzureADPreview)をダウンロードする必要があります。
1. **Connect-AzureAD -Confirm** コマンドを実行します。

    次のサンプル ポリシーは、MaxAgeSingleFactor 更新トークンを設定します。 次のポリシーを作成します。 ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>次の手順

* 所属組織内のすべてのアプリ、マルチテナント アプリ、または所属組織内の特定のサービス プリンシパルに対するトークンの有効期間を設定する方法を含む、Azure AD によって発行されたトークンの有効期間を構成する方法については、[Azure AD で構成可能なトークンの有効期間](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)に関するページを参照してください。 
* [Azure AD のトークン リファレンス](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

