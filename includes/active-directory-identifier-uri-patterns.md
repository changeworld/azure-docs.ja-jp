---
author: madansr7
ms.author: saumadan
ms.date: 10/06/2021
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 133b4d8c1d3c294943ad6224c3d1436b63921462
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050579"
---
次の API および HTTP スキームベースのアプリケーション ID URI 形式がサポートされます。 表の後の一覧の説明に従って、プレースホルダーの値を置き換えてください。

| サポートされるアプリケーション ID <br/> URI 形式 | アプリ ID URI の例 |
|--|--|
| _api://\<appId\>_ | _api://fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _api://\<tenantId\>/\<appId\>_ | _api://a8573488-ff46-450a-b09a-6eca0c6a02dc/fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _api://\<tenantId\>/\<string\>_ | _api://a8573488-ff46-450a-b09a-6eca0c6a02dc/api_ |
| _api://\<string\>/<appId\>_ | _api://productapi/fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _https://\<tenantIdDomain\>.onmicrosoft.com/\<string\>_ | _`https://contoso.onmicrosoft.com/productsapi`_  |
| _https://\<verifiedCustomDomain\>/\<string\>_ |  _`https://contoso.onmicrosoft.com/productsapi`_ |
| _https://\<string\>.\<verifiedCustomDomain\>_ |  _`https://product.contoso.onmicrosoft.com`_ |
| _https://\<string\>.\<verifiedCustomDomain\>/\<string\>_ | _`https://product.onmicrosoft.com/productsapi`_   |


- _\<appId\>_ - アプリケーション オブジェクトのアプリケーション識別子 (appId) プロパティ。
- _\<string\>_ - ホストまたは API パスのセグメントの文字列値。
- _\<tenantId\>_ - すべての Azure AD テナントには、 _\<tenantId\>.onmicrosoft.com_ 形式の 2 つの初期ドメインが付きます。ここで、 _\<tenantID\>_ は、テナント作成時にテナント作成者が指定した初期ドメイン名と、Azure 内のテナントを表すために Azure によって生成された GUID です。
- _\<verifiedCustomDomain\>_ - Azure AD テナント用に構成された [検証済みのカスタム ドメイン](../articles/active-directory/fundamentals/add-custom-domain.md)。

