---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 53494bc9642a3df91492d2353a6aa3c6875c1fff
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170130"
---
|Name |説明 |効果 |Version |
|---|---|---|---|
|[Key Vault の診断設定をイベント ハブにデプロイする](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_DiagnosticLog_Deploy.json) |この診断設定がないキー コンテナーが作成または更新されたときに、Key Vault の診断設定をデプロイして、リージョンのイベント ハブにストリーム配信します。 |deployIfNotExists |1.0.0 |
|[Key Vault で診断ログを有効にする必要がある](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |診断ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |1.0.0 |
|[キー コンテナー オブジェクトが回復可能でなければならない](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |このポリシーは、キー コンテナー オブジェクトが回復可能でないかどうかを監査します。 論理的な削除機能は、DELETE 操作の後でもオブジェクトが削除されているように見せながら特定の保持期間 (90 日間) の間リソースを実際に保持するのに役立ちます。 "消去保護" が有効な場合、削除状態のコンテナーまたはオブジェクトは、90 日間の保持期間が経過するまで消去できません。 これらのコンテナーとオブジェクトはまだ回復可能で、お客様のアイテム保持ポリシーに確実に従うことができます。 |Audit、Disabled |1.0.0 |
