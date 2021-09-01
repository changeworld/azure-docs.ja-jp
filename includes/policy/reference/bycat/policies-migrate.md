---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c42aae91739f4abd03f5be4536aee1f6f0d33bdc
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123097512"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[プライベート DNS ゾーンを使用するよう Azure Migrate リソースを構成する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7590a335-57cf-4c95-babd-ecbc8fafeb1f) |プライベート DNS ゾーンを使用して、プライベート エンドポイントの DNS 解決をオーバーライドします。 プライベート DNS ゾーンは、Azure Migrate プロジェクトを解決するために、仮想ネットワークにリンクします。 詳細については、[https://aka.ms/privatednszone](../../../../articles/private-link/private-endpoint-dns.md) を参照してください。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Migrate/Migrate_PrivateDNSZone_DeployIfNotExists.json) |