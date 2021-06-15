---
title: Oracle ワークロード向けの BareMetal SKU
description: Oracle BareMetal インフラストラクチャ ワークロード用の SKU について説明します。
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 04/15/2021
ms.openlocfilehash: a578de920654aa7b3a298ed92a67efbe2bc2071f
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578536"
---
# <a name="baremetal-skus-for-oracle-workloads"></a>Oracle ワークロード向けの BareMetal SKU

この記事では、Oracle ワークロード向けの特殊な BareMetal インフラストラクチャ SKU について説明します。

Oracle 向けの BareMetal インフラストラクチャの SKU は、2 ソケットから最大 4 ソケットまでの範囲です。 ワークロードの要件を満たすために、さまざまな CPU コアとメモリ サイズから選択することもできます。 使用可能な SKU の機能をまとめた表を次に示します。
 
| **Oracle 認定**  **ハードウェア** | **Model** | **メモリの合計** | **Storage** | **可用性** |
| --- | --- | --- | --- | --- |
| YES | SAP HANA on Azure S32m- 2 x Intel® Xeon® I6234 プロセッサ 16 CPU コア、32 CPU スレッド | 1.5 TB | --- | 利用可能 |
| YES | SAP HANA on Azure S64m- 4 x Intel® Xeon® I6234 プロセッサ 32 CPU コア、64 CPU スレッド | 3.0 TB | --- | 利用可能 |
| YES | SAP HANA on Azure S96– 2 x Intel® Xeon® E7-8890 v4 プロセッサ 48 CPU コア、96 CPU スレッド | 768 GB | 3.0 TB | 利用可能 |
| YES | SAP HANA on Azure S224 – 4 x Intel® Xeon® Platinum 8276 プロセッサ 112 CPU コア、224 CPU スレッド | 3.0 TB | 6.3 TB | 利用可能 |
| YES | SAP HANA on Azure S224m– 4 x Intel® Xeon® Platinum 8276 プロセッサ 112 CPU コア、224 CPU スレッド | 6.0 TB | 10.5 TB | 利用可能 |

- CPU コア = サーバー ユニットの非ハイパースレッド CPU コアの合計 (物理プロセッサの合計数)。 
- CPU スレッド = サーバー ユニットのハイパースレッド CPU コアによって提供されるコンピューティング スレッドの合計 (論理プロセッサの合計)。 ほとんどのユニットは、ハイパースレッディング テクノロジを使用するように既定で構成されます。
- サーバーはお客様専用です。
- お客様はルート アクセス権を持っています (ハイパーバイザーなし)。
- サーバーは Azure VNET 上に直接あるわけではありません。

## <a name="next-steps"></a>次のステップ

Oracle 向け BareMetal インフラストラクチャによって提供されるストレージについて学びます。

> [!div class="nextstepaction"]
> [Oracle ワークロード用の BareMetal のストレージ](oracle-baremetal-storage.md)
