---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: eec2c17a82bc86323da3d2d70c0c6de8ec113a9e
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316768"
---
## <a name="azure-security-benchmark"></a>Azure セキュリティ ベンチマーク

[Azure セキュリティ ベンチマーク](../../../../articles/security/benchmarks/overview.md)には、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 このサービスを完全に Azure セキュリティ ベンチマークにマップする方法については、「[Azure Security Benchmark mapping files](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)」 (Azure セキュリティ ベンチマークのマッピング ファイル) を参照してください。

すべての Azure サービスに対して使用可能な Azure Policy 組み込みを、このコンプライアンス基準に対応させる方法については、[Azure Policy の規制コンプライアンス - Azure セキュリティ ベンチマーク](../../../../articles/governance/policy/samples/azure-security-benchmark.md)に関するページを参照してください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|ログ記録と監視 |2.3 |Azure リソースの監査ログ記録を有効にする |[IoT Hub の診断ログを有効にする必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

すべての Azure サービスに対して使用可能な Azure Policy 組み込みがこのコンプライアンス標準にどのように対応するのかを確認するには、[Azure Policy の規制コンプライアンス - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md) に関するページを参照してください。
このコンプライアンス標準の詳細については、[HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html) に関するページを参照してください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|監査ログ |1204.09aa1System.3 - 09.aa |特権ユーザー (管理者、オペレーターなど) のアクティビティには、イベントの成功または失敗、イベント発生日時、関連するアカウント、関連するプロセス、およびイベントに関する追加情報が含まれます。 |[IoT Hub の診断ログを有効にする必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

