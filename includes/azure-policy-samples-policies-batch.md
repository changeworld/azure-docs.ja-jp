---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 59a47ca7d3b3b42a5822e61c37ee90cb238c0778
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170050"
---
|Name |説明 |効果 |Version |
|---|---|---|---|
|[Batch アカウントで診断ログを有効にする必要がある](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |診断ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |2.0.0 |
|[Batch アカウントでメトリック アラート ルールを構成する必要がある](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Batch/Batch_AuditMetricAlerts_Audit.json) |必須メトリックを有効にするための Batch アカウントにおけるメトリック アラート ルールの構成を監査します |AuditIfNotExists、Disabled |1.0.0 |
