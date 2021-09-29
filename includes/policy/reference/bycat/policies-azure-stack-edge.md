---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8d7c78a30aa865ef1a2c0aecbb5a47b735d25112
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655101"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Stack Edge デバイスは二重暗号化を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |デバイス上の保存データのセキュリティを確保するには、そのデータが二重に暗号化されていること、データへのアクセスが制御されていること、また、デバイスが非アクティブになったときにデータがデータ ディスクから安全な方法で消去されることが必要です。 二重暗号化とは、2 つの暗号化レイヤーを使用することです (データ ボリュームに対する BitLocker XTS-AES 256 ビット暗号化と、ハード ドライブに対する組み込みの暗号化)。 詳細については、特定の Stack Edge デバイスのセキュリティの概要ドキュメントを参照してください。 |audit、deny、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
