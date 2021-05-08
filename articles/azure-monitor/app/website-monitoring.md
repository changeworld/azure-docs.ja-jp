---
title: クイック スタート:Azure Monitor Application Insights を使用して Web サイトを監視する
description: このクイックスタートでは、Azure Monitor Application Insights を使用したクライアント (ブラウザー) 側の Web サイト監視を設定する方法について説明します。
ms.topic: quickstart
ms.date: 03/19/2021
ms.custom: mvc
ms.openlocfilehash: 0e10db39c8dbbf81087d696cfbb5b2ded1ae79ac
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654938"
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
   >Application Insights リソースの作成が初めての場合は、「[Application Insights リソースの作成](./create-new-resource.md)」を参照してください。
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

1. **[概要]** を選択し、アプリケーションの **接続文字列** をコピーします。 この例では、接続文字列のインストルメンテーション キー部分 `InstrumentationKey=00000000-0000-0000-0000-000000000000;` のみが必要です。

    :::image type="content" source="media/website-monitoring/keys.png" alt-text="インストルメンテーション キーと接続文字列が含まれた [概要] ページのスクリーンショット。":::

1. ``hello_world.html`` ファイルの閉じる ``</head>`` タグの前に、次のスクリプトを追加します。

    ```html
    <script type="text/javascript">
    !function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
    src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
    // name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
    // ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
    // useXhr: 1, // Use XHR instead of fetch to report failures (if available),
    crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
    // onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
    cfg: { // Application Insights Configuration
        connectionString:"InstrumentationKey=YOUR_INSTRUMENTATION_KEY_GOES_HERE;" 
        /* ...Other Configuration Options... */
    }});
    </script>
    ```

    > [!NOTE]
    > 現在のスニペット (上に示されています) はバージョン "5" で、バージョンは sv:"#" としてスニペット内でエンコードされています。[現在のバージョンと構成の詳細は、GitHub で入手できます](https://go.microsoft.com/fwlink/?linkid=2156318)。

1. ``hello_world.html`` を編集し、インストルメンテーション キーを追加します。

1. ローカル ブラウザー セッションで、``hello_world.html`` を開きます。 この操作によって、単一のページ ビューが作成されます。 ブラウザーを最新の情報に更新して、複数のテスト ページ ビューを生成することができます。

## <a name="monitor-your-website-in-the-azure-portal"></a>Azure portal で Web サイトを監視する

1. Azure portal で Application Insights の **[概要]** ページを再度開き、現在実行中のアプリケーションの詳細情報を表示できます。 **[概要]** ページは、インストルメンテーション キーを取得した場所です。

   概要ページの 4 つの既定のグラフでは、サーバー側のアプリケーション データが対象になっています。 ここではクライアント (ブラウザー) 側の JavaScript SDK との対話をインストルメント化しているため、この特定のビューは、サーバー側の SDK もインストールされない限り、適用されません。

1. **[ログ]** を選択します。  この操作により、Application Insights で収集されたすべてのデータを分析するための豊かなクエリ言語を備えた **ログ** が開きます。 クライアント側のブラウザー要求に関連しているデータを表示するには、次のクエリを実行します。

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

   :::image type="content" source="media/website-monitoring/log-query.png" alt-text="一定期間にわたるユーザー要求に関する Log Analytics グラフのスクリーンショット。":::

1. **[概要]** ページに戻ります。 **[調査]** ヘッダーで、 **[パフォーマンス]** を選択し、次に **[ブラウザー]** タブを選択します。Web サイトのパフォーマンスに関するメトリックが表示されます。 Web サイトのエラーと例外を分析するための対応するビューがあります。 **[サンプル]** を選択して、[エンドツーエンドのトランザクションの詳細](./transaction-diagnostics.md)にアクセスできます。

     :::image type="content" source="media/website-monitoring/performance.png" alt-text="ブラウザー メトリック グラフが含まれた [パフォーマンス] タブのスクリーンショット。":::

1. Application Insights のメイン メニューの **[使用状況]** ヘッダーにある [ **[ユーザー]**](./usage-segmentation.md) を選択し、[ユーザー動作分析ツール](./usage-overview.md)の調査を開始します。 1 台のマシンからテストを行っているため、1 人のユーザーのデータのみが表示されます。

1. 複数のページがある、より複雑な Web サイトでは、[**User Flows**](./usage-flows.md) ツールを使用して、Web サイトのさまざまな部分を辿る訪問者の経路を追跡できます。

Web サイトを監視するためのより高度な構成については、[JavaScript SDK の API リファレンス](./javascript.md)を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

引き続き他のクイックスタートまたはチュートリアルの作業を行う場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 そうでなければ、以下の手順に従い、このクイックスタートで作成したすべてのリソースを Azure portal で削除します。

> [!NOTE]
> 既存のリソース グループを使用した場合は、次の手順を使用できません。 代わりに、個々の Application Insights リソースを削除するだけで済みます。 リソース グループを削除すると、そのグループに属している基になるリソースがすべて削除されるので注意してください。

1. Azure portal の左側のメニューで **[リソース グループ]** を選択し、**myResourceGroup** またはお使いの一時リソース グループの名前を選択します。
1. リソース グループのページで **[削除]** を選択し、テキスト ボックスに「**myResourceGroup**」と入力してから **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [パフォーマンスの問題の特定と診断](../logs/log-query-overview.md)

