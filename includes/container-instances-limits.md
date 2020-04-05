---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: 33a82a55b03cde79d2d80826041ee6d43565476a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334812"
---
| リソース | 制限 |
| --- | :--- |
| 1 リージョン、1 [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md)あたりの Standard SKU コンテナー グループ | 100<sup>1</sup> |
| 1 リージョン、1 [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md)あたりの Dedicated SKU コンテナー グループ | 0<sup>1</sup> |
| コンテナー グループあたりのコンテナーの数 | 60 |
| コンテナー グループあたりのボリュームの数 | 20 |
| IP あたりのポートの数 | 5 |
| コンテナー インスタンスのログ サイズ - 実行中のインスタンス | 4 MB |
| コンテナー インスタンスのログ サイズ - 停止したインスタンス | 16 KB または 1,000 行 |
| 1 時間あたりのコンテナーの作成件数 |300<sup>1</sup> |
| 5 分あたりのコンテナーの作成件数 | 100<sup>1</sup> |
| 1 時間あたりのコンテナーの削除件数 | 300<sup>1</sup> |
| 5 分あたりのコンテナーの削除件数 | 100<sup>1</sup> |


<sup>1</sup>制限値の引き上げを要求するには、[Azure サポート リクエスト][azure-support]を作成してください。<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
