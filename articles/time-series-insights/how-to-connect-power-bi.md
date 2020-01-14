---
title: 環境を Power BI に接続する - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights を Power BI に接続して、組織全体でデータを共有、グラフ化、表示する方法について説明します。
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: ef7d78b292561e87eac12200bc0b9d68299759d9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443629"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Power BI の Time Series Insights からのデータを視覚化する

Azure Time Series Insights は、クラウド内の時系列データを格納、管理、クエリの実行、および視覚化するためのプラットフォームです。 [Power BI](https://powerbi.microsoft.com) は、組織全体で洞察と結果を共有できる豊富な視覚化機能を備えた、ビジネス分析ツールです。 両方のサービスを統合して、Time Series Insights の固有の視覚化機能と Power BI のそれの、両方のいいところを得られるようになりました。

学習内容は次のとおりです。

* クラウド コネクタを使用して Time Series Insights を Power BI に接続する
* Power BI でデータを使用して視覚化資料を作成する
* Power BI にレポートを発行し、組織の他の部門と共有する

終了するまでに、Azure Time Series Insights を使用して時系列データを視覚化し、Power BI の強力なデータの視覚化と容易な共有機能を使用してそれを拡張する方法について学習します。

まだお持ちでない場合は、[無料の Azure サブスクリプション](https://azure.microsoft.com/free/) にサインアップしてください。

## <a name="prerequisites"></a>前提条件

* 最新バージョンの [Power BI Desktop](https://powerbi.microsoft.com/downloads/) をダウンロードしてインストールすること
* [Azure Time Series Insights プレビュー インスタンス](time-series-insights-update-how-to-manage.md)を備えるまたは作成する

> [!IMPORTANT]
> Power BI コネクタは、現在、**Warm ストア**用に構成された Time Series Insights プレビュー*従量課金制* 環境でサポートされています。

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Time Series Insights から Power BI へのデータの接続

Time Series Insights 環境を Power BI に接続するには、次の手順を実行します。

1. Time Series Insights Explorer を開く                      
1. データをクエリまたは生データとしてエクスポートする                       
1. Power BI Desktop を開く
1. カスタム クエリから読み込む

### <a name="export-data-into-power-bi-desktop"></a>データを Power BI desktop にエクスポートする

作業を開始するには:

1. Time Series Insights プレビュー エクスプローラーを開き、データをキュレーションします。
1. 満足できるビューを作成したら、 **[その他の操作]** ドロップダウン メニューに移動し、 **[Power BI に接続]** をクリックします。 

    [![Time Series Insights プレビュー エクスプローラーのエクスポート](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. このタブ内でパラメーターを設定します。

   1. 表示する相対時間枠を指定します。 既存のビューに問題がなければ、 **[Existing timeframe]\(既存の時間枠\)** のままにします。 
   1. **[Aggregated]\(集計\)** と **[生イベント]** のどちらかを選択します。 
   
       > [!NOTE]
       > 後で Power BI により、いつでもデータを集計できますが、集計後に生データに戻すことはできません。 
       
       > [!NOTE]
       > 生イベント レベル データには、10 万までのイベント数の制限があります。

       [![接続](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. **Warm ストア**用に Time Series Insights インスタンスを構成していない場合は、警告が表示されます。

       [![Warm ストア の警告](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Azure ポータルでは、既存のインスタンスを**Warm ストア**用に構成できます。

1. **[クエリをクリップボードにコピー]** を選択します。
1. 次に、Power BI Desktop を起動します。
1. **[ホーム]** タブの Power BI Desktop で、左上隅にある **[データの取得]** を選択し、 **[More]** を選択します。

    [![[ホーム] のドロップダウン](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. 「**Time Series Insights**」を検索し、 **[Azure Time Series Insights (Beta)]** を選択して、 **[接続]** をクリックします。

    [![Time Series Insights への Power BI の接続](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    または、 **[Azure]** タブに移動し、 **[Azure Time Series Insights (Beta)]** を選択して、 **[接続]** をクリックします。
    
1. サード パーティのリソースに接続するためのアクセス許可を求めるメッセージ ダイアログ ボックスが表示されます。 **[続行]** を選択します。

    [![[Create Custom Query]\(カスタム クエリの作成\) を選択する](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. **[データ ソース ]** 直下のドロップダウン メニューで、 **[Create custom query]\(カスタム クエリの作成\)** を選択します。 クリップボードから、オプションの **[Custom Query (optional)]\(カスタム クエリ (省略可能)\)** フィールドに貼り付け、 **[OK]** を押します。

    [![カスタム クエリを渡し、[OK] を選択する](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. これで、データ テーブルが読み込まれます。 **[読み込み]** を押して、Power BI に読み込みます。

    [![テーブルで読み込まれたデータを確認し、[読み込み] を選択する](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

これらの手順を完了している場合は、次のセクションに進んでください。

## <a name="create-a-report-with-visuals"></a>ビジュアルでレポートを作成する

データを Power BI にインポートしたので、次は視覚化されたレポートを作成します。

1. ウィンドウの左側で、 **[レポート]** ビューが選択されていることを確認します。

    [![[レポート] ビューを選択する](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  **[視覚化]** 列で、希望する視覚化を選択します。 たとえば、 **[折れ線グラフ]** を選択します。 これにより、空の折れ線グラフがキャンバスに追加されます。
 
1.  **[フィールド]** の一覧で **[タイムスタンプ]** を選択し、 **[軸]** フィールドにドラッグして項目を X 軸に沿って表示します。

1.  再び **[フィールド]** の一覧で **[TimeSeriesId]** を選択し、 **[値]** フィールドにドラッグして項目を Y 軸に表示します。

    [![折れ線グラフを作成する](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  キャンバスに別のグラフを追加するには、キャンバス上の折れ線グラフの外側の任意の場所をクリックして、このプロセスを繰り返します。

    [![追加のグラフを作成して共有する](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

レポートを作成したら、Power BI Reporting Services にパブリッシュできます。

## <a name="advanced-editing"></a>高度な編集

データセットを既に Power BI に読み込んでいるが、クエリ (日付/時刻や環境 ID パラメーターなど) を変更する場合、Power BI の詳細エディター機能を使用してこれを行うことができます。 詳細については、[Power BI のドキュメント](https://docs.microsoft.com/power-bi/desktop-query-overview)を参照してください。

概要:

1. Power BI Desktop で、 **[クエリを編集]** を選択します。
1. **[詳細エディター]** を押します。

    [![詳細エディターでクエリを編集する](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. 必要に応じて JSON ペイロードを変更します。
1. **[完了]** を選択し、 **[Power Query エディター ウィンドウ]** で **[Close & Apply]\(閉じて適用\)** します。

希望する変更が適用されていることを確認します。  

## <a name="next-steps"></a>次の手順

* Azure Time Series Insights のための [Power BI コネクタの概念](https://docs.microsoft.com/power-bi/desktop-query-overview)に関するページをご覧ください。

* [Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-query-overview) の詳細を参照してください。

* [Time Series Insights GA エクスプローラー](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)および [Time Series Insights プレビュー エクスプローラー](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)を参照してください。
