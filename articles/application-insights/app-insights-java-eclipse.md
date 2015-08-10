<properties 
	pageTitle="Eclipse で Java 用に Application Insights を使う" 
	description="Eclipse プラグインを使用して、Java Web サイトのパフォーマンスと利用状況を Application Insights で監視できるようにします" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="awills"/>
 
# Eclipse で Java 用に Application Insights を使う

Application Insights SDK は、利用状況とパフォーマンスを分析できるようにするために、Java Web アプリケーションからテレメトリを送信します。Application Insights 用 Eclipse プラグインは、プロジェクトに SDK を自動的にインストールします。これにより、すぐに使用可能なテレメトリに加えて、カスタムのテレメトリの作成に使用できる API が提供されます。


## 前提条件

現在、プラグインは、Eclipse の動的 Web プロジェクトに対応しています ([他の種類の Java プロジェクトに Application Insights を追加します][java])。

必要なものは次のとおりです。

* Oracle JRE 1.6 以降。
* [Microsoft Azure](http://azure.microsoft.com/) のサブスクリプション([無料評価版](http://azure.microsoft.com/pricing/free-trial/)を使って作業を開始できます)。
* [Eclipse IDE for Java EE Developers](http://www.eclipse.org/downloads/) Indigo 以降。
* Windows 7 以降または Windows Server 2008 以降。

## Eclipse に SDK をインストールする (1 回限り)

この操作は、コンピューターごとに 1 回行う必要があります。この手順では、SDK を各動的 Web プロジェクトに追加できるツールキットをインストールします。

1. Eclipse で、[Help]、[Install New Software] の順にクリックします。

    ![[Help]、[Install New Software]](./media/app-insights-java-eclipse/0-plugin.png)

2. SDK は、http://dl.windowsazure.com/eclipse の Azure Toolkit のページから入手できます。
3. **[Contact all update sites]** チェック ボックスをオフにします。

    ![Application Insights SDK の場合、[Contact all update sites] チェック ボックスをオフにします](./media/app-insights-java-eclipse/1-plugin.png)

Java プロジェクトごとに、残りの手順に従います。

## Application Insights のインストルメンテーション キーを取得する

利用状況およびパフォーマンス分析は、Azure Web ポータルの Azure リソースに表示されます。この手順では、アプリケーションの Azure リソースを設定します。

1. [Microsoft Azure ポータル](https://portal.azure.com)にログインします ([Azure サブスクリプションが必要です](http://azure.microsoft.com/))。
2. 新しい Application Insights リソースを作成します。

    ![[+] をクリックし、[Application Insights] を選択します](./media/app-insights-java-eclipse/01-create.png)
3. アプリケーションの種類を [Java Web アプリケーション] に設定します。

    ![名前を入力し、[Java Web アプリケーション] を選択した後、[作成] をクリックします](./media/app-insights-java-eclipse/02-create.png)
4. 新しいリソースのインストルメンテーション キーを見つけます。このキーは、後で Eclipse のプロジェクトに貼り付けます。

    ![新しいリソース概要で、[プロパティ] をクリックし、インストルメンテーション キーをコピーします](./media/app-insights-java-eclipse/03-key.png)

## Java プロジェクトに SDK を追加する

1. Web プロジェクトのコンテキスト メニューから Application Insights を追加します。

    ![新しいリソース概要で、[プロパティ] をクリックし、インストルメンテーション キーをコピーします](./media/app-insights-java-eclipse/4-addai.png)
2. Azure ポータルで入手したインストルメンテーション キーを貼り付けます。

    ![新しいリソース概要で、[プロパティ] をクリックし、インストルメンテーション キーをコピーします](./media/app-insights-java-eclipse/5-config.png)


このキーは、テレメトリのすべての項目と共に送信され、リソース内にこれを表示するように Application Insights に指示します。

## アプリケーションを実行してメトリックを表示する

アプリケーションを実行します。

Microsoft Azure の Application Insights リソースに戻ります。

HTTP 要求データが概要ブレードに表示されます (表示されない場合は、数秒待ってから [最新の情報に更新] をクリックします)。

![サーバーの応答、要求の数、エラー](./media/app-insights-java-eclipse/5-results.png)
 

任意のグラフをクリックして、より詳細なメトリックを表示します。

![名前別の要求数](./media/app-insights-java-eclipse/6-barchart.png)


[メトリックの詳細についてはこちらをご覧ください。][metrics]

 

要求のプロパティを表示すると、その要求に関連付けられているテレメトリ イベント (要求や例外など) が表示されます。
 
![この要求のすべてのトレース](./media/app-insights-java-eclipse/7-instance.png)


## クライアント側のテレメトリ

クイック スタートブレードで、[マイ Web ページを監視するためのコードを取得する] をクリックします。

![アプリの概要ブレードで、クイック スタートを選択し、マイ Web ページを監視するためのコードを取得します。スクリプトをコピーします。](./media/app-insights-java-eclipse/02-monitor-web-page.png)

HTML ファイルの先頭にコード スニペットを挿入します。

#### クライアント側のデータの表示

更新された Web ページを開いて使用します。1 ～ 2 分待ってから、Application Insights に戻り、[使用状況] ブレードを更新します。

[使用状況] ブレードに、ページ ビュー、ユーザー、セッションのメトリックが表示されます。

![セッション、ユーザー、ページ ビュー](./media/app-insights-java-eclipse/appinsights-47usage-2.png)

[クライアント側のテレメトリの設定方法の詳細についてはこちら。][usage]

## 可用性 Web テスト

Application Insights では、Web サイトを定期的にテストして、Web サイトが正常に動作および応答していることを確認できます。セットアップするには、概要ブレードの空の Web テスト グラフをクリックし、パブリック URL を入力します。

応答時間のグラフが表示されます。また、サイトがダウンしている場合はメールによる通知を受け取ります。

![Web テストの例](./media/app-insights-java-eclipse/appinsights-10webtestresult.png)

[可用性 Web テストの詳細についてはこちら。][availability]

## 診断ログ

トレース用に Logback または Log4J (v1.2 または v2.0) を使用している場合は、トレース ログを自動的に Application Insights に送信して、Application Insights でトレース ログを探索および検索できます。

[診断ログの詳細についてはこちら][javalogs]

## カスタムのテレメトリ 

Java Web アプリケーションに数行のコードを挿入して、ユーザーの行動を調べたり、問題の診断に役立つ情報を取得したりすることができます。

コードは、Web ページの JavaScript とサーバー側 Java のどちらにも挿入できます。

[カスタムのテレメトリの詳細についてはこちら][track]



## 次のステップ

#### 問題の検出と診断

* Web クライアントからパフォーマンス テレメトリを取得するには、[Web クライアントのテレメトリを追加][usage]します。
* [Web テストを設定][availability]して、アプリケーションが動作していて応答できることを確認します。
* 問題の診断に役立つ情報を得るには、[イベントおよびログを検索][diagnostic]します。
* [Log4J または Logback トレースをキャプチャします。][javalogs]

#### 利用状況を追跡する

* ビューや基本的なユーザー メトリックを監視するには、[Web クライアント テレメトリを追加][usage]します。
* クライアント側とサーバー側でアプリケーションの利用状況を確認するには、[カスタム イベントおよびメトリックを追跡][track]します。


<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=July15_HO5-->