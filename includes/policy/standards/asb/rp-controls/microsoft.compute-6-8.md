---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/20/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7dcb488e28bdbb2e3fea113ee834e73a69144902
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122636200"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |アプリケーション制御を有効にして、マシンで実行されている既知の安全なアプリケーションの一覧を定義し、他のアプリケーションの実行時にアラートを出します。 これは、マルウェアに対してマシンを強化するのに役立ちます。 ルールの構成と保守のプロセスを簡略化するために、Security Center で機械学習を使用して各マシンで実行されているアプリケーションを分析し、既知の安全なアプリケーションの一覧を提示します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
