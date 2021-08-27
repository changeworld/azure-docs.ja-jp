---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/20/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 035d40766a302c2a82fef6424b3e40865eb1cabf
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122635705"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[自分のサブスクリプションで Log Analytics エージェントの自動プロビジョニングを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシンからデータを収集します。 データは、以前は Microsoft Monitoring Agent (MMA) と呼ばれていた Log Analytics エージェントによって収集されます。これがセキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータを Log Analytics ワークスペースにコピーします。 自動プロビジョニングを有効にして、サポートされているすべての Azure VM と新しく作成された VM にこのエージェントを自動的にデプロイすることをお勧めします。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
