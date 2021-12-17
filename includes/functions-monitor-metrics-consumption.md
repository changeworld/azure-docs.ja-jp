---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/04/2021
ms.author: glenga
ms.openlocfilehash: 34237e64aecfcd4059a9bc9fb3ae390281b3419a
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2021
ms.locfileid: "113567334"
---
#### <a name="portal"></a>[ポータル](#tab/portal)

従量課金プランの関数アプリのコスト関連データをグラフィック形式で表示するには、[Azure Monitor メトリックス エクスプローラー](../articles/azure-monitor/essentials/metrics-getting-started.md)を使用します。 

1. [Azure portal] で関数アプリに移動します。

1. 左側のパネルで、 **[監視]** までスクロールし、 **[メトリック]** を選択します。  

1. **[メトリック]** から **[関数の実行回数]** を選択し、 **[集計]** から **[合計]** を選択します。 これにより、選択した期間の実行回数の合計がグラフに追加されます。

    ![グラフに追加する関数アプリのメトリックを定義する](./media/functions-monitor-metrics-consumption/monitor-metrics-add-metric.png)

1. **[メトリックの追加]** を選択し、手順 2 から手順 4 を繰り返して、 **[関数の実行単位]** をグラフに追加します。 

結果のグラフには、選択した時間範囲 (この例では 2 時間) に対する両方の実行メトリックの合計が含まれます。

![関数の実行回数と実行単位のグラフ](./media/functions-monitor-metrics-consumption/monitor-metrics-execution-sum.png)

実行単位の数は実行回数よりはるかに多いため、グラフは実行単位だけのように見えます。

このグラフでは、MB ミリ秒数で測定して、2 時間に合計で 11.1 億の `Function Execution Units` が消費されたことが示されています。 GB 秒数に変換するには、1,024,000 で割ります。 この例の関数アプリでは、`1110000000 / 1024000 = 1083.98` GB 秒が消費されました。 この値を取得し、[Functions の価格ページ](https://azure.microsoft.com/pricing/details/functions/)で示されている実行時間の現在の料金を乗算することにより、この 2 時間のコストがわかります (実行時間の無料提供を既に使用してあるものとします)。 

#### <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

[Azure CLI](/cli/azure/) には、メトリックを取得するためのコマンドがあります。 ローカル コマンド環境から、または [Azure Cloud Shell](../articles/cloud-shell/overview.md) を使用してポータルから直接、CLI を使用できます。 たとえば、次の [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) コマンドでは、前に使用したのと同じ期間に対する 1 時間あたりのデータが返されます。

`<AZURE_SUBSCRIPTON_ID>` は、コマンドを実行している Azure サブスクリプション ID に置き換えてください。

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

このコマンドでは、次の例のような JSON ペイロードが返されます。

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
この特定の応答では、`2019-09-11T21:46` から `2019-09-11T23:18` までの間に、アプリで 1,110,000,000 MB ミリ秒 (1083.98 GB 秒) が消費されたことが示されています。

---

[Azure Portal]: https://portal.azure.com