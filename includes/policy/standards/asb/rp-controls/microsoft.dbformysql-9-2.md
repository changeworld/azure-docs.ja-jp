---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e2e12cdd06a21d1838ad939a2cebb908bf0452a4
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511675"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for MySQL の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
