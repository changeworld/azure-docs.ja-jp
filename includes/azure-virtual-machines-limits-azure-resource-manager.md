---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: cd3ff3fce80e66d7cd61636b4416cb2fc28f5e77
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2021
ms.locfileid: "97956484"
---
| リソース | 制限 |
| --- | --- |
| [サブスクリプション](https://azure.microsoft.com/pricing/) |リージョンあたり 25,000<sup>1</sup>。 |
| [サブスクリプション](https://azure.microsoft.com/pricing/)あたりの VM の合計コア数 |リージョンあたり 20<sup>1</sup>。 制限を引き上げるには、サポートにお問い合わせください。 |
| [サブスクリプション](https://azure.microsoft.com/pricing/)あたりの Azure Spot VM の合計コア数 |リージョンあたり 20<sup>1</sup>。 制限を引き上げるには、サポートにお問い合わせください。 |
| [サブスクリプション](https://azure.microsoft.com/pricing/)あたりのシリーズ (Dv2 や F など) ごとの VM のコア数 |リージョンあたり 20<sup>1</sup>。 制限を引き上げるには、サポートにお問い合わせください。 |
| サブスクリプションあたりの[可用性セット](../articles/virtual-machines/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)数 |リージョンあたり 2,500。 |
| 可用性セットあたりの仮想マシン数 | 200 |
| [リソース グループ](../articles/azure-resource-manager/management/overview.md#resource-groups)あたりの[近接配置グループ数](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups-portal) | 800 | 
| 可用性セットあたりの証明書数 | 199<sup>2</sup> |
| サブスクリプションあたりの証明書数 |無制限<sup>3</sup> |

<sup>1</sup> 既定の制限は、プラン カテゴリの種類 (無料試用版や従量課金制など) とシリーズ (Dv2、F、G など) によって異なります。たとえば、マイクロソフトエンタープライズ契約サブスクリプションの既定値は 350 です。

<sup>2</sup> SSH 公開キーなどのプロパティは証明書としてプッシュされ、この値を上限として加算されます。 この制限をバイパスするには、[Windows 用の Azure Key Vault 拡張機能](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows)または [Linux 用の Azure Key Vault 拡張機能](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-linux)を使用して証明書をインストールしてください。

<sup>3</sup> Azure Resource Manager では、証明書は Azure Key Vault に格納されます。 証明書の数は、サブスクリプションに対しては無制限です。 1 つの VM または可用性セットから成るデプロイあたりの証明書数には 1 MB という制限があります。



> [!NOTE]
> 仮想マシンのコア数には、リージョン別総数の制限があります。 さらに、リージョンごとにサイズ シリーズ (Dv2 や F など) あたりの制限もあります。これらの制限は別々に適用されます。 たとえば、米国東部で VM のコア上限が 30、A シリーズのコア上限が 30、D シリーズのコア上限が 30 のサブスクリプションがあるとします。 このサブスクリプションでは、30 個の A1 VM、または 30 台の D1 VM、または合計 30 個のコア数を超えないようにこれらの 2 つを組み合わせでデプロイすることができます。 組み合わせの例としては、10 個の A1 VM と 20 個の D1 VM が考えられます。  
> <!-- -->
>
