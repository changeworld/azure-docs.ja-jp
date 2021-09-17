---
author: johndeu
ms.service: media-services
ms.topic: include
ms.date: 08/17/2021
ms.author: johndeu
ms.openlocfilehash: d6641a3ca8181f7b3c8538179623549cc92a098a
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429169"
---
## <a name="azure-policy-definitions-for-private-links"></a>Private Link に対する Azure Policy の定義

Azure Media Services では、[Azure Policy](../../../governance/policy/overview.md) の定義に基づくいくつかの組み込みのユース ケースがサポートされています。 

Private Link では、次のポリシー定義を使用できます。

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Media Services アカウントでは、Private Link をサポートする API を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa77d8bb4-8d22-4bc1-a884-f582a705b480) |Media Services アカウントは、プライベート リンクをサポートする API を使用して作成する必要があります。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_RequirePrivateLinkSupport_Audit.json) |
|[Azure Media Services ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a591bf5-918e-4a5f-8dad-841863140d61) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 プライベート エンドポイントを Media Services にマッピングすることにより、データ漏えいのリスクを軽減することができます。 プライベート リンクの詳細については、[https://aka.ms/mediaservicesprivatelinkdocs](../../../../articles/media-services/latest/security-private-link-how-to.md) を参照してください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_PrivateLink_AuditIfNotExists.json) |
|[プライベート DNS ゾーンを使用するように Azure Media Services を構成する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4a7f6c1-585e-4177-ad5b-c2c93f4bb991) |プライベート DNS ゾーンを使用して、プライベート エンドポイントの DNS 解決をオーバーライドします。 プライベート DNS ゾーンが仮想ネットワークにリンクされ、Media Services アカウントに解決されます。 詳細については、[https://aka.ms/mediaservicesprivatelinkdocs](../../../../articles/media-services/latest/security-private-link-how-to.md) を参照してください。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_PrivateLinkDns_DeployIfNotExists.json) |
|[プライベート エンドポイントを使用して Azure Media Services を構成する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5632066-946d-4766-9544-cd79bcc1286e) |プライベート エンドポイントを使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート エンドポイントを Media Services にマッピングすることにより、データ漏えいのリスクを軽減することができます。 プライベート リンクの詳細については、[https://aka.ms/mediaservicesprivatelinkdocs](../../../../articles/media-services/latest/security-private-link-how-to.md) を参照してください。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_PrivateLink_DeployIfNotExists.json) |

すべての組み込みのポリシー定義とユース ケースの詳細な一覧については、「[Azure Policy for Media Services](../security-azure-policy.md)」(Media Services 向けの Azure Policy) を参照してください。

Azure Policy の定義をデプロイして使用する方法の詳細については、「[Azure Policy とは](../../../governance/policy/overview.md)」の記事を参照してください。