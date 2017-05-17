---
title: "Azure Application Insights におけるカスタム ライブ メトリックと診断 | Microsoft Docs"
description: "カスタム メトリックを使用して Web アプリをリアルタイムで監視し、エラー、トレース、イベントのライブ フィードを使用して問題を診断します。"
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 86e01cf6cb14334e85da4102610fa7feb66cb543
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="custom-live-metrics-and-events-monitor--diagnose-with-1-second-latency"></a>カスタム ライブ メトリックとイベント： 1 秒の待機時間での監視と診断 
Live Metrics Stream には、[Application Insights](app-insights-overview.md) のメトリックとイベントが、1 秒というほぼリアルタイムの待機時間で表示されます。 この即時監視により、検出と診断の平均時間が短縮され、SLA を満たすことができます。 そのための方法は次のとおりです。
* カスタム ライブ KPI の監視: 既存の Application Insights Telemetry のフィルター、スライス、ダイスを試みて、最も関連性の高い KPI をポータルから瞬時に取得できます。 コードまたは構成の変更やデプロイは不要です。 送信する可能性のある任意のカスタム メトリックや測定も使用できます。
* ライブ検出と診断: 発生した要求エラーや依存関係エラーを詳細な例外トレースと共に表示できます。 既知の問題を除外して、実際の問題や新たな問題に焦点を合わせることができます。
* ライブ デバッグ: 問題を再現し、カスタム フィルターを適用して再現操作を識別する特定のセッション ID に絞り込むか、カスタム属性を使用して、関連するすべてのライブ テレメトリを表示できます。 あらゆるサーバーから情報を収集して、問題を早期に解決できます。
* 負荷に対するリソース使用量の反応を瞬時に表示: 負荷テストを実行するときに、"*任意の*" Windows パフォーマンス カウンターをライブで監視できます。また、運用環境を監視して悪影響を受ける前に対策を講じることもできます。 構成の変更やデプロイは不要です。
* リリースする修正プログラムを検証できます。 サービスを更新したときに、更新に問題がないことを確認します。 修正したエラーが発生しなくなっていることを検証します。
* 問題が発生しているサーバーを簡単に特定し、すべての KPI/ライブ フィードをフィルター処理してそのサーバーだけに絞り込むことができます。

Live Metrics & Events Stream のデータは無料です。請求には加算されません。 ポータル エクスペリエンスを開くと、サーバーからデータがオンデマンドでストリーミングされます。 データは、グラフが表示されている場合にのみ保持され、その後破棄されます。 従来の ASP.NET アプリケーションではすべての機能を使用できます。.NET Core アプリケーションでは、現在、ライブ メトリックとサンプル エラーの固定セットのみを使用できます。 Microsoft では、最新のライブ ストリーミング機能に対応するために、サポートされているすべての SDK が標準レベルに達するよう取り組んでいます。 

ライブ ストリームは、サンプリングの前またはカスタム TelemetryProcessors が適用される前に、アプリケーション インスタンスから収集されます。 

![Live Metrics Stream のビデオ](./media/app-insights-live-stream/youtube.png)[Live Metrics Stream のビデオ](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

ライブ メトリックとイベントにアクセスするには、左側のオプションをクリックするか、[概要] ブレードのボタンをクリックします。

![概要ブレードで、[Live Stream (ライブ ストリーム)] をクリックします](./media/app-insights-live-stream/live-stream-2.png)

## <a name="custom-live-kpi"></a>カスタム ライブ KPI
ポータルで Application Insights Telemetry に任意のフィルターを適用して、カスタム ライブ KPI を監視できます。 グラフをマウスでポイントしたときに表示されるフィルター コントロールをクリックします。 次のグラフは、URL 属性と期間属性にフィルターを適用して、カスタム要求数 KPI をプロットしています。 [ストリームのプレビュー] セクションでフィルターを検証します。このセクションには、指定した条件といずれかの時点で一致するテレメトリのライブ フィードが表示されます。 

![カスタム要求 KPI](./media/app-insights-live-stream/live-stream-filteredMetric.png)

Count 以外の値を監視できます。 オプションはストリームの種類によって異なります。ストリームの種類には、任意の Application Insights Telemetry (要求、依存関係、例外、トレース、イベント、またはメトリック) を指定できます。 オプションには、独自の[カスタム測定](app-insights-api-custom-events-metrics.md#properties)を指定できます。

![値のオプション](./media/app-insights-live-stream/live-stream-valueoptions.png)

Application Insights Telemetry だけでなく、Windows パフォーマンス カウンターを監視することもできます。ストリーム オプションから Windows パフォーマンス カウンターを選択し、パフォーマンス カウンターの名前を指定します。

ライブ メトリックは 2 つのポイントで集計されます。具体的には、サーバーごとにローカルで集計された後、すべてのサーバーにわたって集計されます。 どちらについても、それぞれのドロップダウンで他のオプションを選択することで既定値を変更できます。

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>サンプル テレメトリ: カスタム ライブ診断イベント
既定では、イベントのライブ フィードには失敗した要求と依存関係呼び出し、例外、イベント、トレースのサンプルが表示されます。 フィルター アイコンをクリックすると、任意の時点で適用された条件が表示されます。 

![既定のライブ フィード](./media/app-insights-live-stream/live-stream-eventsdefault.png)

メトリックと同様に、Application Insights Telemetry の種類のいずれかに任意の条件を指定できます。 この例では、特定の要求エラー、トレース、イベントを選択しています。 また、すべての例外と依存関係エラーも選択しています。

![カスタム ライブ フィード](./media/app-insights-live-stream/live-stream-events.png)

注: 現時点では、例外メッセージ ベースの条件には、最も外側の例外メッセージを使用します。 前の例では、"クライアントが切断されました。" という内部例外メッセージ ("<--" 区切り記号の後) が示されている害のない例外を除外するために、 "Error reading request content (要求内容の読み取りエラー)" が含まれていないメッセージという条件を使用します。

ライブ フィードの項目をクリックして詳細を表示します。 フィードを一時停止するには、**[一時停止]** をクリックするか、下にスクロールするか、または項目をクリックします。 スクロールして上部に戻るか、一時停止されている間に収集された項目のカウンターをクリックすると、ライブ フィードが再開されます。

![サンプリングされたライブ エラー](./media/app-insights-live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>サーバー インスタンスによるフィルター処理

特定のサーバー ロール インスタンスを監視する場合は、サーバーでフィルター処理できます。

![サンプリングされたライブ エラー](./media/app-insights-live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>SDK の要件
カスタムの Live Metrics Stream は、バージョン 2.4.0-beta2 以降の [Application Insights SDK for web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) で使用できます。 NuGet パッケージ マネージャーで [リリース前のパッケージを含める] を必ず選択してください。

## <a name="authenticated-channel"></a>認証済みチャネル
指定したカスタム フィルター条件は、Application Insights SDK の Live Metrics コンポーネントに送信されます。 フィルターに顧客 ID などの機密情報が含まれている可能性があります。 インストルメンテーション キーに加え、シークレット API キーを使用してチャネルをセキュリティで保護できます。
### <a name="create-an-api-key"></a>API キーを作成する

![API キーの作成](./media/app-insights-live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>API キーを構成に追加する
applicationinsights.config ファイルで、AuthenticationApiKey を QuickPulseTelemetryModule に追加します。
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add> 

```
または、コードで QuickPulseTelemetryModule に API キーを設定します。

``` C#

    module.AuthenticationApiKey = "YOUR-API-KEY-HERE";

```

ただし、接続されているすべてのサーバーを認識し、信頼している場合は、認証済みチャネルなしにカスタム フィルターを試すことができます。 このオプションは 6 か月間使用できます。 この上書きは、新しいセッションごとに 1 回、または新しいサーバーがオンラインになったときに必要になります。

![Live Metrics の認証オプション](./media/app-insights-live-stream/live-stream-auth.png)

>[!NOTE]
>フィルター条件に CustomerID などの機密情報を入力する前に、認証済みチャネルを設定することを強くお勧めします。
>

## <a name="troubleshooting"></a>トラブルシューティング

データが表示されない場合 保護されているネットワークにアプリケーションが存在する場合、Live Metrics Stream では他の Application Insights Telemetry とは異なる IP アドレスを使用します。 [これらの IP アドレス](app-insights-ip-addresses.md)がファイアウォールで開いていることを確認してください。



## <a name="next-steps"></a>次のステップ
* [Application Insights による使用状況の監視](app-insights-web-track-usage.md)
* [診断検索の使用](app-insights-diagnostic-search.md)


