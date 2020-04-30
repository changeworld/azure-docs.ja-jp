---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/26/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8de55ced9d75573c49d4cc069c2c3db24e7932d3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82190989"
---
|名前 |説明 |効果 |Version |GitHub |
|---|---|---|---|---|
|[コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |カスタマー マネージド キー (CMK) を使用して暗号化が有効になっていないコンテナー レジストリを監査します。 CMK 暗号化の詳細については、[https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) を参照してください。 |Audit、Disabled |1.0.0-preview |[リンク](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[コンテナー レジストリでは無制限のネットワーク アクセスを許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |ネットワーク (IP または VNET) ルールが構成されておらず、既定ですべてのネットワーク アクセスを許可するコンテナー レジストリを監査します。 少なくとも 1 つの IP ルールやファイアウォール ルールまたは構成済みの仮想ネットワークを含むコンテナー レジストリは、準拠していると見なされます。 コンテナー レジストリのネットワーク ルールについては、[https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) を参照してください。 |Audit、Disabled |1.0.0-preview |[リンク](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
