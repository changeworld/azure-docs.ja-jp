---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f6e4aaf143f077c40c6184fd8e55e437763ea2c6
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108762378"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[HTTPS 入力のある Azure Media Services ジョブでは入力 URI を許可された URI パターンに制限する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9914afe-31cd-4b8a-92fa-c887f847d477) |Media Services ジョブによって使用される HTTPS 入力を既知のエンドポイントに制限します。 HTTPS エンドポイントからの入力は、許可されるジョブ入力パターンとして空のリストを設定することで完全に無効にすることができます。 ジョブの入力で "baseUri" が指定されている場合、その値とパターンが照合されます。"baseUri" が設定されていない場合、パターンは "files" プロパティと照合されます。 |Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/Jobs_RestrictHttpInputs.json) |
