---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0cb2e1ca35d61a829acf0bcb12b581530e26e3c6
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319633"
---
## <a name="azure-security-benchmark"></a>Azure セキュリティ ベンチマーク

[Azure セキュリティ ベンチマーク](../../../../articles/security/benchmarks/overview.md)には、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 このサービスを完全に Azure セキュリティ ベンチマークにマップする方法については、[Azure セキュリティ ベンチマーク マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)に関するページを参照してください。

すべての Azure サービスに対して使用可能な Azure Policy 組み込みを、このコンプライアンス基準に対応させる方法については、[Azure Policy の規制コンプライアンス - Azure セキュリティ ベンチマーク](../../../../articles/governance/policy/samples/azure-security-benchmark.md)に関するページを参照してください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|データ保護 |4.4. |転送中のすべての機密情報を暗号化する |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

すべての Azure サービスに対して使用可能な Azure Policy 組み込みがこのコンプライアンス標準にどのように対応するのかを確認するには、[Azure Policy の規制コンプライアンス - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md) に関するページを参照してください。
このコンプライアンス標準の詳細については、[HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html) に関するページを参照してください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|ネットワーク接続コントロール |0809.01n2Organizational.1234 - 01.n |ネットワーク トラフィックは、ファイアウォールと、各ネットワーク アクセス ポイントまたは外部通信サービスのマネージド インターフェイスに対するその他のネットワーク関連の制限を介し、組織のアクセス制御ポリシーに従って制御されます。 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|ネットワーク接続コントロール |0810.01n2Organizational.5 - 01.n |送信される情報はセキュリティで保護され、少なくとも、オープンな公衆ネットワークでは暗号化されます。 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|ネットワーク接続コントロール |0811.01n2Organizational.6 - 01.n |トラフィック フロー ポリシーの例外は、サポートしているミッションとビジネス ニーズおよび例外の期間と共に文書化され、少なくとも年に 1 回確認されます。トラフィック フロー ポリシーの例外は、明示的なミッションとビジネス ニーズによってサポートされなくなった時点で削除されます。 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|ネットワーク接続コントロール |0812.01n2Organizational.8 - 01.n |リモート以外の接続を確立するリモート デバイスは、外部 (リモート) リソースと通信することを許可されません。 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|ネットワーク接続コントロール |0814.01n1Organizational.12 - 01.n |ユーザーが内部ネットワークに接続できるかどうかは、アクセス制御ポリシーと、臨床およびビジネス アプリケーションの要件に従って、マネージド インターフェイスで、"既定で拒否、例外で許可" ポリシーを使用して制限されます。 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|外部パーティに関連するリスクの特定 |1451.05iCSPOrganizational.2 - 05.i |クラウド サービス プロバイダーは、サプライ チェーン内のすべての担当者に対する、適切な職務の分離、ロールベースのアクセス、および最小特権アクセスにより、データ セキュリティ リスクを軽減および抑止するためのコントロールを設計および実装します。 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|オンライン トランザクション |0946.09y2Organizational.14 - 09.y |組織では、トランザクションに関係する各パーティの間で暗号化を使用し、各パーティで電子署名を使用することを、要求します。 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

すべての Azure サービスに対して使用可能な Azure Policy 組み込みをこのコンプライアンス基準に対応させる方法については、[Azure Policy の規制コンプライアンス - NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md) に関するページを参照してください。
このコンプライアンス標準の詳細については、[NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final) に関するページを参照してください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|システムと通信の保護 |3.13.1 |組織システムの外部境界と主要な内部境界で、通信 (つまり、組織システムによって送受信される情報) を監視、制御、および保護する。 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|システムと通信の保護 |3.13.8 |代替の物理的な保護手段によって保護されている場合を除き、送信中に CUI の不正な開示を防ぐための暗号化メカニズムを実装する。 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

すべての Azure サービスに対して使用可能な Azure Policy 組み込みを、このコンプライアンス基準に対応させる方法については、[Azure Policy の規制コンプライアンス - NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md) に関するページを参照してください。
このコンプライアンス標準の詳細については、「[NIST SP 800-53 R4](https://nvd.nist.gov/800-53)」を参照してください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|システムと通信の保護 |SC-8 (1) |送信の機密性と整合性 \| 暗号化または代替の物理的保護 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

