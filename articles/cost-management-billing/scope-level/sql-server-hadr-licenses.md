---
title: SQL Server HADR とスコープレベルの Azure ハイブリッド特典の共存
description: この記事では、SQL Server HADR のソフトウェア アシュアランスの特典とスコープレベルの Azure ハイブリッド特典の共存について説明します。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 97c338134430156fefa5d874d44e27c8dc34a496
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547295"
---
# <a name="sql-server-hadr-and-scope-level-azure-hybrid-benefit-coexistence"></a>SQL Server HADR とスコープレベルの Azure ハイブリッド特典の共存

ソフトウェア アシュアランス (SA) の利点の 1 つは、Azure のお客様がフェールオーバー イベントを想定して、ディザスター リカバリーのためにパッシブな SQL Server インスタンスをインストールして実行できるという点です。 Azure ハイブリッド特典のスコープレベルの管理は、割り当て済みの SQL Server ライセンスが適格なディザスター リカバリー レプリカによって消費されないようにすることで、SQL Server HADR の特典をサポートします。 そのため、ユーザーがレプリカを個別に管理する必要がなくなります。 

## <a name="hadr-benefit-selection"></a>HADR の特典の選択

HADR の特典を利用するには、次の図に示すように、**SQL 仮想マシン** リソースの **[構成]** ペインで **[HADR]** を選択します。

:::image type="content" source="./media/sql-server-hadr-licenses/select-hadr-benefit.png" alt-text="SQL 仮想マシン構成の [HADR] オプションを示すスクリーンショット。" lightbox="./media/sql-server-hadr-licenses/select-hadr-benefit.png" :::

HADR プロパティが選択されている場合、スコープに集中的に割り当てられたライセンスは、そのリソースの SQL ソフトウェアにかかるコストの割引には使用されません。 代わりに、従量課金制メーターが自動的に $0 の HADR メーターに切り替わります。 この方法により、割り当て済みの SQL ライセンスは、ライセンスを必要とするリソースにのみ使用されます。 HADR の特典を考慮して、SQL ライセンスの割り当てを増やす必要はありません。 次の図にこの概念を示します。

以下の画像で示されている価格は、例示のみを目的としたものです。

:::image type="content" source="./media/sql-server-hadr-licenses/fully-discounted-consumption-hadr.svg" alt-text="HADR を選択した場合の割引が完全に適用された仮想コアの消費量を示す図。" border="false" lightbox="./media/sql-server-hadr-licenses/fully-discounted-consumption-hadr.svg":::

> [!NOTE]
> HADR オプションには、AlwaysOn 可用性グループにおけるこの SQL Server インスタンスの特定のロールが反映されています。 これを選択することは、サービス オーナーまたは DBA の担当で、少なくとも [SQL Server 共同作成者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)のロールが必要です。 このタスクは、スコープレベルのライセンス割り当てとは無関係です。

## <a name="next-steps"></a>次のステップ

- 「[スコープ レベルの Azure ハイブリッド特典の管理に関する FAQ](faq-azure-hybrid-benefit-scope.yml)」を確認します。
- 割引がどのように適用されるかについては、[Azure ハイブリッド特典のスコープレベルの管理](sql-server-hadr-licenses.md)に関するページを参照してください。
- [既存の Azure ハイブリッド特典エクスペリエンスから移行する](transition-existing.md)方法について確認します。