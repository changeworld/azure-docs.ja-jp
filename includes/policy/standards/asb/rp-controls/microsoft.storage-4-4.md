---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/13/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f114357ca6bc637968c4738e07915741ced77e51
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122264698"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ストレージ アカウントへの安全な転送を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |ストレージ アカウント内の安全な転送の要件を監査します。 安全な転送は、ストレージ アカウントに、セキュリティで保護された接続 (HTTPS) からの要求のみを受け入れるように強制するオプションです。 HTTPS を使用することにより、サーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
