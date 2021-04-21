---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 19c5d2ef3845d4939aa28d07bee4506422916262
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499319"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[分散トレースが有効になっていない Azure Spring Cloud インスタンスを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |Azure Spring Cloud の分散トレース ツールを使用すると、アプリケーション内のマイクロサービス間の複雑な相互接続のデバッグと監視が可能になります。 分散トレース ツールを有効にして、正常な状態にしてください。 |Audit、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Azure Spring Cloud でネットワークの挿入を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Azure Spring Cloud インスタンスでは、次の目的で仮想ネットワークの挿入を使用する必要があります。1. Azure Spring Cloud をインターネットから分離する。 2. オンプレミスのデータ センター内のシステム、または他の仮想ネットワーク内の Azure サービスとの Azure Spring Cloud のやりとりを有効化する。 3. Azure Spring Cloud の送受信ネットワーク通信を制御するための権限を顧客に付与する。 |Audit, Disabled, Deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
