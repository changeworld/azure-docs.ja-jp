---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3dae7c6b26d4745909e107f94a783c28940f1593
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090956"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Stack Edge デバイスは二重暗号化を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |デバイス上の保存データのセキュリティを確保するには、そのデータが二重に暗号化されていること、データへのアクセスが制御されていること、また、デバイスが非アクティブになったときにデータがデータ ディスクから安全な方法で消去されることが必要です。 二重暗号化とは、2 つの暗号化レイヤーを使用することです (データ ボリュームに対する BitLocker XTS-AES 256 ビット暗号化と、ハード ドライブに対する組み込みの暗号化)。 詳細については、特定の Stack Edge デバイスのセキュリティの概要ドキュメントを参照してください。 |audit、deny、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
