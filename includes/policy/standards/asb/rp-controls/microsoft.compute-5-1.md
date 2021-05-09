---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/28/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0cc6e73fc7d0b490b32cb4f3805e6693c9054365
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108212859"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[脆弱性評価ソリューションを仮想マシンで有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |仮想マシンを監査して、サポートされている脆弱性評価ソリューションを実行しているかどうかを検出します。 すべてのサイバーリスクとセキュリティプログラムの中核となるコンポーネントは、脆弱性の特定と分析です。 Azure Security Center の標準価格帯には、追加費用なしで仮想マシン脆弱性スキャンをする機能が含まれています。 また、Security Center では、このツールを自動的にデプロイできます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
