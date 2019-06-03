---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 9070aee55969c1cc0fdf3870a05a065aaa5a8bf3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238334"
---
| Resource | 既定の制限 |
| --- | --- |
| 可用性セットあたりの仮想マシン数 | 200 |
| サブスクリプションあたりの証明書数 |無制限<sup>1</sup> |

<sup>1</sup>Azure リソース マネージャーでは、証明書は Azure Key Vault に格納されます。 証明書の数は、サブスクリプションに対しては無制限です。 1 つの VM または可用性セットから成るデプロイあたりの証明書数には 1 MB という制限があります。

