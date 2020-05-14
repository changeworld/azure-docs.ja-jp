---
title: クイック スタート:Azure Monitor Application Insights を使用して Web サイトを監視する
description: このクイックスタートでは、Azure Monitor Application Insights を使用したクライアント (ブラウザー) 側の Web サイト監視を設定する方法について説明します。
ms.topic: quickstart
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: b47f3ce1ebed12d14dffd68e87dd013bb86218ea
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801639"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>クイック スタート:Azure Monitor Application Insights を使って Web サイトの監視を開始する

このクイックスタートでは、オープンソースの Application Insights JavaScript SDK を Web サイトに追加する方法について説明します。 また、Web サイトへの訪問者にとってのクライアント (ブラウザー) 側のエクスペリエンスについても理解を深めることができます。

Azure Monitor Application Insights を使用すると、Web サイトの可用性、パフォーマンス、および利用状況を簡単に監視できます。 アプリケーションのエラーを、ユーザーからの報告を待つことなく、迅速に特定して診断することもできます。 Application Insights では、サーバー側の監視機能とクライアント (ブラウザー) 側の監視機能の両方が提供されます。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* Application Insights JavaScript SDK を追加できる Web サイト。

## <a name="enable-application-insights"></a>Application Insights を有効にする

Application Insights は、オンプレミスまたはクラウドで実行されている、インターネットに接続された任意のアプリケーションからテレメトリ データを収集できます。 このデータを表示するには、次の手順を使用します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. **[リソースの作成]**  >  **[管理ツール]**  >  **[Application Insights]** の順に選択します。

   > [!NOTE]
   >Application Insights リソースの作成が初めての場合は、「[Application Insights リソースの作成](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)」を参照してください。
1. 構成ボックスが表示されたら、次の表を使用して入力フィールドを完成させます。

    | 設定        | 値           | 説明  |
   | ------------- |:-------------|:-----|
   | **名前**      | グローバルに一意の値 | 監視しているアプリを識別する名前。 |
   | **リソース グループ**     | myResourceGroup      | Application Insights データをホストする新しいリソース グループの名前。 新しいリソース グループを作成するか、既存のリソース グループを使用することができます。 |
   | **場所** | 米国東部 | お近くの場所か、アプリがホストされている場所の近くを選択します。 |
1. **［作成］** を選択します

## <a name="create-an-html-file"></a>HTML ファイルを作成する

1. ローカル コンピューターで、``hello_world.html`` という名前のファイルを作成します。 この例では、C ドライブのルートにファイルを作成するので、``C:\hello_world.html`` のようになります。
1. 次のスクリプトをコピーして ``hello_world.html`` に貼り付けます。

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations, visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Application Insights SDK を構成する

1. **[概要]**  >  **[基本]** の順に選択し、ご自分のアプリケーションの**インストルメンテーション キー**をコピーします。

   ![新しい Application Insights リソースのフォーム](media/website-monitoring/instrumentation-key-001.png)

1. ``hello_world.html`` ファイルの閉じる ``</head>`` タグの前に、次のスクリプトを追加します。

   ```javascript
   <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
      {
         instrumentationKey:"INSTRUMENTATION_KEY"
      }
      );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

1. ``hello_world.html`` を編集し、インストルメンテーション キーを追加します。

1. ローカル ブラウザー セッションで、``hello_world.html`` を開きます。 この操作によって、単一のページ ビューが作成されます。 ブラウザーを最新の情報に更新して、複数のテスト ページ ビューを生成することができます。

## <a name="monitor-your-website-in-the-azure-portal"></a>Azure portal で Web サイトを監視する

1. Azure portal で Application Insights の **[概要]** ページを再度開き、現在実行中のアプリケーションの詳細情報を表示できます。 **[概要]** ページは、インストルメンテーション キーを取得した場所です。

   概要ページの 4 つの既定のグラフでは、サーバー側のアプリケーション データが対象になっています。 ここではクライアント (ブラウザー) 側の JavaScript SDK との対話をインストルメント化しているため、この特定のビューは、サーバー側の SDK もインストールされない限り、適用されません。

1. **[Analytics]** ![アプリケーション マップ アイコン](media/website-monitoring/006.png) を選択します。  この操作により、Application Insights で収集されたすべてのデータを分析するための豊富なクエリ言語を備えた **Analytics** が開きます。 クライアント側のブラウザー要求に関連しているデータを表示するには、次のクエリを実行します。

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![一定の期間にわたるユーザー要求に関する Analytics グラフ](./media/website-monitoring/analytics-query.png)

1. **[概要]** ページに戻ります。 **[調査]** ヘッダーで、 **[ブラウザー]** を選択し、 **[パフォーマンス]** を選択します。  Web サイトのパフォーマンスに関するメトリックが表示されます。 Web サイトのエラーと例外を分析するための対応するビューがあります。 **[サンプル]** を選択して、[エンドツーエンドのトランザクションの詳細](../../azure-monitor/app/transaction-diagnostics.md)にアクセスできます。

   ![サーバー メトリックのグラフ](./media/website-monitoring/browser-performance.png)

1. Application Insights のメイン メニューの **[使用状況]** ヘッダーにある [ **[ユーザー]** ](../../azure-monitor/app/usage-segmentation.md) を選択し、[ユーザー動作分析ツール](../../azure-monitor/app/usage-overview.md)の調査を開始します。 1 台のマシンからテストを行っているため、1 人のユーザーのデータのみが表示されます。 ライブの Web サイトの場合、ユーザーの分布はこのようになります。

     ![ユーザーのグラフ](./media/website-monitoring/usage-users.png)

1. 複数のページがある、より複雑な Web サイトでは、[**User Flows**](../../azure-monitor/app/usage-flows.md) ツールを使用して、Web サイトのさまざまな部分を辿る訪問者の経路を追跡できます。

   ![User Flows の視覚化](./media/website-monitoring/user-flows.png)

Web サイトを監視するためのより高度な構成については、[JavaScript SDK の API リファレンス](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

引き続き他のクイックスタートまたはチュートリアルの作業を行う場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 そうでなければ、以下の手順に従い、このクイックスタートで作成したすべてのリソースを Azure portal で削除します。

> [!NOTE]
> 既存のリソース グループを使用した場合は、次の手順を使用できません。 代わりに、個々の Application Insights リソースを削除するだけで済みます。 リソース グループを削除すると、そのグループに属している基になるリソースがすべて削除されるので注意してください。

1. Azure portal の左側のメニューで **[リソース グループ]** を選択し、**myResourceGroup** またはお使いの一時リソース グループの名前を選択します。
1. リソース グループのページで **[削除]** を選択し、テキスト ボックスに「**myResourceGroup**」と入力してから **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [パフォーマンスの問題の特定と診断](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
