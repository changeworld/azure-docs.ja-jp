---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d27c8ec0fd9300b248f2ae5bab69a8679b306d3f
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123123583"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 シナリオ レベルの監視により、エンド ツー エンドのネットワーク レベル ビューで問題を診断できるようになります。 仮想ネットワークが存在するすべてのリージョンに Network Watcher リソース グループを作成する必要があります。 特定のリージョンで Network Watcher リソース グループを使用できない場合は、アラートが有効になります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
