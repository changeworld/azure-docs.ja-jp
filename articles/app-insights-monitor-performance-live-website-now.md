<properties 
	pageTitle="実行中の Web サイトのパフォーマンスの問題の診断" 
	description="Web サイトを再デプロイせずにそのパフォーマンスを監視します。スタンドアロンでの使用または Application Insights SDK との併用" 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-23" 
	ms.author="awills"/>
 

# ライブ Web サーバーの監視を今すぐ始める

*Application Insights はプレビュー段階です。*

Web アプリケーションが動作がおかしくなっていませんか。Web アプリケーションを再構築したり再デプロイしたりすることなく、例外やパフォーマンスの問題をすぐに分析できます。Application Insights エージェントをサーバーにインストールすると、パフォーマンスのボトルネックを見つけたり、任意の例外のスタック トレースを取得したりできます。 


#### この方法で Application Insights を設定するのはどのような場合が適していますか?

このアプローチは、主に、ライブ IIS Web サイトのパフォーマンスの問題を迅速に診断するのに適しています。

エージェントをサーバーにインストールするだけで、Application Insights にパフォーマンス データが表示されます。

- この方法は、IIS サーバー上でホストされる ASP.NET アプリケーションに適用できます。

- データを表示するには、[Microsoft Azure アカウント](http://azure.com)が必要です。

- Web アプリケーションが実行されているサーバーに対する管理アクセス権が必要です。 

- Web アプリケーションのコードは*不要*です。アプリケーションの再構築や再デプロイも必要ありません。 

- この方法では、Web アプリケーションがそのままインストルメント化されます。トレースまたはログ コードを挿入したりはしません (ただし、後で必要に応じてそうすることもできます)。

ログまたは診断トレースを挿入する場合は、ここから続けずに、[Application Insights をプロジェクトに追加し][greenbrown]、再デプロイしてください。すべての種類のオプションについては、「[Application Insights - 開始方法][start]」を参照してください。

## Web サーバーへの Application Insights エージェントのインストール

1. Web サーバーで、管理者の資格情報を使用してログインします。

2. Version 5.0 以降の [Web プラットフォーム インストーラー](http://www.microsoft.com/web/downloads/platform.aspx)があることを確認します。
3. Web プラットフォーム インストーラーを使用して、Application Insights エージェントをインストールします。

    ![](./media/appinsights/appinsights-031-wpi.png)
4. インストール ウィザードで、Microsoft Azure にサインインします。

    ![](./media/appinsights/appinsights-035-signin.png)
5. 監視するインストール済みの Web アプリケーションまたは Web サイトを選択し、Application Insights ポータルで結果を表示するときに使用する名前を構成します。最後に、[追加] ボタンをクリックします。

    ![](./media/appinsights/appinsights-036-configAIC.png)

    通常は、新しいリソースを作成する方法を選んでください。

    既存のリソースを使用するケースとしては、たとえば、サイト用の [Web テスト][availability]を既に設定している場合があります。  

6. 監視対象の Web サイトに ApplicationInsights.config が挿入されます。

    ![](./media/appinsights/appinsights-034-aiconfig.png)

   これ以外にも、web.config にいくつかの変更が加えられます。

### 後で (再) 構成する

ウィザードを完了した後、いつでも必要に応じてエージェントを再構成できます。この方法は、エージェントをインストールした際の初期設定に問題があった場合にも使用できます。

![Click the Application Insights icon on the task bar](./media/appinsights/appinsights-033-aicRunning.png)

## データの表示

Azure のアカウントを開いた後、Application Insights を参照して、作成したリソースを開きます。

![](./media/appinsights/appinsights-08openApp.png)

[アプリケーションの健全性] にデータが表示されます。

![](./media/appinsights/appinsights-037-results.png)

#### データが表示されない場合

  * サイトを使用してデータを生成します。
  * データが到着するまでしばらく待ちます。
  * サーバーのファイアウォールでポート 443 を介した dc.services.visualstudio.com への発信トラフィックが許可されていることを確認します。 

#### アプリケーションの使用状況レポートの使用方法
 * [データを理解して、タイルを構成します。][perf]

## <a name="next"></a>次のステップ


[データの表示][perf]

[診断ログを検索する][diagnostic]

[Web テスト][availability]

[利用状況の監視の設定][start]

[トラブルシューティング][qna]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




<!--HONumber=46--> 
