---
title: Azure Static Web Apps の監視
description: Azure Static Web Apps で要求、エラー、およびトレース情報を監視する
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 4/23/2021
ms.author: cshoe
ms.openlocfilehash: 8c97c3c008dda4269b282e89af7badda889588fe
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110497575"
---
# <a name="monitor-azure-static-web-apps"></a>Azure Static Web Apps の監視

API の要求、エラー、およびトレース情報を監視するには、[Application Insights](../azure-monitor/app/app-insights-overview.md) を有効にします。

> [!IMPORTANT]
> Application Insights には、Azure Static Web Apps からは[独立した価格モデル](https://azure.microsoft.com/pricing/details/monitor)があります。

> [!NOTE]
> Application Insights を Azure Static Web Apps で使用するには、[API](./add-api.md) を使用したアプリケーションが必要です。

## <a name="add-monitoring"></a>監視を追加する

次の手順を使用して、Application Insights 監視を静的 Web アプリに追加します。

1. Azure portal で Static Web Apps インスタンスを開きます。

1. メニューから **[Application Insights]** を選択します。

1. _[Application Insights を有効にする]_ の横の **[はい]** を選択します。

1. **[保存]** を選択します。

:::image type="content" source="media/monitoring/azure-static-web-apps-application-insights-add.png" alt-text="Application Insights を Azure Static Web Apps に追加する":::

> [!NOTE]
> Application Insights インスタンスを作成すると、関連するアプリケーション設定が、サービスをリンクするために使用される Azure Static Web Apps インスタンスに作成されます。

## <a name="access-data"></a>データにアクセスする

1. 静的 Web アプリの _[概要]_ ウィンドウで、 _[リソース グループ]_ の横にあるリンクを選択します。

1. 一覧から、静的 Web アプリと同じ名前のプレフィックスが付いた Application Insights インスタンスを選択します。

以下では、アプリケーションの API エンドポイントの各部を調べるために使用する、ポータル内のいくつかの場所を示しています。

> [!NOTE]
> Application Insights の使用方法の詳細については、「[テレメトリの確認場所](../azure-monitor/app/app-insights-overview.md#where-do-i-see-my-telemetry)」を参照してください。

| Type | メニューの場所 | [説明] |
|--- | --- | --- |
| [エラー](../azure-monitor/app/asp-net-exceptions.md) | _[調査] > [エラー]_ | 失敗した要求を確認します。 |
| [サーバー要求](../azure-monitor/app/tutorial-performance.md) | _[調査] > [パフォーマンス]_ | 個々の API 要求を確認します。  |
| [ログ](../azure-monitor/app/diagnostic-search.md) | _[監視] > [ログ]_ | エディターを操作してトランザクション ログを照会します。 |
| [Metrics](../azure-monitor/essentials/app-insights-metrics.md) | _[監視] > [メトリック]_ | デザイナーを操作して、さまざまなメトリックを使用したカスタム グラフを作成します。 |

### <a name="traces"></a>トレース

次の手順を使用して、アプリケーションのトレースを表示します。

1. _[監視]_ で **[ログ]** を選択します。

1. _[クエリ]_ ウィンドウの任意のカードの上にマウス ポインターを置きます。

1. **[エディターの読み込み]** ボタンを選択します。

1. 生成されたクエリを `traces` という単語に置き換えます。

1. **[実行]** ボタンを選択します。

:::image type="content" source="media/monitoring/azure-static-web-apps-application-insights-traces.png" alt-text="Application Insights トレースの表示":::

## <a name="limit-logging"></a>ログ記録を制限する

場合によっては、エラーと警告の詳細をキャプチャしつつも、ログ記録を制限したい場合があります。その場合は、Azure Functions アプリの _host.json_ ファイルを次のように変更します。

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true
            },
            "enableDependencyTracking": false
        },
        "logLevels": {
            "default": "Warning"
        }
    }
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [認証と承認を設定する](authentication-authorization.md)
