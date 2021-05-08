---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: ca8963ed8928745a6d5918c86021199432339c83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104611873"
---
| プロパティ | 説明 |
|:--- |:---|
|**identity / type** | 要求の作成に使用された認証の種類。 例: `OAuth`、`Kerberos`、`SAS Key`、`Account Key`、`Anonymous` |
|**identity / tokenHash**|要求で使用される認証トークンの SHA-256 ハッシュ。 <br>認証の種類が `Account Key` である場合、形式は "key1 \| key2 (キーの SHA256 ハッシュ)" になります。 (例: `key1(5RTE343A6FEB12342672AFD40072B70D4A91BGH5CDF797EC56BF82B2C3635CE)`)。 <br>認証の種類が `SAS Key` である場合、形式は "key1 \| key2 (キーの SHA 256 ハッシュ),SasSignature (SAS トークンの SHA 256 ハッシュ)" になります。 (例: `key1(0A0XE8AADA354H19722ED12342443F0DC8FAF3E6GF8C8AD805DE6D563E0E5F8A),SasSignature(04D64C2B3A704145C9F1664F201123467A74D72DA72751A9137DDAA732FA03CF)`)。 認証の種類が `OAuth` の場合、形式は "OAuth トークンの SHA 256 ハッシュ" になります。 例: `B3CC9D5C64B3351573D806751312317FE4E910877E7CBAFA9D95E0BE923DW25C`<br> その他の認証の種類では、tokenHash フィールドはありません。 |
|**authorization / action** | 要求に割り当てられているアクション。 |
|**authorization / roleAssignmentId** | ロールの割り当て ID (例: `4e2521b7-13be-4363-aeda-111111111111`)。|
|**authorization / roleDefinitionId** | ロール定義 ID (例: `ba92f5b4-2d11-453d-a403-111111111111"`)。|
|**principals / id** | セキュリティ プリンシパルの ID (例: `a4711f3a-254f-4cfb-8a2d-111111111111`)。|
|**principals / type** | セキュリティ プリンシパルの種類 (例: `ServicePrincipal`)。 |
|**requester / appID** | 要求元として使用される Open Authorization (OAuth) アプリケーション ID <br> (例: `d3f7d5fe-e64a-4e4e-871d-333333333333`)。|
|**requester / audience** | 要求の OAuth 対象ユーザー (例: `https://storage.azure.com`)。 |
|**requester / objectId** | 要求元の OAuth オブジェクト ID。 Kerberos 認証の場合は、Kerberos で認証されたユーザーのオブジェクト識別子を表します (例: `0e0bf547-55e5-465c-91b7-2873712b249c`)。 |
|**requester / tenantId** | 識別子の OAuth テナント ID (例: `72f988bf-86f1-41af-91ab-222222222222`)。|
|**requester / tokenIssuer** | OAuth トークン発行者 (例: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`)。|
|**requester / upn** | 要求元のユーザー プリンシパル名 (UPN) (例: `someone@contoso.com`)。 |
|**requester / userName** | このフィールドは、内部使用専用として予約されています。|
