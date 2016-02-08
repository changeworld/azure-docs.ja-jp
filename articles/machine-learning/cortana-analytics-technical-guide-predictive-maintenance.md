<properties
	pageTitle="航空宇宙などの業務における予測メンテナンスのための Cortana Analytics Solution Template に関する技術ガイド |Microsoft Azure"
	description="航空宇宙、公益事業、および輸送業界における予測メンテナンスのための Microsoft Cortana Analytics によるソリューション テンプレートに関する技術ガイドです。"
	services="cortana-analytics"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cortana-analytics"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/20/2016"
	ms.author="garye" />

# 航空宇宙などの業務における予測メンテナンスのための Cortana Analytics Solution Template に関する技術ガイド

## **概要**

ソリューション テンプレートは、Cortana Analytics Suite に基づいて、エンドツー エンドのデモを構築するプロセスを促進するために設計されています。デプロイされたテンプレートは、必要な Cortana Analytics コンポーネントのサブスクリプションをプロビジョニングし、それらの関係を構築します。さらに、ソリューション テンプレートのデプロイ後に、ローカル コンピューターにダウンロードされ、インストールされたデータ シミュレーション アプリケーションから生成されたサンプル データをデータ パイプラインに準備します。シミュレーターから生成されたデータはデータ パイプラインに取り込まれ、Machine Learning の予測の生成が開始され、それらを Power BI ダッシュボードに表示することができます。デプロイメント プロセスでは、ソリューション資格情報を設定するためのいくつかの手順を進みます。デプロイメント時に指定したソリューション名、ユーザー名、パスワードなどの資格情報を記録しておくようにしてください。

このドキュメントの目的は、このソリューション テンプレートの一部として、サブスクリプションでプロビジョニングされる参照アーキテクチャとさまざまなコンポーネントについて説明することです。さらに、独自のデータから洞察と予測を表示できるように、サンプル データを実際のデータと置き換える方法についても説明します。また、独自のデータによってソリューションをカスタマイズする必要がある場合に、変更する必要があると考えられるソリューション テンプレートの部分についても説明します。このソリューション テンプレートの Power BI ダッシュボードを構築する方法については、最後に説明します。

>[AZURE.TIP] [このドキュメントの PDF バージョン](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf)をダウンロードして、印刷できます。

## **全体像**

![](media/cortana-analytics-technical-guide-predictive-maintenance\predictive-maintenance-architecture.png)

ソリューションをデプロイすると、Cortana Analytics Suite 内のさまざまな Azure サービス (*つまり*、 Event Hub、Stream Analytics、HDInsight、Data Factory、Machine Learning *など*) がアクティブ化されます。上のアーキテクチャ図は、エンド ツー エンドの航空宇宙ソリューション テンプレートの予測メンテナンスの構築方法を大まかに示しています。これらのサービスを調査するには、ソリューションのデプロイメントによって作成されたソリューション テンプレート図で、それらをクリックしてください。[図のフルサイズ バージョン](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png)をダウンロードできます。

次のセクションでは、各部分について説明します。

## **データ ソースと取り込み**

### 合成データソース

このテンプレートでは、デプロイメントの成功後にローカルにダウンロードして、実行するデスクトップ アプリケーションから、使用するデータ ソースが生成されます。このアプリケーションをダウンロードして、インストールする手順については、ソリューション テンプレート図の予測メンテナンス データ シミュレーターと呼ばれる最初のノードを選択すると、プロパティ バーに表示されます。このアプリケーションは、[Azure Event Hub](#azure-event-hub) サービスに、ソリューション フローの残りで使用されるデータ ポイント、またはイベントを提供します。このデータ ソースは、[Turbofan Engine Degradation Simulation Data Set (ターボファン エンジンの劣化シミュレーション データ セット)](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) を使用して、[NASA データ リポジトリ](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/)で公開されているデータから構成または派生したものです。

イベント生成アプリケーションは、コンピューターで実行中の場合にのみ、Azure Event Hub にデータを入力します。

### Azure Event Hub

[Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) サービスは、上記の合成データ ソースによって提供される入力の受け取り側です。

## **データの準備と分析**


### Azure Stream Analytics

[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) サービスを使用して、[Azure Event Hub](#azure-event-hub) サービスからの入力ストリームをほぼリアルタイムで分析し、結果を [Power BI](https://powerbi.microsoft.com) ダッシュボードに公開するほか、後で [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) サービスで処理するために、未加工のすべての受信イベントを [Azure Storage](https://azure.microsoft.com/services/storage/) サービスにアーカイブできます。

### HD Insights カスタム集計

Azure HD Insight サービスを使用して、[Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプト (Azure Data Factory によって調整される) を実行し、Azure Stream Analytics サービスによってアーカイブされた未加工のイベントの集計を行います。

### Azure Machine Learning

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) サービスを使用して (Azure Data Factory によって調整される)、受け取った入力から、特定の航空機エンジンの残存耐用年数 (RUL) を予測します 。

## **データの公開**


### Azure SQL Database サービス

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) サービスを使用して、[Power BI](https://powerbi.microsoft.com) ダッシュボードで使用される Azure Machine Learning サービスが受け取った予測を保存します (Azure Data Factory によって管理される)。

## **データの使用**

### Power BI

[Power BI](https://powerbi.microsoft.com) サービスを使用して、[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) サービスによって提供される集計とアラートのほか、[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) サービスを使用して生成され、[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) に保存された RUL 予測を記載するダッシュボードを表示します。このソリューション テンプレートの Power BI ダッシュボードを構築する方法については、次のセクションを参照してください。

## **独自のデータを取り込む方法**

このセクションでは、Azure に独自のデータを取り込む方法およびこのアーキテクチャに取り込むデータに応じて変更が必要になりそうな領域について説明します。

取り込むデータセットが、このソリューション テンプレートで使用されている [Turbofan Engine Degradation Simulation Data Set (ターボファン エンジンの劣化シミュレーション データ セット)](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) に含まれるデータセットに一致している可能性はほとんどないでしょう。自分のデータと要件を理解することは、独自のデータで動作するように、このテンプレートを変更する方法においてきわめて重要です。Azure Machine Learning サービスを初めて使用する場合は、[初めての実験を作成する方法](machine-learning-create-experiment.md)の例を使用して、概要を把握することができます。

以降のセクションでは、新しいデータセットを導入したときに変更が必要となるテンプレートのセクションについて説明します。

### Azure Event Hub

Azure Event Hub サービスはきわめて汎用的で、データを CSV または JSON 形式でハブに投稿できます。Azure Event Hub では特別な処理は行われませんが、それに入力されるデータを理解することが重要です。

このドキュメントでは、データを取り込む方法について説明しませんが、Event Hub API を使用して、イベントやデータを Azure Event Hub に簡単に送信できます。

### Azure Stream Analytics

Azure Stream Analytics サービスを使用して、データ ストリームから読み取り、任意の数のソースにデータを出力することによって、ほぼリアルタイムで分析を行います。

航空宇宙ソリューション テンプレートの予測メンテナンスの場合、Azure Stream Analytics クエリは、それぞれ Azure Event Hub サービスからのイベントを使用し、4 つの異なる場所に出力する 4 つのサブ クエリから構成されます。これらの出力は、3 つの Power BI データセットと 1 つの Azure Storage の場所から構成されます。

Azure Stream Analytics クエリは次によって見つけることができます。

-   Azure ポータルにログインする

-   ソリューションがデプロイされたときに生成された Stream Analytics ジョブ ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-stream-analytics.png) (*例:* 予測メンテナンス ソリューションの **maintenancesa02asapbi** および **maintenancesa02asablob** ) を見つける

-   次を選択する

    -   入力クエリを表示する場合は ***[入力]***

    -   クエリ自体を表示する場合は ***[クエリ]***

    -   別の出力を表示する場合は ***[出力]***

Azure Stream Analytics クエリの構築については、MSDN の [Stream Analytics クエリ リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)を参照してください。

このソリューションでは、クエリは、このソリューション テンプレートの一部として提供されている Power BI ダッシュボードへの受信データ ストリームに関するリアルタイムの分析情報を含む 3 つのデータセットを出力します。受信データ形式に関する暗黙的知識があるため、これらのクエリはデータ形式に基づいて、変更が必要になる場合があります。

2 番目の Stream Analytics ジョブ **maintenancesa02asablob** のクエリは、すべての [Event Hub](https://azure.microsoft.com/services/event-hubs/) イベントを [Azure Storage](https://azure.microsoft.com/services/storage/) に出力するだけです。完全なイベント情報が記憶域にストリームされるため、データ形式に関係なく変更を必要としません。

### Azure Data Factory

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) サービスは、データの移動や処理を調整します。航空宇宙ソリューション テンプレートの予測メンテナンスでは、さまざまなテクノロジを使用してデータを移動し、処理する 3 つの [パイプライン](../data-factory/data-factory-create-pipelines.md)からデータ ファクトリが構成されます。データ ファクトリにアクセスするには、ソリューションのデプロイメントよって作成されたソリューション テンプレート図の下部にある Data Factory ノードを開きます。これにより、Azure ポータルのデータ ファクトリに移動します。データセットの下にエラーが表示された場合、それらはデータ ファクトリのデータ ジェネレーターが起動する前に、データ ファクトリがデプロイされていることが原因であるため、無視できます。これらのエラーによって、データ ファクトリの機能が妨げられることはありません。

![](media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

このセクションでは、[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) に含まれる必要な[パイプライン](../data-factory/data-factory-create-pipelines.md)と[アクティビティ](../data-factory/data-factory-create-pipelines.md)について説明します。ソリューションの図のビューを以下に示します。

![](media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

このファクトリの 2 つのパイプラインには、データのパーティション分割と集計に使用される [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトが含まれます。このスクリプトは、セットアップ時に作成される [Azure Storage](https://azure.microsoft.com/services/storage/) アカウントに置かれます。それらの場所は maintenancesascript\\\script\\\hive\\\ (または https://[Your solution name].blob.core.windows.net/maintenancesascript) になります。

[Azure Stream Analytics](#azure-stream-analytics-1) クエリと同様に、[Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトは、受信データ形式に関して暗黙的に認識するため、これらのクエリをデータ形式と[特徴エンジニアリング](machine-learning-feature-selection-and-engineering.md)要件に基づいて変更する必要がある場合があります。

#### *AggregateFlightInfoPipeline*

この[パイプライン](../data-factory/data-factory-create-pipelines.md)には、1 つのアクティビティが含まれます。それは、[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) ジョブの実行時に、[Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトを実行して、[Azure Storage](https://azure.microsoft.com/services/storage/) に入れられるデータをパーティション分割する [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) を使用する [HDInsightHive](../data-factory/data-factory-hive-activity.md) アクティビティです。

このパーティション分割タスク用の [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトは ***AggregateFlightInfo.hql*** です。

#### *MLScoringPipeline*

この[パイプライン](../data-factory/data-factory-create-pipelines.md)には、複数のアクティビティが含まれ、それらの最終結果は、このソリューション テンプレートに関連付けられている [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) の実験からのスコア付けされた予測です。

これに含まれるアクティビティは次のとおりです。

-   [HDInsightHive](../data-factory/data-factory-hive-activity.md) アクティビティ: [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトを実行して、[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) の実験に必要な集計と特徴エンジニアリングを実行する [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) を使用します。このパーティション分割タスク用の [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) スクリプトは ***PrepareMLInput.hql*** です。

-   [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) アクティビティ: [HDInsightHive](../data-factory/data-factory-hive-activity.md) アクティビティからの結果を、[AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) アクティビティによってアクセス可能な 1 つの [Azure Storage](https://azure.microsoft.com/services/storage/) BLOB に移動します。

-   [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) アクティビティ: 1 つの [Azure Storage](https://azure.microsoft.com/services/storage/) BLOB に結果が配置されることになる [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) の実験を呼び出します。

#### *CopyScoredResultPipeline*

この[パイプライン](../data-factory/data-factory-create-pipelines.md)には 1 つのアクティビティが含まれます。それは、[Azure Machine Learning](#azure-machine-learning) の実験の結果を ***MLScoringPipeline*** から、ソリューション テンプレートのインストールの一部としてプロビジョニングされた [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) に移動する [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) アクティビティです。

### Azure Machine Learning

このソリューション テンプレートで使用されている [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) の実験は、航空機のエンジンの残存耐用年数 (RUL) を提供します。実験は使用されるデータ セットに固有であるため、取り込まれるデータに固有の変更や置換が必要になります。

Azure Machine Learning の実験の作成方法については、「[Predictive Maintenance: Step 1 of 3, data preparation and feature engineering (予測メンテナンス: ステップ 1/3、データの準備と特徴エンジニアリング)](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)」を参照してください。

## **進行状況の監視**
 データ ジェネレーターを起動すると、データを取り込むパイプラインが開始され、Data Factory によって発行されたコマンドに従ってソリューションのさまざまなコンポーネントがアクションを開始します。パイプラインを監視する 2 つの方法があります。

1. いずれかの Stream Analytics ジョブが、未加工の受信データを BLOB ストレージに書き込みます。ソリューションが正常にデプロイされた画面からソリューションの Blob Storage コンポーネントをクリックし、右側のパネルの [開く] をクリックすると、[管理ポータル](https://portal.azure.com/)に移動します。管理ポータルで、BLOB をクリックします。次のパネルに、コンテナーの一覧が表示されます。**maintenancesadata** をクリックします。次のパネルに、**rawdata** フォルダーが表示されます。rawdata フォルダーの中に、hour=17 や hour=18 などのフォルダーが表示されます。これらのフォルダーの表示は、生データがコンピューター上に正常に生成され、BLOB ストレージに格納されたことを示します。これらのフォルダーの中に、有限サイズ (MB 単位) の csv ファイルがあります。

2. パイプラインの最後の手順は、データ (MachineLearning からの予測など) を SQL Database に書き込むことです。データが SQL Database に表示されるまで、最大 3 時間の待機が必要な場合があります。SQL Database で使用できるデータの量を監視する 1 つの方法は、[Azure ポータル](https://manage.windowsazure.com/)を使用することです。左側のパネルで、SQL DATABASES ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-SQL-databases.png) を見つけてそれをクリックします。次に、データベース **pmaintenancedb** を見つけてそれをクリックします。次のページの下部にある [管理] をクリックします。

	![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-manage.png)

	ここで、[新しいクエリ] をクリックし、行数をクエリできます (例: select count(*) from PMResult)。データベースが大きくなれば、テーブル内の行数も増加します。


## **Power BI ダッシュボード**

### 概要

このセクションでは、 Azure Stream Analytics (ホット パス) からのリアルタイムのデータのほか、Azure Machine Learning (コールド パス) からのバッチ予測結果を表示する Power BI ダッシュボードの設定方法について説明します。

### コールド パス ダッシュボードの設定

コールド パス データ パイプラインでの重要な目標は、フライト (サイクル) の終了時に、各航空機エンジンの予測 RUL (残存耐用年数) を取得することです。過去 3 時間以内にフライトを終了した航空機のエンジンを予測するために、予測結果は 3 時間ごとに更新されます。

Power BI は、そのデータ ソースとして、予測結果が格納されている Azure SQL データベースに接続します。注: 1) ソリューションのデプロイ時、実際の予測は 3 時間以内にデータベース内に現れます。ジェネレーターのダウンロードに付属する pbix ファイルには、Power BI ダッシュボードをすぐに作成できるようにシード データが含まれています。2) この手順の前提条件は、無料のソフトウェア [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/) をダウンロードしてインストールしていることです。

次の手順では、ソリューションのデプロイメント時に起動され、表示用のデータ (*例:* 予測結果) を格納する SQL Database に pbix ファイルを接続する方法について説明します。

1.  データベースの資格情報を取得します。

    次の手順に進む前に、**データベース サーバー名、データベース名、ユーザー名、およびパスワード**が必要です。以下の手順で、それらを見つける方法を説明します。

    -   ソリューション テンプレート図の **[Azure SQL Database]** が緑色に変わったら、それをクリックし、**[開く]** をクリックします。

    -   新しいブラウザー タブ/ウィンドウが表示され、Azure ポータル ページが表示されます。左側のパネルの **[リソース グループ]** をクリックします。

    -   ソリューションのデプロイに使用しているサブスクリプションを選択し、**[YourSolutionName\_ResourceGroup]** を選択します。

    -   新しいポップアップ パネルで、![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-sql.png) アイコンをクリックして、データベースにアクセスします。データベース名はこのアイコンの横にあり (*例:* **'pmaintenancedb'**)、**データベース サーバー名**は、サーバー名のプロパティの下に **YourSoutionName.database.windows.net** のように表示されます。

	-   データベースの**ユーザー名**と**パスワード**は、ソリューションのデプロイメント時に記録しておいたユーザー名とパスワードと同じです。

2.  Power BI Desktop でコールド パス レポート ファイルのデータ ソースを更新します。

    -   ジェネレーター ファイルをダウンロードして解凍した PC のフォルダー内の **PowerBI\\PredictiveMaintenanceAerospace.pbix** ファイルをダブルクリックします。ファイルを開くときに警告メッセージが表示された場合、それらを無視します。ファイルの先頭の **[クエリを編集]** をクリックします。

	    ![](media\cortana-analytics-technical-guide-predictive-maintenance\edit-queries.png)

	-	2 つのテーブル **[RemainingUsefulLife]** と **[PMResult]** があります。最初のテーブルを選択し、右側の **[クエリの設定]** パネルの **[適用したステップ]** の **[ソース]** の横の ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-query-settings.png) をクリックします。表示される警告メッセージは無視します。

    -   ポップアップ ウィンドウの **[サーバー]** と **[データベース]** を独自のサーバーとデータベースの名前に置き換えて、**[OK]** をクリックします。サーバー名の場合、ポート 1433 (**YourSoutionName.database.windows.net 1433**) を指定していることを確認してください。画面に表示される警告メッセージは無視します。

    -   次のポップアップ ウィンドウで、左側のウィンドウに 2 つのオプション (**[Windows]** と **[データベース]**) が表示されます。**[データベース]** をクリックし、**[ユーザー名]** と **[パスワード]** (これは、初めてソリューションをデプロイし、Azure SQL Database を作成したときに入力したユーザー名とパスワードです) を入力します。***[これらの設定の適用対象レベルの選択]*** で、データベース レベル オプションをオンにします。次に **[接続]** をクリックします。

    -   2 番目のテーブル **[PMResult]** をクリックし、右側の **[クエリの設定]** パネルの **[適用したステップ]** の **[ソース]** の横の ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-navigation.png) をクリックし、上記の手順と同様に、サーバーとデータベース名を更新して、[OK] をクリックします。

    -   前のページに戻ったら、ウィンドウを閉じます。メッセージが表示されます。**[適用]** をクリックします。最後に、**[保存]** ボタンをクリックして、変更を保存します。これで、Power BI ファイルは、サーバーへの接続を確立しました。視覚エフェクトが空の場合、凡例の右上隅にある消しゴム アイコンをクリックして、視覚エフェクトの選択をクリアし、すべてのデータを表示します。更新ボタンを使用して、視覚エフェクトに新しいデータを反映させます。最初、視覚エフェクトにはシード データのみ表示されます。データ ファクトリは 3 時間ごとに更新されるようにスケジュールされています。3 時間後、データを更新すると、視覚エフェクトに反映された新しい予測が表示されます。

3.  (省略可能) コールド パス ダッシュボードを [Power BI オンライン](http://www.powerbi.com/)に公開します。この手順では、Power BI アカウント (または Office 365 アカウント) が必要であることに注意してください。

    -   **[公開]** をクリックします。数秒後、緑色のチェック マークの付いた 「Power BI への公開が成功しました」と表示するウィンドウが表示されます。"Power BI で PredictiveMaintenanceAerospace.pbix を開く" の下のリンクをクリックします。詳細な手順については、「[Power BI Desktop からの発行](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop)」を参照してください。

    -   新しいダッシュボードを作成するには、左側のウィンドウで **[ダッシュ ボード]** の横の**[+]** 記号をクリックします。この新しいダッシュボードの名前として、「Predictive Maintenance Demo」と入力します。

    -   レポートが開いたら、![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-pin.png) をクリックして、すべての視覚エフェクトをダッシュボードにピン留めします。詳細な手順については、「[レポートから Power BI ダッシュボードにタイルをピン留め](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report)」を参照してください。ダッシュボード ページに移動し、視覚エフェクトのサイズと場所を調整し、タイルを編集します。タイルを編集する方法の詳細については、「[タイルの編集 -- サイズ変更、移動、名前の変更、ピン留め、削除、ハイパーリンクの追加](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename)」を参照してください。いくつかのコールド パス視覚エフェクトがピン留めされたサンプル ダッシュボードを次に示します。データ シミュレーターの実行時間に応じて、視覚エフェクト上の数値は異なる可能性があります。<br/>![](media\cortana-analytics-technical-guide-predictive-maintenance\final-view.png)<br/>
    -   データの更新をスケジュールするには、**[PredictiveMaintenanceAerospace]** データセットの上にマウス ポインターを移動し、![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-elipsis.png) をクリックし、**[更新のスケジュール設定]** を選択します。<br/> **注:** 警告メッセージが表示された場合は、**[資格情報の編集]** をクリックし、データベース資格情報が、手順 1 で説明したものと同じかどうかを確認します。<br/> ![](media\cortana-analytics-technical-guide-predictive-maintenance\schedule-refresh.png) <br/>
    -   **[更新のスケジュール設定]** セクションを展開します。[データを最新の状態に保つ] をオンにします。<br/>
    -   必要に応じて更新をスケジュールします。詳細については、「[Power BI でのデータの更新](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi)」を参照してください。

### ホット パス ダッシュボードの設定

次の手順では、ソリューションのデプロイメント時に生成された Stream Analytics ジョブからのリアルタイム データの出力を表示する方法について説明します。次の手順を実行するには、[Power BI オンライン](http://www.powerbi.com/) アカウントが必要です。アカウントがない場合は、[作成](https://powerbi.microsoft.com/pricing)できます。

1.  Azure Stream Analytics (ASA) に Power BI 出力を追加します。

    -  Azure Stream Analytics ジョブの出力を Power BI ダッシュボードとして設定するには、「[Azure Stream Analytics & Power BI: ストリーミング データをリアルタイムで表示するリアルタイム分析ダッシュボード](stream-analytics-power-bi-dashboard.md)」の手順に従う必要があります。
	- [Azure ポータル](https://manage.windowsazure.com)で、Stream Analytics ジョブ **maintenancesa02asapbi** を見つけます。
	- ASA クエリの 3 つの出力 **aircraftmonitor**、**aircraftalert**、および **flightsbyhour** を設定します。**出力の別名**、**データセット名**、および**テーブル名**が、クエリと同じ (**aircraftmonitor**、**aircraftalert**、および **flightsbyhour**) であることを確認します。3 つすべての出力テーブルを追加し、Stream Analytics ジョブを開始すると、確認メッセージ (*例:* "ジョブ maintenancesa02asapbi を開始しています") が表示されます。

2. [Power BI オンライン](http://www.powerbi.com)にログインします。

    -   左側のパネルの [個人用ワークスペース] の [データセット] セクションに、ASA ジョブの Power BI 出力設定で定義した ***データセット***名 (**aircraftmonitor**、**aircraftalert**、および **flightsbyhour**) が表示されます。

    -   ***[視覚エフェクト]*** ウィンドウが開き、画面の右側に表示されることを確認します。

3. "Fleet View of Sensor 11 vs.Threshold 48.26" タイルを作成します。

    -   左側のパネルの [データセット] セクションで、データセット **aircraftmonitor** をクリックします。

    -   **[折れ線グラフ]** アイコンをクリックします。

    -   **[視覚エフェクト]** ウィンドウの [軸] の下に表示されるように、**[フィールド]** ウィンドウの **[処理済み]** をクリックします。

    -   どちらも [値] の下に表示されるように、[s11] と [s11\_alert] をクリックします。**[s11]** と **[s11\_alert]** の横にある小さな矢印をクリックして、[合計] を [平均] に変更します。

    -   上部の **[保存]** をクリックし、レポートに "aircraftmonitor" という名前を付けます。左側の **[ナビゲーター]** ウィンドウの **[レポート]** セクションに、"aircraftmonitor" という名前のレポートが表示されます。

    -   折れ線グラフの右上隅の **[ビジュアルをピン留めする]** アイコンをクリックします。ダッシュボードを選択する [ダッシュボードにピン留めする] ウィンドウが表示されることがあります。[Predictive Maintenance Demo] を選択し、[ピン留め] をクリックします。

    -   ダッシュボードのこのタイルにマウス ポインターを置き、右上隅の [編集] アイコンをクリックし、そのタイトルを「Fleet View of Sensor 11 vs.Threshold 48.26」に変更し、サブタイトルを「Average across fleet over time」に変更します。

4.  適切なデータセットに基づいて他のダッシュボード タイルを作成します。いくつかのホット パス視覚エフェクトがピン留めされているサンプル ダッシュボードを次に示します。データ シミュレーターの実行時間に応じて、視覚エフェクトの番号が異なる場合があります。

    ![](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

## **コスト見積もりツール**

各自のサブスクリプションで航空宇宙ソリューション テンプレートの予測メンテナンスを実行する場合に関連する合計コストを詳しく知るために役立つ次の 2 つのツールがあります。

-   [Microsoft Azure 料金計算ツール (オンライン)](https://azure.microsoft.com/pricing/calculator/)

-   [Microsoft Azure 料金計算ツール (デスクトップ)](http://www.microsoft.com/download/details.aspx?id=43376)

<!---HONumber=AcomDC_0128_2016-->