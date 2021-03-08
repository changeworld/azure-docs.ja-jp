---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 643e6f14ef11c66361855a859576081a990d8370
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2021
ms.locfileid: "100105633"
---
## <a name="cmmc-level-3"></a>CMMC レベル 3

すべての Azure サービスで使用可能な Azure Policy 組み込みがこのコンプライアンス標準にどのように対応するのかを確認するには、[Azure Policy の規制コンプライアンス - CMMC レベル 3](../../../../articles/governance/policy/samples/cmmc-l3.md) に関する記事をご覧ください。
このコンプライアンス標準の詳細については、[サイバーセキュリティ成熟度モデル認定 (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf) に関するドキュメントをご覧ください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|システムと通信の保護 |SC.3.177 |CUI の機密性を保護するために使用する場合は、FIPS 検証済みの暗号化を採用する。 |[Azure Synapse ワークスペースでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d52b2d-e161-4dfa-a82b-55e564167385) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceCMK_Audit.json) |

