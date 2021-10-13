---
author: georgewallace
ms.service: azure-policy
ms.topic: include
ms.date: 10/11/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: bfd87400d7b0294f28ed168495e4285f05da317a
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129809883"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Stack Edge デバイスは二重暗号化を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |デバイス上の保存データのセキュリティを確保するには、そのデータが二重に暗号化されていること、データへのアクセスが制御されていること、また、デバイスが非アクティブになったときにデータがデータ ディスクから安全な方法で消去されることが必要です。 二重暗号化とは、2 つの暗号化レイヤーを使用することです (データ ボリュームに対する BitLocker XTS-AES 256 ビット暗号化と、ハード ドライブに対する組み込みの暗号化)。 詳細については、特定の Stack Edge デバイスのセキュリティの概要ドキュメントを参照してください。 |audit、deny、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
