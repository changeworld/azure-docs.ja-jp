---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/11/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 887d2784d4949a70763e6c08ce3ec72aaddd0bcd
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112019771"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Media Services アカウントでは、Private Link をサポートする API を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa77d8bb4-8d22-4bc1-a884-f582a705b480) |Media Services アカウントは、プライベート リンクをサポートする API を使用して作成する必要があります。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_RequirePrivateLinkSupport_Audit.json) |
|[レガシ v2 API へのアクセスを許可する Azure Media Services アカウントをブロックする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fccf93279-9c91-4143-a841-8d1f21505455) |Media Services レガシ v2 API は、Azure Policy を使用して管理できない要求を許可します。 2020-05-01 API 以降を使用して作成された Media Services リソースは、レガシ v2 API へのアクセスをブロックします。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_BlockRestV2_Audit.json) |
|[Azure Media Services コンテンツ キー ポリシーではトークン認証を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdaccf7e4-9808-470c-a848-1c5b582a1afb) |コンテンツ キー ポリシーは、コンテンツ キーにアクセスするために満たす必要がある条件を定義します。 トークン制限により、認証サービス (Azure Active Directory など) からの有効なトークンを持つユーザーしかコンテンツ キーにアクセスできなくなります。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/ContentKeyPolicies_RequireTokenAuth_Audit.json) |
|[HTTPS 入力のある Azure Media Services ジョブでは入力 URI を許可された URI パターンに制限する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9914afe-31cd-4b8a-92fa-c887f847d477) |Media Services ジョブによって使用される HTTPS 入力を既知のエンドポイントに制限します。 HTTPS エンドポイントからの入力は、許可されるジョブ入力パターンとして空のリストを設定することで完全に無効にすることができます。 ジョブの入力で "baseUri" が指定されている場合、その値とパターンが照合されます。"baseUri" が設定されていない場合、パターンは "files" プロパティと照合されます。 |Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/Jobs_RestrictHttpInputs.json) |
