---
title: Azure Monitor でメトリックを表示する
description: この記事では、Azure portal のグラフと Azure CLI を使用してメトリックを監視する方法を示します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7f8aa7e8cd38de37f5d3757804f7836612ad64cb
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227757"
---
# <a name="monitor-media-services-metrics"></a>Media Services のメトリックを監視する

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

[Azure Monitor](/azure/azure-monitor/overview) により、アプリケーションの実行状況を理解する上で役立つメトリックと診断ログを監視できます。 この機能の詳細な説明と、Azure Media Services のメトリックと診断ログを使用する必要がある理由については、[Media Services のメトリックと診断ログの監視](monitor-media-services-data-reference.md)に関するページを参照してください。

Azure Monitor では、複数の方法を使用してメトリックを操作できます。たとえば、ポータルでメトリックをグラフ化したり、REST API でアクセスしたり、Azure CLI を使ってクエリを実行したりできます。 この記事では、Azure portal のグラフと Azure CLI を使用してメトリックを監視する方法を示します。

## <a name="prerequisites"></a>前提条件

- [Media Services アカウントを作成する](../account-create-how-to.md)
- 「[Media Services のメトリックと診断ログの監視](monitor-media-services-data-reference.md)」を確認する

## <a name="view-metrics-in-azure-portal"></a>Azure portal でメトリックを表示する

1. Azure Portal ( https://portal.azure.com ) にサインインします。
1. Azure Media Services アカウントに移動して、 **[メトリック]** を選択します。
1. **[スコープ]** ボックスをクリックして、監視するリソースを選択します。

    **[スコープの選択]** ウィンドウが右側に表示され、利用可能なリソースの一覧が示されます。 ここでは、以下が表示されます。

    * &lt;Media Services アカウント名&gt;
    * &lt;Media Services アカウント名&gt;/&lt;ストリーミング エンドポイント名&gt;
    * &lt;ストレージ アカウント名&gt;

    フィルター処理してリソースを選択し、 **[適用]** を押します。 サポートされているリソースとメトリックの詳細については、「[Media Services のメトリックを監視する](monitor-media-services-data-reference.md)」を参照してください。

    > [!NOTE]
    > 監視するリソースを切り替えるには、もう一度 **[ソース]** ボックスをクリックして、この手順を繰り返します。

1. 省略可能: グラフに名前を付けます (上部にある鉛筆を押して名前を編集します)。
1. 表示するメトリックを追加します。
1. グラフはダッシュボードにピン留めできます。

## <a name="view-metrics-with-azure-cli"></a>Azure CLI でメトリックを表示する

Azure CLI を使用して "Egress" メトリックを取得するには、次の `az monitor metrics` コマンドを実行します。

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

他のメトリックを取得するには、"Egress" を対象のメトリック名で置き換えてください。

## <a name="see-also"></a>関連項目

- [Azure Monitor のメトリック](/azure/azure-monitor/data-platform)
- [Azure Monitor を使用してメトリック アラートを作成、表示、管理する](/azure/azure-monitor/alerts/alerts-metric)。

## <a name="next-steps"></a>次のステップ

[診断ログ](../media-services-diagnostic-logs-howto.md)
