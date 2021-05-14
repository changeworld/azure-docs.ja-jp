---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: acde4db609dedc9f2ccce409102a152e5d0ca425
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108793157"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[キー コンテナーで消去保護が有効になっている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |悪意でキー コンテナーが削除されると、データが完全に失われる可能性があります。 組織内の悪意のある内部関係者が、キー コンテナーの削除と消去を実行できるおそれがあります。 消去保護では、論理的に削除されたキー コンテナーに必須の保有期間を適用することによって、内部関係者の攻撃から組織を保護します。 組織や Microsoft の内部にいるどのユーザーも、論理的な削除の保有期間中にキー コンテナーを消去することはできなくなります。 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
