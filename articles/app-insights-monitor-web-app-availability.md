<properties title="Monitor your web app's availability and responsiveness" pageTitle="Web tests in Application Insights" description="Make sure your web application is available and responsive. Get alerts if it becomes unavailable or responds slowly." metaKeywords="analytics web test" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Web アプリケーションの可用性と応答性の監視

Web アプリケーションをデプロイした後、Web テストを設定して Web アプリケーションの可用性と応答性を監視できます。Application Insights は、世界各地の複数のポイントから定期的に Web 要求を送信します。アプリケーションの応答が遅くなったりアプリケーションがまったく応答しなくなったりした場合は、Application Insights からその旨が通知されます。

Web テストは、パブリック インターネットからアクセスできる任意の HTTP エンドポイントに対して設定できます。

1.  [新しいアプリケーションを作成するかどうかの判断][新しいアプリケーションを作成するかどうかの判断]
-   [Web テストの設定][Web テストの設定]
-   [監視レポートの表示][監視レポートの表示]
-   [テストの編集または無効化][テストの編集または無効化]
-   [次のステップ][次のステップ]

## <a name="create"></a>1. 新しいアプリケーションを作成するかどうかの判断

この Web アプリケーションに関して既に[利用状況と健全性の監視][利用状況と健全性の監視]を行っていて、同じ場所に可用性データを表示する場合は、この手順をスキップしてください。

また、これらの結果を別個に保持する場合は、Microsoft Azure プレビューにログインし、Application Insights で新しいアプリケーションを作成します。

![New \> Application Insights][New \> Application Insights]

## <a name="setup"></a>2. Web テストの設定

Application Insights のアプリケーションのホーム ブレードで、空の [Web テスト] タイルをクリックします。

![Click the empty availability test][Click the empty availability test]

> *既に Web テストを行っている場合は、[Web テスト] タイルをクリックし、[Web テストの追加] を選択します。*

テストの名前とテストする URL を指定します。この URL は、パブリック インターネットから認識できる必要があります。

![Fill at least the URL of your website][Fill at least the URL of your website]

-   テストの場所とは、指定された URL に Web 要求を送信する Application Insights のサーバーの場所です。Web サイトで発生している問題とネットワークで発生している問題とを区別できるように、2 ～ 3 か所を選択してください。4 つ以上の場所は選択できません。

-   [成功条件] - 許容できる HTTP リターン コードを指定します (通常は 200)。

    すべての適切な応答に含まれる必要のある文字列を指定することもできます。文字列は、(ワイルドカードを含まない) プレーン文字列である必要があります。コンテンツが変更された場合はこの文字列も更新する必要があることに注意してください。

-   [アラート] - 既定では、15 分間の間にエラーが繰り返し発生する場合にアラートが送信されます。また、感度をさらに高めたり、通知先の電子メール アドレスを変更したりもできます。

### 他の URL のテスト

テストする URL は必要に応じて追加できます。たとえば、ホーム ページをテストするのに加えて、検索用の URL をテストしてデータベースが稼動していることを確認できます。

![On the web tests blade, choose Add][On the web tests blade, choose Add]

## <a name="monitor"></a>3. 監視レポートの表示

1 ～ 2 分待ってから、アプリケーション ブレードをいったん閉じて開き直します (このリリースでは、アプリケーション ブレードは自動的に更新されません)。

![Summary results on the home blade][Summary results on the home blade]

これは要約です。このアプリケーションに対して複数の Web テストを定義している場合は、ここにすべてまとめて表示されます。

[Web テスト] ブレードをクリックすると、Web テストの一覧が表示されます。

特定の Web テストを開きます。

![Click a specific webtest][Click a specific webtest]

特定の Web テスト ブレードで、下へスクロールして **[失敗したテスト]** を選択し、結果を選択します。

![Click a specific webtest][1]

結果には、エラーの原因が示されています。

![Webtest run result][Webtest run result]

詳細を調べるには、結果ファイルをダウンロードして Visual Studio で内容を確認します。

## <a name="edit"></a>4. テストの編集または無効化

個々のテストを開くと、テストを編集したり無効にしたりできます。

![Edit or disable a web test][Edit or disable a web test]

たとえば、サービスのメンテナンスを行うときは Web テストを無効にします。

## <a name="next"></a>次のステップ

[診断ログの検索][診断ログの検索]

[トラブルシューティング][トラブルシューティング]

## <a name="next"></a>詳細情報

-   [Application Insights - 概要][Application Insights - 概要]
-   [アプリケーションの正常性と利用状況の監視][利用状況と健全性の監視]
-   [ライブ Web サーバーの監視を今すぐ始める][ライブ Web サーバーの監視を今すぐ始める]
-   [メトリックの探索][メトリックの探索]
-   [Application Insights での診断ログの検索][診断ログの検索]
-   [Web アプリケーションの可用性と応答性の監視][Web アプリケーションの可用性と応答性の監視]
-   [カスタム イベントおよびメトリックを使用した利用状況の追跡][カスタム イベントおよびメトリックを使用した利用状況の追跡]
-   [トラブルシューティングおよび Q & A - Microsoft Azure プレビューの Application Insights][トラブルシューティング]

<!--Link references-->

  [新しいアプリケーションを作成するかどうかの判断]: #create
  [Web テストの設定]: #setup
  [監視レポートの表示]: #monitor
  [テストの編集または無効化]: #edit
  [次のステップ]: #next
  [利用状況と健全性の監視]: ../app-insights-monitor-application-health-usage/
  [New \> Application Insights]: ./media/appinsights/appinsights-11newApp.png
  [Click the empty availability test]: ./media/appinsights/appinsights-12avail.png
  [Fill at least the URL of your website]: ./media/appinsights/appinsights-13availChoices.png
  [On the web tests blade, choose Add]: ./media/appinsights/appinsights-16anotherWebtest.png
  [Summary results on the home blade]: ./media/appinsights/appinsights-14availSummary.png
  [Click a specific webtest]: ./media/appinsights/appinsights-15webTestList.png
  [1]: ./media/appinsights/appinsights-17-availViewDetails.png
  [Webtest run result]: ./media/appinsights/appinsights-18-availDetails.png
  [Edit or disable a web test]: ./media/appinsights/appinsights-19-availEdit.png
  [診断ログの検索]: ../app-insights-search-diagnostic-logs/
  [トラブルシューティング]: ../app-insights-troubleshoot-faq/
  [Application Insights - 概要]: ../app-insights-get-started/
  [ライブ Web サーバーの監視を今すぐ始める]: ../app-insights-monitor-performance-live-website-now/
  [メトリックの探索]: ../app-insights-explore-metrics/
  [Web アプリケーションの可用性と応答性の監視]: ../app-insights-monitor-web-app-availability/
  [カスタム イベントおよびメトリックを使用した利用状況の追跡]: ../app-insights-track-usage-custom-events-metrics/
