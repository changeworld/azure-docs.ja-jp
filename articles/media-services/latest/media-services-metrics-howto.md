---
title: Azure Monitor でメトリックを表示する
description: この記事では、Azure portal のグラフと Azure CLI を使用してメトリックを監視する方法を示します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: c230e1e950bb924631032940642a6202acf4ade8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382938"
---
# <a name="monitor-media-services-metrics"></a>Media Services のメトリックを監視する

[Azure Monitor](../../azure-monitor/overview.md) により、アプリケーションの実行状況を理解する上で役立つメトリックと診断ログを監視できます。 この機能の詳細な説明と、Azure Media Services のメトリックと診断ログを使用する必要がある理由については、「[Media Services のメトリックと診断ログの監視](media-services-metrics-diagnostic-logs.md)」を参照してください。

Azure Monitor では、複数の方法を使用してメトリックを操作できます。たとえば、ポータルでメトリックをグラフ化したり、REST API でアクセスしたり、Azure CLI を使ってクエリを実行したりできます。 この記事では、Azure portal のグラフと Azure CLI を使用してメトリックを監視する方法を示します。

## <a name="prerequisites"></a>前提条件

- [Media Services アカウントを作成する](create-account-cli-how-to.md)
- 「[Media Services のメトリックと診断ログの監視](media-services-metrics-diagnostic-logs.md)」を確認する

## <a name="view-metrics-in-azure-portal"></a>Azure portal でメトリックを表示する

1. Azure Portal ( https://portal.azure.com ) にサインインします。
1. Azure Media Services アカウントに移動して、 **[メトリック]** を選択します。
1. **[リソース]** ボックスをクリックして、メトリックを監視するリソースを選択します。

    右側の **[リソースの選択]** ウィンドウに、利用可能なリソースの一覧が表示されます。 ここでは、以下が表示されます。

    * &lt;Media Services アカウント名&gt;
    * &lt;Media Services アカウント名&gt;/&lt;ストリーミング エンドポイント名&gt;
    * &lt;ストレージ アカウント名&gt;

    リソースを選択し、 **[適用]** を押します。 サポートされているリソースとメトリックの詳細については、「[Media Services のメトリックを監視する](media-services-metrics-diagnostic-logs.md)」を参照してください。

    ![メトリック](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > メトリックを監視するリソースを切り替えるには、もう一度 **[リソース]** ボックスをクリックして、この手順を繰り返します。
1. (省略可能) グラフに名前を付けます (上部にある鉛筆を押して名前を編集します)。
1. 表示するメトリックを追加します。

    ![メトリック](media/media-services-metrics/metrics03.png)
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

* [Azure Monitor のメトリック](../../azure-monitor/platform/data-platform.md)
* [Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../../azure-monitor/platform/alerts-metric.md)。

## <a name="next-steps"></a>次のステップ

[診断ログ](media-services-diagnostic-logs-howto.md)
