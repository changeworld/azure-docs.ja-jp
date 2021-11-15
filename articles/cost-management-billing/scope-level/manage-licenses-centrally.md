---
title: 一元割り当てする SQL ライセンスが Azure で 1 時間ごとにリソースの使用に適用される仕組み
description: この記事では、Azure ハイブリッド特典で一元割り当てする SQL ライセンスが、Azure で 1 時間ごとにリソースの使用に適用される仕組みを、詳しく説明します。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 53f8538e6e34c91023bc55e7d47e5f28e9fb3e21
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130243849"
---
# <a name="how-azure-applies-centrally-assigned-sql-licenses-to-hourly-usage"></a>一元割り当てする SQL ライセンスが Azure で 1 時間ごとにリソースの使用に適用される仕組み

この記事では、SQL Server の スコープレベル Azure ハイブリッド特典一元管理について詳しく説明します。 このプロセスは、管理者がサブスクリプションまたは課金アカウントのスコープにライセンスを割り当てることから始まります。

適切な定価または従量課金制のメーターが使用され、各リソースから 1 時間に 1 回使用状況が報告されます。 Azure 内部では、利用状況アプリケーション エンジンによって使用可能な NCL が評価され、その時間に適用されます。 ある時間の仮想コア リソースの消費に対して、選択されたスコープに十分な未使用の NCL がある場合、従量課金制のメーターは、価格がゼロ ($0) の対応する Azure ハイブリッド特典のメーターに切り替わります。

## <a name="license-discount"></a>ライセンス割引 

次の図は、その時間のすべての SQL リソースによる仮想コア全体の消費量を割り引くのに十分な未使用の NCL がある場合の割引プロセスを示しています。

以下の画像で示されている価格は、例示だけを目的としたものです。

:::image type="content" source="./media/manage-licenses-centrally/fully-discounted-consumption.svg" alt-text="完全に割引された仮想コアの消費量を示す図。" border="false" lightbox="./media/manage-licenses-centrally/fully-discounted-consumption.svg":::


スコープ内の SQL リソースによる仮想コアの消費量が未使用の NCL 数を超えた場合、超過した仮想コアの消費量は、適切な従量課金制メーターを使用して請求されます。 次の図は、仮想コアの消費量が未使用の NCL 数を超えた場合の割引プロセスを示しています。

以下の画像で示されている価格は、例示だけを目的としたものです。

:::image type="content" source="./media/manage-licenses-centrally/partially-discounted-consumption.svg" alt-text="部分的に割引された消費量を示す図。" border="false" lightbox="./media/manage-licenses-centrally/partially-discounted-consumption.svg":::

割り当てられた Core ライセンスがカバーする Azure SQL リソースは、時間ごとに変動する可能性があります。 この変動は、どのリソースが実行され、自動化されたシステムによってその使用量がどのような順序で処理されるかによって変わります。 ただし、システムにより、選択されたスコープ内で割り当てられた SQL ライセンスが最大限に使用されます。 使用状況は、Cost Management を使用して監視できます。 詳細については、[割り当てられたライセンスの使用状況を追跡する方法](create-sql-license-assignments.md#track-assigned-license-use)に関するセクションを参照してください。

次の図は、割り当てられた SQL Server ライセンスが時間の経過と共にどのように適用され、Azure ハイブリッド特典の最大割引が達成されるかを示しています。

:::image type="content" source="./media/manage-licenses-centrally/ncl-utilization-over-time.png" alt-text="時間経過に伴う NCL の使用状況を示す図。" border="false" lightbox="./media/manage-licenses-centrally/ncl-utilization-over-time.png":::

## <a name="next-steps"></a>次のステップ

- [Azure ハイブリッド特典一元管理の FAQ](faq-azure-hybrid-benefit-scope.yml) を確認する。
- [既存の Azure ハイブリッド特典エクスペリエンスから移行する](transition-existing.md)方法について確認します。