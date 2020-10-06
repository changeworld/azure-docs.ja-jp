---
title: Azure リソース情報の表示とフィルター処理
description: 情報をフィルター処理し、さまざまなビューを使用して、Azure リソースをより深く理解します。
author: mgblythe
ms.service: azure-portal
ms.topic: quickstart
ms.author: mblythe
ms.date: 09/11/2020
ms.openlocfilehash: 21dcadaa929b90e72c7d429229fc6ac4e0a66683
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90605430"
---
# <a name="view-and-filter-azure-resource-information"></a>Azure リソース情報の表示とフィルター処理

Azure portal を使用すると、Azure サブスクリプション全体でリソースに関する詳細情報を参照できます。 この記事では、情報をフィルター処理し、さまざまなビューを使用してリソースをより深く理解する方法について説明します。

この記事では、次のスクリーンショットに示されている **[すべてのリソース]** の画面について重点的に説明します。 仮想マシンなどの個々のリソースの種類の画面には、VM の起動や停止などのさまざまなオプションがあります。

:::image type="content" source="media/manage-filter-resource-views/all-resources.png" alt-text="Azure portal の [すべてのリソース] ビュー":::

## <a name="filter-resources"></a>リソースをフィルター処理する

フィルターを使用してリソースのサブセットに焦点を当てて、 **[すべてのリソース]** の説明を開始します。 次のスクリーンショットは、リソース グループでフィルター処理し、サブスクリプション内の 6 つのリソース グループのうち 2 つを選択した状態を示しています。

:::image type="content" source="media/manage-filter-resource-views/filter-resource-group.png" alt-text="Azure portal の [すべてのリソース] ビュー" が含まれているリソースが対象になります。

:::image type="content" source="media/manage-filter-resource-views/filter-simplewinvm.png" alt-text="Azure portal の [すべてのリソース] ビュー":::

ビューに含める列を変更するには、 **[ビューの管理]** を選択し、 **[列の編集]** をクリックします。

:::image type="content" source="media/manage-filter-resource-views/edit-columns.png" alt-text="Azure portal の [すべてのリソース] ビュー":::

## <a name="save-use-and-delete-views"></a>ビューを保存、使用、削除する

選択したフィルターと列を含むビューを保存できます。 ビューを保存して使用するには、次のようにします。

1. **[ビューの管理]** 、 **[ビューの保存]** の順に選択します。

1. ビューの名前を入力し、 **[OK]** を選択します。 保存されたビューが **[ビューの管理]** メニューに表示されます。

    :::image type="content" source="media/manage-filter-resource-views/simple-view.png" alt-text="Azure portal の [すべてのリソース] ビュー":::

1. ビューを使用するには、 **[既定]** といずれかの独自のビューを切り替えて、表示されるリソースの一覧にどのように影響するかを確認します。

ビューを削除するには、次のようにします。

1. **[ビューの管理]** 、 **[すべてのビューを参照]** の順に選択します。

1. **[すべてのリソース] の保存済みビュー** ペインで、ビューを選択して **[削除]** アイコン ![ビューの削除アイコン](media/manage-filter-resource-views/icon-delete.png) を選択します。

## <a name="summarize-resources-with-visuals"></a>ビジュアルを使用してリソースを集計する

これまでに見たビューは_リスト ビュー_になっていますが、ビジュアルを含む_概要ビュー_もあります。 ビューを一覧表示できるのと同じように、これらのビューを保存して使用できます。 フィルターは 2 種類のビューの間で保持されます。 概要ビューを保存して使用するには、次のようにします。

1. ビュー メニューで **[概要ビュー]** を選択します。

    :::image type="content" source="media/manage-filter-resource-views/menu-summary-view.png" alt-text="Azure portal の [すべてのリソース] ビュー":::

1. [概要] ビューでは**場所**や**種類**などのさまざまな属性で集計できます。 **[集計の方法]** オプションおよび適切なビジュアルを選択します。 次のスクリーンショットは、 **[Bar chart]\(棒グラフ\)** ビジュアルを使用して **[種類の概要]** を示しています。

    :::image type="content" source="media/manage-filter-resource-views/type-summary-bar-chart.png" alt-text="Azure portal の [すべてのリソース] ビュー":::

1. **[ビューの管理]** 、 **[保存]** の順に選択して、リスト ビューで行ったようにこのビューを保存します。

1. [概要] ビューの **[種類の概要]** で、グラフ内の棒を 1 つ選択します。 棒を選択すると、1 種類のリソースにフィルター処理された一覧が表示されます。

    :::image type="content" source="media/manage-filter-resource-views/all-resources-filtered-type.png" alt-text="Azure portal の [すべてのリソース] ビュー":::

## <a name="run-queries-in-azure-resource-graph"></a>Azure Resource Graph でクエリを実行する

Azure Resource Graph は、一連のサブスクリプションにわたって大規模にクエリを実行する機能を備えた、効率的でパフォーマンスに優れたリソース探索を実現します。 Azure portal の **[すべてのリソース]** 画面には、現在フィルターされたビューを対象とした Resource Graph クエリを開くためのリンクが含まれています。

Resource Graph クエリを実行するには、次のようにします。

1. **[クエリを開く]** を選択します。

    :::image type="content" source="media/manage-filter-resource-views/open-query.png" alt-text="Azure portal の [すべてのリソース] ビュー":::

1. **Azure Resource Graph エクスプローラー**で、 **[クエリの実行]** を選択して結果を確認します。

    :::image type="content" source="media/manage-filter-resource-views/run-query.png" alt-text="Azure portal の [すべてのリソース] ビュー":::

    詳細については、「[Azure Resource Graph エクスプローラーを使用して初めての Resource Graph クエリを実行する](../governance/resource-graph/first-query-portal.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure Portal の概要](azure-portal-overview.md)

[Azure portal でのダッシュボードの作成と共有](azure-portal-dashboards.md)
