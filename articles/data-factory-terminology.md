<properties 
	pageTitle="Azure のデータのファクトリの用語" 
	description="この記事で、Azure のデータのファクトリのサービスを使用してデータのファクトリの作成に使われる用語を紹介します。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

#Azure のデータのファクトリの用語

## データ ファクトリ
 **Azure データ ファクトリ** が Azure の HDInsight などのリンクのコンピューティング サービスを使用してリンクされているデータ ストア (Azure のストレージ、Azure SQL データベース、内部設置型 SQL Server など) のデータを処理する 1 つまたは複数のパイプライン。Azure データ ファクトリ自体にはデータは含まれません。データは前に説明したデータ ストアに格納されます。

## リンクされたサービス
A **サービス リンク** 、Azure のデータのファクトリにリンクされているサービスです。リンクされたサービスは、次のいずれかです。

- A **データ記憶域** など、Azure のストレージ、Azure SQL データベースまたは内部設置型 SQL Server データベース サービスです。データ ストアは、入力データ セットや出力データ セットのコンテナーです。    
- A **コンピューティング** など、Azure の HDInsight、Azure の機械学習、およびバッチの Azure サービスです。コンピューティング サービスでは、入力データを処理し、出力データを生成します。  

## データ セット
A **データ セット** データの名前付きのビューです。記述されるデータは、単純なバイト、CSV ファイルのような半構造化データから、リレーショナル テーブルやモデルまでさまざまです。データのファクトリ **テーブル** データ セットを持ち、スキーマが長方形です。あるデータ ストアを参照するデータ ストア内にリンクされたサービスを作成した後に、そのデータ ストアに格納される入力データや出力データを表現するデータ セットを定義します。


##パイプライン
A **パイプライン** 、Azure でデータ ファクトリ リンクのコンピューティング サービスを使用してリンクされたストレージ サービスでのデータを処理します。パイプラインは、一連のアクティビティで構成されます。各アクティビティは、特定の処理操作を実行します。たとえば、 **コピー アクティビティ** データ ソース、記憶域を移行先のストレージにコピーと **HDInsight アクティビティ** Hive クエリまたは Pig スクリプトを使用してデータを処理するには、Azure の HDInsight クラスターを使用します。データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。

Azure Data Factory インスタンスを作成するための一般的な手順は、次のとおりです。

1. 作成、 **データ ファクトリ**です。
2. 作成、 **サービス リンクされている** 各データの格納またはサービスを計算します。
3. 入力と出力を作成する **データセット**です。
4. 作成、 **パイプライン**です。 

##アクティビティ
パイプラインにおけるデータ処理の手順です。1 つ以上の入力データセットを受け取り、1 つ以上の出力データセットを生成します。アクティビティ実行環境で実行 (例: Azure の HDInsight クラスター) と、Azure のデータのファクトリに関連付けられているおよびリンクのデータ ストアにデータの読み取り/書き込みです。

Azure Data Factory サービスは、パイプライン内の次のアクティビティをサポートします。

- **コピー アクティビティ** 、データをデータ ストアから別のデータ ストアにコピーします。参照してください [Azure のデータのファクトリでのデータをコピー][copy-data-with-adf] データ コピーのアクティビティを格納するための詳細をサポートしています。 
- **HDInsight アクティビティ** 、HDInsight クラスターでの Hive と Pig スクリプトまたは MapReduce プログラムを実行してデータを処理します。参照してください [の Pig の使用、およびデータのファクトリでの Hive][use-pig-hive] と [データ ファクトリからの MapReduce プログラムの起動][run-map-reduce] の詳細。 
- **Azure マシン ラーニング バッチ スコアリング アクティビティ** API のスコアリング Azure 機械学習のバッチを呼び出します。参照してください [予測のパイプラインを作成する Azure のデータのファクトリと Azure の機械学習を使用して][azure-ml-adf] の詳細。 
- **ストアド プロシージャのアクティビティ** 、Azure SQL データベース内のストアド プロシージャを呼び出します。参照してください、 [ストアド プロシージャのアクティビティ][msdn-stored-procedure-activity] 詳細については、MSDN ライブラリでします。   

##スライス
Azure データ ファクトリのテーブルは、スライスで構成されます。スライスの幅は、スケジュール (毎時/毎日) によって決まります。スケジュールが "毎時" のときは、パイプラインの開始時刻から終了時刻までに、スライスが 1 時間ごとに作成されます。たとえば、パイプラインの開始日時が 2015 年 3 月 3 日 6 時 0 分 0 秒 (午前 6 時)、終了日時が 2015 年 3 月 3 日 9 時 0 分 0 秒 (同じ日の午前 9 時) の場合、1 時間間隔で、午前 6 ～ 7 時、午前 7 ～ 8 時、午前 8 ～ 9 時の 3 つのデータ スライスが生成されます。

スライスは、特定の時間枠の全体的なデータのサブセットを操作する機能を提供 (例: 期間 (時間) の生成されるスライス: 1時 00分 PM を午後 2 時)。

## スライスに対するアクティビティ実行
 **実行** または実行するアクティビティが、スライスの処理の単位です。再試行する場合や、エラー発生時にスライスを再実行する場合には、1 つのスライスを 1 回以上実行することもあります。スライスは、開始時刻によって識別されます。

##Data Management Gateway
Microsoft **Data Management Gateway** は内部設置型のデータ ソースを使用するためのサービスをクラウドに接続されているソフトウェア。内部設置型のデータ ソースをリンクされたサービスとして追加するには、企業環境に少なくとも 1 つのゲートウェイをインストールし、Azure Data Factory ポータルに登録する必要があります。
 
##データ ハブ
A **Data Hub** データ ストレージとコンピューティング サービスの論理コンテナーです。たとえば、ストレージでは HDFS を使用する Hadoop クラスターがデータ ハブで、コンピューティング (処理) では Hive/Pig がデータ ハブです。同様に、エンタープライズ データ ウェアハウス (EDW) をデータ ハブとしてモデル化できます (ストレージとしてはデータベース、コンピューティング サービスとしてはストアド プロシージャや ETL ツール)。パイプラインは、データ ハブ内でデータ ストアを使用し、コンピューティング リソースを実行します。現時点でサポートされているのは、HDInsight ハブのみです。

データ ハブを使用して、Data Factory を論理的なグループやドメイン別のグループに分割できます。たとえば、"米国西部 Azure ハブ" を作成して、米国西部データ センター向けのリンクされたサービス (データ ストアとコンピューティング) とパイプラインを管理したり、"営業部門 EDW ハブ" を作成して、営業部門エンタープライズ データ ウェアハウスのデータの入力と処理に関連するすべてのリンクされたサービスとパイプラインを管理したりできます。

ハブの重要な特性は、パイプラインが 1 つのハブで実行されることです。つまり、パイプラインを定義する際、パイプライン内のテーブルまたはアクティビティによって参照されるすべてのリンクされたサービスには、パイプライン自体と同じハブ名を設定する必要があります。リンクされたサービスに HubName プロパティが指定されていない場合、リンクされたサービスは "Default" ハブに配置されます。

## 関連項目

1. [ファクトリの Azure データの概要][adf-intro]です。この記事では、Azure Data Factory サービス、もたらされる価値、およびサポートしているシナリオの概要について説明します。
2. [データ ファクトリの][datafactory-getstarted]です。リンク先の記事では、Azure BLOB から Azure SQL データベースにデータをコピーする Azure Data Factory サンプルの作成方法についての詳細なチュートリアルが記載されています。


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-intro]: data-factory-introduction.md
[copy-data-with-adf]: data-factory-copy-activity.md
[use-pig-hive]: data-factory-pig-hive-activities.md
[run-map-reduce]: data-factory-map-reduce.md
[azure-ml-adf]: data-factory-create-predictive-pipelines.md
[adf-common-scenarios]: data-factory-common-scenarios.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[data-factory-editor]: data-factory-editor.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx


[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]: ./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]: ./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]: ./media/data-factory-introduction/DataFactoryDataFlow.png

<!---HONumber=GIT-SubDir-->