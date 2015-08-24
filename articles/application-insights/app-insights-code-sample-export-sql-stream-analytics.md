<properties 
	pageTitle="チュートリアル: Application Insights から SQL Database へのテレメトリのエクスポート" 
	description="連続エクスポート機能を使用して、Application Insights でテレメトリの独自の分析をコーディングします。" 
	services="application-insights" 
    documentationCenter=""
	authors="noamben" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/13/2015" 
	ms.author="awills"/>
 
# チュートリアル: Stream Analytics を使用した Application Insights から SQL へのエクスポート

この記事では、[連続エクスポート][export]と [Azure Stream Analytics](http://azure.microsoft.com/services/stream-analytis/) を使用してテレメトリ データを [Visual Studio Application Insights][start] から Azure SQL データベースに移動する方法を示します。

連続エクスポートにより、JSON 形式でテレメトリ データが Azure Storage に移動されます。Azure Stream Analytics を使って JSON オブジェクトを解析し、データベース テーブルに行を作成します。

(一般的に、連続エクスポートは、アプリが Application Insights に送信するテレメトリの独自の分析を行うための手段です。エクスポートしたテレメトリを使用して他のことを行うよう、このコード サンプルを適合させることもできます。たとえば、データの集約や PowerBI 視覚化スイートへのデータの発行などです)。

監視対象のアプリが存在していることを想定して説明を始めます。


この例では、ページ ビュー データを使用しますが、同じパターンを簡単に他のデータ型 (カスタム イベントや例外など) に拡張できます。


## Application Insights SDK の追加

アプリケーションを監視するには、アプリケーションに [Application Insights SDK][start] を追加します。プラットフォーム、IDE、および言語ごとにそれぞれ異なる SDK やヘルパー ツールがあります。Web ページ、Java または ASP.NET Web サーバー、および各種モバイル デバイスを監視できます。SDK はすべてテレメトリを [Application Insights ポータル][portal]に送信します。そのポータルでは、強力な分析ツールおよび診断ツールを使用でき、データをストレージにエクスポートできます。

作業を開始するには:

1. [Microsoft Azure のアカウント](http://azure.microsoft.com/pricing/)を取得します。
2. [Azure ポータル][portal]で、アプリに新しい Application Insights リソースを追加します。

    ![[新規]、[開発者向けサービス]、[Application Insights] の順に選択し、アプリケーションの種類を選択します](./media/app-insights-code-sample-export-sql-stream-analytics/010-new-asp.png)


    (対象とするアプリの種類やお使いのサブスクリプションは異なる可能性があります。)
3. クイック スタートを開き、アプリの種類に応じて SDK をセットアップする方法を調べます。

    ![クイック スタートを選択し、指示に従います](./media/app-insights-code-sample-export-sql-stream-analytics/020-quick.png)

    アプリの種類が一覧表示されていない場合、[[作業の開始]][start] ページを確認します。

4. この例では、Web アプリを監視しますので、Visual Studio の Azure ツールを使用して SDK をインストールします。Application Insights リソースの名前を指定します。

    ![Visual Studio ソリューション エクスプローラーでプロジェクトを右クリックし、[Application Insights の追加] を選択します。[Send telemetry to] で、新しいリソースを選択するか、既存のリソースを使用します。](./media/app-insights-code-sample-export-sql-stream-analytics/appinsights-d012-addbrown.png)

5. アプリを発行して Application Insights のリソースに表示されるテレメトリ データを確認します。


## Azure でのストレージの作成

連続エクスポートでは、常に Azure のストレージ アカウントにデータが出力されるため、まずストレージを作成する必要があります。

1. [Azure ポータル][portal]で、サブスクリプションのストレージ アカウントを作成します。

    ![Azure ポータルで、[新規]、[データ]、[Storage] の順に選択します](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)

2. コンテナーを作成する

    ![新しいストレージで、[コンテナー] を選択し、[追加] を選択します](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)

3. ストレージ アクセス キーのコピー

    これは、ストリーム分析サービスへの入力のセットアップのためにすぐに必要になります。

    ![ストレージで、[設定]、[キー] の順に開き、プライマリ アクセス キーのコピーを取ります](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## Azure ストレージへの連続エクスポートの開始

1. Azure ポータルで、アプリケーション用に作成した Application Insights リソースを参照します。

    ![[参照]、[Application Insights]、アプリケーションの順に選択します](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)

2. 連続エクスポートを作成します。

    ![[設定]、[連続エクスポート]、[追加] の順に選択します](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)


    以前に作成したストレージ アカウントを選択します。

    ![エクスポート先の設定](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)
    
    表示するイベントの種類を設定します。

    ![イベントの種類の選択](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)

ここで、しばらく待機し、ユーザーにアプリケーションを使用させます。テレメトリが開始し、統計グラフが[メトリックス エクスプローラー][metrics]に表示され、個々のイベントが[診断検索][diagnostic]に表示されます。

また、データがストレージにエクスポートされます。そこで内容を検査できます。たとえば、Visual Studio にはストレージ ブラウザーがあります。


![Visual Studio で次の順に開きます。[サーバー ブラウザー]、[Azure]、[Storage]](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)

イベントが JSON 形式で BLOB ファイルに書き込まれます。各ファイルに 1 つ以上のイベントが含まれる場合があります。このため、イベント データを読み取って必要なフィールドをフィルター処理します。データの処理に関して行えることはありますが、今日の計画は、Stream Analytics を使用してデータを SQL database に移動することです。それにより、興味深い多くのクエリを実行しやすくなります。

## Azure SQL Database の作成

[Azure ポータル][portal]でサブスクリプションから開始するにあたり、データ書き込み先となるデータベース (サーバーが存在しない場合は新しいサーバーも) を作成します。

![[新規]、[データ]、[SQL]](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)


データベース サーバーに Azure サービスがアクセス可能であることをご確認ください。


![[参照]、[サーバー]、[使用するサーバー]、[設定]、[ファイアウォール]、[Azure へのアクセスの許可]](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## Azure SQL DB でのテーブルの作成

任意の管理ツールを使って前のセクションで作成したデータベースに接続します。このチュートリアルでは、[SQL Server Management Tools](https://msdn.microsoft.com/ms174173.aspx) (SSMS) を使用します。

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

新しいクエリを作成し、次の T-SQL を実行します。

```SQL

CREATE TABLE [dbo].[PageViewsTable](
	[pageName] [nvarchar](max) NOT NULL,
	[viewCount] [int] NOT NULL,
	[url] [nvarchar](max) NULL,
	[urlDataPort] [int] NULL,
	[urlDataprotocol] [nvarchar](50) NULL,
	[urlDataHost] [nvarchar](50) NULL,
	[urlDataBase] [nvarchar](50) NULL,
	[urlDataHashTag] [nvarchar](max) NULL,
	[eventTime] [datetime] NOT NULL,
	[isSynthetic] [nvarchar](50) NULL,
	[deviceId] [nvarchar](50) NULL,
	[deviceType] [nvarchar](50) NULL,
	[os] [nvarchar](50) NULL,
	[osVersion] [nvarchar](50) NULL,
	[locale] [nvarchar](50) NULL,
	[userAgent] [nvarchar](max) NULL,
	[browser] [nvarchar](50) NULL,
	[browserVersion] [nvarchar](50) NULL,
	[screenResolution] [nvarchar](50) NULL,
	[sessionId] [nvarchar](max) NULL,
	[sessionIsFirst] [nvarchar](50) NULL,
	[clientIp] [nvarchar](50) NULL,
	[continent] [nvarchar](50) NULL,
	[country] [nvarchar](50) NULL,
	[province] [nvarchar](50) NULL,
	[city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
	[eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/app-insights-code-sample-export-sql-stream-analytics/34-create-table.png)

## Azure Stream Analytics インスタンスの作成

[従来の Azure ポータル](https://manage.windowsazure.com/)で、Azure Stream Analytics サービスを選択し、新しい Stream Analytics ジョブを作成します。


![](./media/app-insights-code-sample-export-sql-stream-analytics/37-create-stream-analytics.png)



![](./media/app-insights-code-sample-export-sql-stream-analytics/38-create-stream-analytics-form.png)

新しいジョブが作成された後、その詳細を展開します。

![](./media/app-insights-code-sample-export-sql-stream-analytics/41-sa-job.png)

#### BLOB の場所の設定

連続エクスポート BLOB から入力を取るよう設定します。

![](./media/app-insights-code-sample-export-sql-stream-analytics/42-sa-wizard1.png)

ここで、Storage アカウントからのプライマリ アクセス キーが必要になります。これは前にメモしておいたものです。Storage アカウント キーとしてこれを設定します。

![](./media/app-insights-code-sample-export-sql-stream-analytics/46-sa-wizard2.png)

#### パスのプレフィックス パターンの設定 

![](./media/app-insights-code-sample-export-sql-stream-analytics/47-sa-wizard3.png)

日付の書式は YYYY-MM-DD (ダッシュ付き) に設定してください。

パスのプレフィックス パターンは、Stream Analytics がストレージ内の入力ファイルを検索する方法を指定します。連続エクスポートによるデータ格納方法と一致するようセットアップする必要があります。次のように設定します。

    webapplication27_100000000-0000-0000-0000-000000000000/PageViews/{date}/{time}

次の点に注意してください。

* `webapplication27` は Application Insights リソースの名前です。 
* `1000...` は Application Insights リソースのインストルメンテーション キーです。 
* `PageViews` は分析するデータの種類です。使用可能な種類は、連続エクスポートで設定するフィルターによって異なります。エクスポートされたデータを調べ、その他の使用可能な種類をご確認ください。
* `/{date}/{time}` はそのまま書き込まれるパターンです。

Application Insights リソースの名前と iKey を取得するには、概要ページの [Essentials] を開くか、[設定] を開きます。

#### 初期セットアップの完了

シリアル化の書式を確認します。

![[確認]をクリックしてウィザードをして閉じます](./media/app-insights-code-sample-export-sql-stream-analytics/48-sa-wizard4.png)

ウィザードを閉じ、セットアップが完了するまで待機します。

## クエリの設定

クエリ セクションを開きます。

![ストリーム分析で、[クエリ] を選択します](./media/app-insights-code-sample-export-sql-stream-analytics/51-query.png)

既定のクエリを次のもので置き換えます。

```SQL

    SELECT flat.ArrayValue.name as pageName
	, flat.ArrayValue.count as viewCount
	, flat.ArrayValue.url as url
	, flat.ArrayValue.urlData.port as urlDataPort
	, flat.ArrayValue.urlData.protocol as urlDataprotocol
	, flat.ArrayValue.urlData.host as urlDataHost
	, flat.ArrayValue.urlData.base as urlDataBase
	, flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOuput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

最初のいくつかのプロパティはページ ビュー データに固有のプロパティです。他のテレメトリの種類のエクスポートにはそれぞれ異なるプロパティがあります。

## データベースへの出力のセットアップ

SQL を出力として選択します。

![ストリーム分析で、[出力] を選択します](./media/app-insights-code-sample-export-sql-stream-analytics/53-store.png)

SQL データベースを指定します。


![データベースの詳細を入力します](./media/app-insights-code-sample-export-sql-stream-analytics/55-output.png)

ウィザードを閉じ、出力がセットアップされたことを示す通知を待機します。

## 処理の開始

ジョブを操作バーから開始します。

![ストリーム分析で、[開始] をクリックします](./media/app-insights-code-sample-export-sql-stream-analytics/61-start.png)

データの処理を今から開始するか、以前のデータを使って開始するかを選択できます。連続エクスポートの実行を開始してから既に時間が経過している場合は、後者がお勧めです。


![ストリーム分析で、[開始] をクリックします](./media/app-insights-code-sample-export-sql-stream-analytics/63-start.png)

数分後、SQL Server Management Tools に戻り、流れているデータを監視します。たとえば、次のようなクエリを使用します。

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## 関連記事:

* [worker ロールを使用して SQL にエクスポートする](app-insights-code-sample-export-telemetry-sql-database.md)
* [Application Insights での連続エクスポート](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-get-started.md

 

<!---HONumber=August15_HO7-->