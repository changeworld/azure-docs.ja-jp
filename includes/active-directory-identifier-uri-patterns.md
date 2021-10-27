---
author: madansr7
ms.author: saumadan
ms.date: 10/06/2021
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: ca25f04da2f626c55eb167bec6622f51780dc68c
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069572"
---
次の API および HTTP スキームベースのアプリケーション ID URI 形式がサポートされます。 表の後の一覧の説明に従って、プレースホルダーの値を置き換えてください。

| サポートされるアプリケーション ID <br/> URI 形式 | アプリ ID URI の例 |
|--|--|
| _api://\<appId\>_ | _api://fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _api://\<tenantId\>/\<appId\>_ | _api://a8573488-ff46-450a-b09a-6eca0c6a02dc/fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _api://\<tenantId\>/\<string\>_ | _api://a8573488-ff46-450a-b09a-6eca0c6a02dc/api_ |
| _api://\<string\>/<appId\>_ | _api://productapi/fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _https://\<tenantIdDomain\>.onmicrosoft.com/\<string\>_ | _https://contoso.onmicrosoft.com/productsapi_  |
| _https://\<verifiedCustomDomain\>/\<string\>_ |  _https://contoso.onmicrosoft.com/productsapi_ |
| _https://\<string\>.\<verifiedCustomDomain\>_ |  _https://product.contoso.onmicrosoft.com_ |
| _https://\<string\>.\<verifiedCustomDomain\>/\<string\>_ | _https://product.onmicrosoft.com/productsapi_   |


- _\<appId\>_ - アプリケーション オブジェクトのアプリケーション識別子 (appId) プロパティ。
- _\<string\>_ - ホストまたは API パスのセグメントの文字列値。
- _\<tenantId\>_ - すべての Azure AD テナントには、 _\<tenantId\>.onmicrosoft.com_ 形式の 2 つの初期ドメインが付きます。ここで、 _\<tenantID\>_ は、テナント作成時にテナント作成者が指定した初期ドメイン名と、Azure 内のテナントを表すために Azure によって生成された GUID です。
- _\<verifiedCustomDomain\>_ - Azure AD テナント用に構成された[検証済みのカスタム ドメイン](../articles/active-directory/fundamentals/add-custom-domain.md)。

