---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 12/01/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c2385d07ccb81041bd340a8bec0412a8f14cef56
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "96476876"
---
## <a name="azure-security-benchmark"></a>Azure セキュリティ ベンチマーク

[Azure セキュリティ ベンチマーク](../../../../articles/security/benchmarks/overview.md)には、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 このサービスを完全に Azure セキュリティ ベンチマークにマップする方法については、「[Azure Security Benchmark mapping files](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)」 (Azure セキュリティ ベンチマークのマッピング ファイル) を参照してください。

すべての Azure サービスに対して使用可能な Azure Policy 組み込みを、このコンプライアンス基準に対応させる方法については、[Azure Policy の規制コンプライアンス - Azure セキュリティ ベンチマーク](../../../../articles/governance/policy/samples/azure-security-benchmark.md)に関するページを参照してください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|データ保護 |4.8 |機密情報を保存時に暗号化する |[Automation アカウント変数は、暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |

> [!NOTE]
> Automation アカウントの変数を作成するときに、Azure Automation を使用して、セキュリティで保護された資産となるようにその変数の暗号化とストレージを指定できます。 変数を作成した後は、変数を再作成せずにその暗号化の状態を変更することはできません。 まだ暗号化されていない機密データを格納する Automation アカウントの変数がある場合は、それらを削除し、暗号化された変数として再作成する必要があります。 Azure Security Center の推奨事項は、「[Automation アカウント変数は、暗号化する必要がある](../../../../articles/security-center/recommendations-reference.md#recs-computeapp)」で説明されているように、すべての Azure Automation 変数を暗号化することです。 このセキュリティの推奨事項から除外する暗号化されていない変数がある場合は、「[推奨事項とセキュリティ スコアからリソースを除外する](../../../../articles/security-center/exempt-resource.md)」を参照して除外規則を作成してください。