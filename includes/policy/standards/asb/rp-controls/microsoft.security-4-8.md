---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 386109e6a28514c6d57f201bdc6097f4d969cdad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910480"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[コンピューティングとストレージのリソース間で一時ディスク、キャッシュ、データ フローを仮想マシンによって暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |既定では、仮想マシンの OS とデータのディスクは、保存時にプラットフォーム マネージド キーを使用して暗号化されます。 一時ディスク、データ キャッシュ、およびコンピューティングとストレージの間を流れているデータは暗号化されません。 次のような場合は、この推奨事項を無視してください。1. ホストでの暗号化を使用している場合。または 2. マネージド ディスクでのサーバー側暗号化がセキュリティ要件を満たしている場合。 詳細については、「[Azure Disk Storage のサーバー側暗号化](../../../../../articles/virtual-machines/disk-encryption.md)」を参照してください。 また、[さまざまなディスク暗号化オファリング](../../../../../articles/virtual-machines/disk-encryption-overview.md#comparison)についても参照してください。 |AuditIfNotExists、Disabled |[2.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |