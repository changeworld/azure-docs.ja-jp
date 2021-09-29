---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 940428fb0847d0b7dbca7a0918f8590430be0b70
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128678833"
---
## <a name="australian-government-ism-protected"></a>Australian Government ISM PROTECTED

すべての Azure サービスに対して使用可能な Azure Policy 組み込みを、このコンプライアンス基準に対応させる方法については、[Azure Policy の規制コンプライアンス - Australian Government ISM PROTECTED](../../../../articles/governance/policy/samples/australia-ism.md) に関するページを参照してください。
このコンプライアンス基準の詳細については、[Australian Government ISM PROTECTED](https://www.cyber.gov.au/acsc/view-all-content/ism) を参照してください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|データベース システムのガイドライン - データベース サーバー |1277 |データベース サーバーと Web サーバー間の通信 - 1277 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|ソフトウェア開発のガイドライン - Web アプリケーション開発 |1552 |Web アプリケーションの相互作用 - 1552 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="azure-security-benchmark"></a>Azure セキュリティ ベンチマーク

[Azure セキュリティ ベンチマーク](/security/benchmark/azure/introduction)には、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 このサービスを完全に Azure セキュリティ ベンチマークにマップする方法については、「[Azure Security Benchmark mapping files](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)」 (Azure セキュリティ ベンチマークのマッピング ファイル) を参照してください。

すべての Azure サービスに対して使用可能な Azure Policy 組み込みを、このコンプライアンス基準に対応させる方法については、[Azure Policy の規制コンプライアンス - Azure セキュリティ ベンチマーク](../../../../articles/governance/policy/samples/azure-security-benchmark.md)に関するページを参照してください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|ネットワークのセキュリティ |NS-2 |プライベート ネットワークをまとめて接続する |[Azure Cache for Redis は仮想ネットワーク内に存在しなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|データ保護 |DP-4 |転送中の機密情報を暗号化する |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure セキュリティ ベンチマーク v1

[Azure セキュリティ ベンチマーク](/security/benchmark/azure/introduction)には、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 このサービスを完全に Azure セキュリティ ベンチマークにマップする方法については、「[Azure Security Benchmark mapping files](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)」 (Azure セキュリティ ベンチマークのマッピング ファイル) を参照してください。

すべての Azure サービスに対して使用可能な Azure Policy 組み込みを、このコンプライアンス基準に対応させる方法については、[Azure Policy の規制コンプライアンス - Azure セキュリティ ベンチマーク](../../../../articles/governance/policy/samples/azure-security-benchmark.md)に関するページを参照してください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|データ保護 |4.4. |転送中のすべての機密情報を暗号化する |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="canada-federal-pbmm"></a>カナダ連邦の PBMM

すべての Azure サービスで使用可能な Azure Policy 組み込みがこのコンプライアンス標準にどのように対応するのかを確認するには、[Azure Policy の規制コンプライアンス - カナダ連邦 PBMM](../../../../articles/governance/policy/samples/canada-federal-pbmm.md) に関する記事をご覧ください。
このコンプライアンス標準の詳細については、[カナダ連邦 PBMM](https://www.canada.ca/en/government/system/digital-government/digital-government-innovations/cloud-services/government-canada-security-control-profile-cloud-based-it-services.html) に関するドキュメントをご覧ください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|システムと通信の保護 |SC-8(1) |送信の機密性と整合性 \| 暗号化または代替の物理的保護 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC レベル 3

すべての Azure サービスで使用可能な Azure Policy 組み込みがこのコンプライアンス標準にどのように対応するのかを確認するには、[Azure Policy の規制コンプライアンス - CMMC レベル 3](../../../../articles/governance/policy/samples/cmmc-l3.md) に関する記事をご覧ください。
このコンプライアンス標準の詳細については、[サイバーセキュリティ成熟度モデル認定 (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf) に関するドキュメントをご覧ください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|アクセス制御 |AC.1.002 |情報システムへのアクセスを、許可されているユーザーが実行を許可されているトランザクションおよび機能の種類に制限する。 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|システムと通信の保護 |SC.1.175 |組織システムの外部境界と主要な内部境界で、通信 (つまり、組織システムによって送受信される情報) を監視、制御、および保護する。 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|システムと通信の保護 |SC.3.185 |代替の物理的な保護手段によって保護されている場合を除き、送信中に CUI の不正な開示を防ぐための暗号化メカニズムを実装する。 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="fedramp-high"></a>FedRAMP High

すべての Azure サービスに対して使用可能な Azure Policy 組み込みがこのコンプライアンス標準にどのように対応するのかを確認するには、[Azure Policy の規制コンプライアンス - FedRAMP High](../../../../articles/governance/policy/samples/fedramp-high.md) に関するページを参照してください。
このコンプライアンス標準の詳細については、[FedRAMP High](https://www.fedramp.gov/) に関するページを参照してください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|アクセス制御 |AC-4 |情報フローの適用 |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|アクセス制御 |AC-17 |リモート アクセス |[Azure Cache for Redis は仮想ネットワーク内に存在しなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|アクセス制御 |AC-17 |リモート アクセス |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|アクセス制御 |AC-17 (1) |監視または制御の自動化 |[Azure Cache for Redis は仮想ネットワーク内に存在しなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|アクセス制御 |AC-17 (1) |監視または制御の自動化 |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|システムと通信の保護 |SC-7 |境界保護 |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|システムと通信の保護 |SC-7 (3) |アクセス ポイント |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|システムと通信の保護 |SC-8 |送信の機密性と整合性 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|システムと通信の保護 |SC-8 (1) |暗号化または代替の物理的保護 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="fedramp-moderate"></a>FedRAMP Moderate

すべての Azure サービスに対して使用可能な Azure Policy 組み込みがこのコンプライアンス標準にどのように対応するのかを確認するには、[Azure Policy の規制コンプライアンス - FedRAMP Moderate](../../../../articles/governance/policy/samples/fedramp-moderate.md) に関するページを参照してください。
このコンプライアンス標準の詳細については、[FedRAMP Moderate](https://www.fedramp.gov/) に関するページを参照してください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|アクセス制御 |AC-4 |情報フローの適用 |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|アクセス制御 |AC-17 |リモート アクセス |[Azure Cache for Redis は仮想ネットワーク内に存在しなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|アクセス制御 |AC-17 |リモート アクセス |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|アクセス制御 |AC-17 (1) |監視または制御の自動化 |[Azure Cache for Redis は仮想ネットワーク内に存在しなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|アクセス制御 |AC-17 (1) |監視または制御の自動化 |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|システムと通信の保護 |SC-7 |境界保護 |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|システムと通信の保護 |SC-7 (3) |アクセス ポイント |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|システムと通信の保護 |SC-8 |送信の機密性と整合性 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|システムと通信の保護 |SC-8 (1) |暗号化または代替の物理的保護 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

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

## <a name="irs-1075-september-2016"></a>IRS 1075 (2016 年 9 月)

すべての Azure サービスで使用可能な Azure Policy 組み込みがこのコンプライアンス標準にどのように対応するのかを確認するには、[Azure Policy の規制コンプライアンス - IRS 1075 (2016 年 9 月)](../../../../articles/governance/policy/samples/irs-1075-sept2016.md) に関する記事をご覧ください。
このコンプライアンス標準の詳細については、[IRS 1075 (2016 年 9 月)](https://www.irs.gov/pub/irs-pdf/p1075.pdf) に関するドキュメントをご覧ください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|システムと通信の保護 |9.3.16.6 |送信の機密性と整合性 (SC-8) |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="iso-270012013"></a>ISO 27001:2013

すべての Azure サービスで使用可能な Azure Policy 組み込みがこのコンプライアンス標準にどのように対応するのかを確認するには、[Azure Policy の規制コンプライアンス - ISO 27001:2013](../../../../articles/governance/policy/samples/iso-27001.md) に関する記事をご覧ください。
このコンプライアンス標準の詳細については、[ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) に関するドキュメントをご覧ください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|暗号化 |10.1.1 |暗号化コントロールの使用に関するポリシー |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|通信のセキュリティ |13.2.1 |情報転送のポリシーと手順 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="new-zealand-ism-restricted"></a>New Zealand ISM Restricted

すべての Azure サービスで使用可能な Azure Policy 組み込みがこのコンプライアンス標準にどのように対応するのかを確認するには、[Azure Policy の規制コンプライアンス - New Zealand ISM Restricted](../../../../articles/governance/policy/samples/new-zealand-ism.md) に関する記事をご覧ください。
このコンプライアンス標準の詳細については、[New Zealand ISM Restricted](https://www.nzism.gcsb.govt.nz/) に関するページを参照してください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|物理的なセキュリティ |PS-4 |8.3.5 セキュリティで保護されていない領域のネットワーク インフラストラクチャ |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|インフラストラクチャ |INF-9 |10.8.35 セキュリティ アーキテクチャ |[Azure Cache for Redis は仮想ネットワーク内に存在しなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

すべての Azure サービスに対して使用可能な Azure Policy 組み込みをこのコンプライアンス基準に対応させる方法については、[Azure Policy の規制コンプライアンス - NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md) に関するページを参照してください。
このコンプライアンス標準の詳細については、[NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final) に関するページを参照してください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|システムと通信の保護 |3.13.1 |組織システムの外部境界と主要な内部境界で、通信 (つまり、組織システムによって送受信される情報) を監視、制御、および保護する。 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|システムと通信の保護 |3.13.8 |代替の物理的な保護手段によって保護されている場合を除き、送信中に CUI の不正な開示を防ぐための暗号化メカニズムを実装する。 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-53-rev-4"></a>NIST SP 800-53 Rev. 4

すべての Azure サービスに対して使用可能な Azure Policy 組み込みがこのコンプライアンス標準にどのように対応するのかを確認するには、[Azure Policy の規制コンプライアンス - NIST SP 800-53 Rev. 4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md) に関するページを参照してください。
このコンプライアンス標準の詳細については、[NIST SP 800-53 Rev. 4](https://nvd.nist.gov/800-53) に関するページを参照してください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|アクセス制御 |AC-4 |情報フローの適用 |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|アクセス制御 |AC-17 |リモート アクセス |[Azure Cache for Redis は仮想ネットワーク内に存在しなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|アクセス制御 |AC-17 |リモート アクセス |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|アクセス制御 |AC-17 (1) |監視または制御の自動化 |[Azure Cache for Redis は仮想ネットワーク内に存在しなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|アクセス制御 |AC-17 (1) |監視または制御の自動化 |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|システムと通信の保護 |SC-7 |境界保護 |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|システムと通信の保護 |SC-7 (3) |アクセス ポイント |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|システムと通信の保護 |SC-8 |送信の機密性と整合性 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|システムと通信の保護 |SC-8 (1) |暗号化または代替の物理的保護 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-53-rev-5"></a>NIST SP 800-53 Rev. 5

すべての Azure サービスに対して使用可能な Azure Policy 組み込みがこのコンプライアンス標準にどのように対応するのかを確認するには、[Azure Policy の規制コンプライアンス - NIST SP 800-53 Rev. 5](../../../../articles/governance/policy/samples/nist-sp-800-53-r5.md) に関するページを参照してください。
このコンプライアンス標準の詳細については、[NIST SP 800-53 Rev. 5](https://nvd.nist.gov/800-53) に関するページを参照してください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|アクセス制御 |AC-4 |情報フローの適用 |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|アクセス制御 |AC-17 |リモート アクセス |[Azure Cache for Redis は仮想ネットワーク内に存在しなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|アクセス制御 |AC-17 |リモート アクセス |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|アクセス制御 |AC-17 (1) |監視および制御 |[Azure Cache for Redis は仮想ネットワーク内に存在しなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|アクセス制御 |AC-17 (1) |監視および制御 |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|システムと通信の保護 |SC-7 |境界保護 |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|システムと通信の保護 |SC-7 (3) |アクセス ポイント |[Azure Cache for Redis でプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|システムと通信の保護 |SC-8 |送信の機密性と整合性 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|システムと通信の保護 |SC-8 (1) |暗号化による保護 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="uk-official-and-uk-nhs"></a>UK OFFICIAL および UK NHS

すべての Azure サービスで使用可能な Azure Policy 組み込みがこのコンプライアンス標準にどのように対応するのかを確認するには、[Azure Policy の規制コンプライアンス - UK OFFICIAL および UK NHS](../../../../articles/governance/policy/samples/ukofficial-uknhs.md) に関する記事をご覧ください。
このコンプライアンス標準の詳細については、[UK OFFICIAL](https://www.gov.uk/government/publications/government-security-classifications) に関するドキュメントをご覧ください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|転送中のデータの保護 |1 |転送中のデータの保護 |[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

