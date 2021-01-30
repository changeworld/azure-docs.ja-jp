---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 11e14a435f6ea33cf9f4f8a7dd3804c95187c9bf
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99106249"
---
## <a name="cmmc-level-3"></a>CMMC レベル 3

すべての Azure サービスに対して使用可能な Azure Policy 組み込みがこのコンプライアンス標準にどのように対応しているかを確認するには、「 [法令遵守の Azure Policy-CMMC レベル 3](../../../../articles/governance/policy/samples/cmmc-l3.md)」を参照してください。
この準拠基準の詳細については、「 [サイバーセキュリティ成熟度モデル認定 (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)」を参照してください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|アクセス制御 |1.001 |認証されたユーザー、承認されたユーザーに代わって動作するプロセス、およびデバイス (他の情報システムを含む) に対する情報システムのアクセスを制限します。 |[CORS で API for FHIR へのアクセスをすべてのドメインには許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|アクセス制御 |1.002 |権限のあるユーザーが実行を許可されているトランザクションおよび関数の種類に対して、情報システムのアクセスを制限します。 |[CORS で API for FHIR へのアクセスをすべてのドメインには許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|アクセス制御 |2.016 |承認された認可に従って CUI のフローを制御する。 |[CORS で API for FHIR へのアクセスをすべてのドメインには許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|構成管理 |CM. 3.068 |不要なプログラム、関数、ポート、プロトコル、およびサービスの使用を制限、無効化、または禁止する。 |[CORS で API for FHIR へのアクセスをすべてのドメインには許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|システムと通信の保護 |3.177 |CUI の機密性を保護するために使用する場合は、FIPS 検証済みの暗号化を使用します。 |[Azure API for FHIR での保存データの暗号化には、カスタマー マネージド キー (CMK) を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|システムと通信の保護 |3.183 |ネットワーク通信トラフィックを既定で拒否し、例外 (つまり、すべて拒否、例外による許可) によるネットワーク通信トラフィックを許可します。 |[CORS で API for FHIR へのアクセスをすべてのドメインには許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |

