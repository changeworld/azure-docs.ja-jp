---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8c8c26a36332f13c21eb9d5cf0f4642fac4a2044
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655371"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[プレビュー\]: プライベート DNS ゾーンを使用するよう Azure Recovery Services コンテナーを構成する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F942bd215-1a66-44be-af65-6a1c0318dbe2) |プライベート DNS ゾーンを使用して、プライベート エンドポイントの DNS 解決をオーバーライドします。 プライベート DNS ゾーンが仮想ネットワークにリンクされ、Recovery Services コンテナーに解決されます。 詳細については、[https://aka.ms/privatednszone](../../../../articles/private-link/private-endpoint-dns.md) を参照してください。 |DeployIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Site%20Recovery/SiteRecovery_PrivateDnsZones_DeployIfNotExist.json) |
|[\[プレビュー \]: プライベート エンドポイントを Azure Recovery Services コンテナーに構成する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe95a8a5c-0987-421f-84ab-df4d88ebf7d1) |プライベート エンドポイントを使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークが Azure サービスに接続されます。 Recovery Services コンテナーのサイトの回復リソースにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクを軽減できます。 プライベート リンクを使用するには、マネージド サービス ID を Recovery Services コンテナーに割り当てる必要があります。 プライベート リンクの詳細については、[https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-private-endpoints](../../../../articles/site-recovery/azure-to-azure-how-to-enable-replication-private-endpoints.md) を参照してください。 |DeployIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Site%20Recovery/SiteRecovery_PrivateEndpoints_DeployIfNotExist.json) |
|[\[プレビュー \]: Recovery Services コンテナーではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F11e3da8c-1d68-4392-badd-0ff3c43ab5b0) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Azure Recovery Services コンテナーにマッピングすることにより、データ漏えいのリスクが軽減されます。 Azure Site Recovery のプライベート リンクの詳細については、[https://aka.ms/HybridScenarios-PrivateLink](../../../../articles/site-recovery/hybrid-how-to-enable-replication-private-endpoints.md) および [https://aka.ms/AzureToAzure-PrivateLink](../../../../articles/site-recovery/azure-to-azure-how-to-enable-replication-private-endpoints.md) を参照してください。 |Audit、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Site%20Recovery/RecoveryServices_SiteRecovery_PrivateEndpoint_Audit.json) |