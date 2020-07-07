---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/23/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7f4af8b536f44da23e88792c792a46765f6bbd91
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2020
ms.locfileid: "85318705"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Stream Analytics の診断設定をイベント ハブにデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fedf3780c-3d70-40fe-b17e-ab72013dafca) |Stream Analytics の診断設定をデプロイして、この診断設定がない Stream Analytics が作成または更新されたときにリージョンのイベント ハブにストリーミングします。 |DeployIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/StreamAnalytics_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Stream Analytics の診断設定を Log Analytics ワークスペースにデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F237e0f7e-b0e8-4ec4-ad46-8c12cb66d673) |Stream Analytics の診断設定をデプロイして、この診断設定がない Stream Analytics が作成または更新されたときにリージョンの Log Analytics ワークスペースにストリーミングします。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/StreamAnalytics_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Azure Stream Analytics で診断ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |診断ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
