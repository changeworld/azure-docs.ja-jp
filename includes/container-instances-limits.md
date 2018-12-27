---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 44bdaec78e1fad574f29a5945b07041b588aaff8
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572367"
---
| リソース | 既定の制限 |
| --- | :--- |
| [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md)あたりのコンテナー グループ数 | 100<sup>1</sup> |
| コンテナー グループあたりのコンテナーの数 | 60 |
| コンテナー グループあたりのボリュームの数 | 20 |
| IP あたりのポートの数 | 5 |
| 1 時間あたりのコンテナーの作成件数 |300<sup>1</sup> |
| 5 分あたりのコンテナーの作成件数 | 100<sup>1</sup> |
| 1 時間あたりのコンテナーの削除件数 | 300<sup>1</sup> |
| 5 分あたりのコンテナーの削除件数 | 100<sup>1</sup> |
| コンテナー グループあたりの複数のコンテナー | Linux のみ<sup>2</sup> |
| Azure Files ボリューム | Linux のみ<sup>2</sup> |
| GitRepo ボリューム | Linux のみ<sup>2</sup> |
| シークレット ボリューム | Linux のみ<sup>2</sup> |

<sup>1</sup> 制限値の拡大を要求するには、[Azure Support request][azure-support] を作成してください。<br />
<sup>2</sup> Windows によるこの機能のサポートが予定されています。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
