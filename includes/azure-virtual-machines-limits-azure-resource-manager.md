---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: a9c830025b9ce6c93bf106a8348b0f9dc6521582
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560022"
---
| リソース | 制限 |
| --- | --- |
| [サブスクリプション](https://azure.microsoft.com/pricing/) |リージョンあたり 25,000<sup>1</sup>。 |
| [サブスクリプション](https://azure.microsoft.com/pricing/)あたりの VM の合計コア数 |リージョンあたり 20<sup>1</sup>。 制限を引き上げるには、サポートにお問い合わせください。 |
| [サブスクリプション](https://azure.microsoft.com/pricing/)あたりの Azure Spot VM の合計コア数 |リージョンあたり 20<sup>1</sup>。 制限を引き上げるには、サポートにお問い合わせください。 |
| [サブスクリプション](https://azure.microsoft.com/pricing/)あたりのシリーズ (Dv2 や F など) ごとの VM のコア数 |リージョンあたり 20<sup>1</sup>。 制限を引き上げるには、サポートにお問い合わせください。 |
| サブスクリプションあたりの[可用性セット](../articles/virtual-machines/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)数 |リージョンあたり 2,500。 |
| 可用性セットあたりの仮想マシン数 | 200 |
| サブスクリプションあたりの証明書数 |無制限<sup>2</sup> |

<sup>1</sup>既定の制限は、オファー カテゴリの種類 (無料試用版や従量課金制など) とシリーズ (Dv2、F、G など) によって異なります。たとえば、Enterprise Agreement サブスクリプションの既定値は 350 です。

<sup>2</sup>Azure Resource Manager では、証明書は Azure Key Vault に格納されます。 証明書の数は、サブスクリプションに対しては無制限です。 1 つの VM または可用性セットから成るデプロイあたりの証明書数には 1 MB という制限があります。

> [!NOTE]
> 仮想マシンのコア数には、リージョン別総数の制限があります。 さらに、リージョンごとにサイズ シリーズ (Dv2 や F など) あたりの制限もあります。これらの制限は別々に適用されます。 たとえば、米国東部で VM のコア上限が 30、A シリーズのコア上限が 30、D シリーズのコア上限が 30 のサブスクリプションがあるとします。 このサブスクリプションでは、30 個の A1 VM、または 30 台の D1 VM、または合計 30 個のコア数を超えないようにこれらの 2 つを組み合わせでデプロイすることができます。 組み合わせの例としては、10 個の A1 VM と 20 個の D1 VM が考えられます。  
> <!-- -->
>