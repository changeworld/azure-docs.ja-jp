---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: 3608eb6182f7806c0d4df8b96aa69faac68928f1
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73591022"
---
| リソース | 既定の制限 |
| --- | :--- |
| 1 リージョン、1 [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md)あたりのコンテナー グループ | 100<sup>1</sup> |
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
