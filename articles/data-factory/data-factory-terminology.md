<properties 
	pageTitle="Azure Data Factory - 用語" 
	description="この記事では、Azure Data Factory サービスで Data Factory を作成する際に使われる用語を紹介します。" 
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
	ms.date="08/25/2015" 
	ms.author="spelluru"/>

#Azure Data Factory - 用語

## Data Factory  

**Azure Data Factory** には、Azure HDInsight などのリンクされたコンピューティング サービスを使用して、リンクされたデータ ストア (Azure Storage、Azure SQL Database、オンプレミスの SQL Server など) のデータを処理するパイプラインが 1 つ以上あります。Azure データ ファクトリ自体にはデータは含まれません。データは前に説明したデータ ストアに格納されます。

## リンクされたサービス
**リンクされたサービス**とは、Azure Data Factory にリンクされているサービスです。リンクされたサービスは、次のいずれかです。

- Azure Storage、Azure SQL Database、オンプレミスの SQL Server データベースなどの**データ ストレージ** サービス。データ ストアは、入力データ セットや出力データ セットのコンテナーです。    
- Azure HDInsight、Azure Machine Learning、Azure Batch などの**コンピューティング** サービス。コンピューティング サービスでは、入力データを処理し、出力データを生成します。  

## データ セット
**データ セット**とは、データの名前付きビューです。記述されるデータは、単純なバイト、CSV ファイルのような半構造化データから、リレーショナル テーブルやモデルまでさまざまです。Data Factory **テーブル**は、スキーマを持つ四角形のデータ セットです。あるデータ ストアを参照するデータ ストア内にリンクされたサービスを作成した後に、そのデータ ストアに格納される入力データや出力データを表現するデータ セットを定義します。


##パイプライン
Azure Data Factory の**パイプライン**は、リンクされたコンピューティング サービスを使用して、リンクされたストレージ サービス内のデータを処理します。パイプラインは、一連のアクティビティで構成されます。各アクティビティは、特定の処理操作を実行します。たとえば、**コピー アクティビティ**は、ソース ストレージからターゲット ストレージにデータをコピーします。**HDInsight アクティビティ**は、Azure HDInsight クラスターを使用して、Hive クエリや Pig スクリプトによるデータ処理を実行します。データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。

Azure Data Factory インスタンスを作成するための一般的な手順は、次のとおりです。

1. **Data Factory  
**を作成する。
2. データ ストアまたはコンピューティング サービスごとに**リンクされたサービス**を作成する。
3. 入力**データセット**と出力データセットを作成する。
4. **パイプライン**を作成する。 

##アクティビティ
パイプラインにおけるデータ処理の手順です。1 つ以上の入力データセットを受け取り、1 つ以上の出力データセットを生成します。アクティビティは実行環境で実行され (例: Azure HDInsight クラスター)、Azure Data Factory にリンクまたは関連付けられたデータ ストアに対してデータの読み取り/書き込みを行います。

Azure Data Factory サービスは、パイプライン内の次のアクティビティをサポートします。

- **コピー アクティビティ**は、データをデータ ストアから別のデータ ストアにコピーします。  
- **HDInsight アクティビティ**は、HDInsight クラスター上で Hive/Pig スクリプトまたは MapReduce プログラムを実行してデータを処理します。  
- **Azure Machine Learning のバッチ スコアリング アクティビティ**は、Azure Machine Learning のバッチ スコアリング API を呼び出します。詳細については、「[Azure Data Factory および Azure Machine Learning を使用して予測パイプラインを作成する][azure-ml-adf]」を参照してください。 
- **ストアド プロシージャのアクティビティ**、Azure SQL データベース内のストアド プロシージャを呼び出します。詳細については、MSDN ライブラリの[ストアド プロシージャ アクティビティ][msdn-stored-procedure-activity]に関するページを参照してください。   

##スライス
Azure データ ファクトリのテーブルは、スライスで構成されます。スライスの幅は、スケジュール (毎時/毎日) によって決まります。スケジュールが "毎時" のときは、パイプラインの開始時刻から終了時刻までに、スライスが 1 時間ごとに作成されます。たとえば、パイプラインの開始日時が 2015 年 3 月 3 日 6 時 0 分 0 秒 (午前 6 時)、終了日時が 2015 年 3 月 3 日 9 時 0 分 0 秒 (同じ日の午前 9 時) の場合、1 時間間隔で、午前 6 ～ 7 時、午前 7 ～ 8 時、午前 8 ～ 9 時の 3 つのデータ スライスが生成されます。

スライスを使えば、特定の時間帯の全データのサブセットを利用できます (例: 午後 1 時から午後 2 時の間 (1 時間) で生成されたスライス)。

## スライスに対するアクティビティ実行
**実行** (アクティビティの実行) がスライスの処理単位です。再試行する場合や、エラー発生時にスライスを再実行する場合には、1 つのスライスを 1 回以上実行することもあります。スライスは、開始時刻によって識別されます。

##Data Management Gateway
Microsoft **Data Management Gateway** は、オンプレミスのデータ ソースをクラウド サービスで使用できるように接続するソフトウェアです。内部設置型のデータ ソースをリンクされたサービスとして追加するには、企業環境に少なくとも 1 つのゲートウェイをインストールし、Azure Data Factory ポータルに登録する必要があります。
 
##データ ハブ
**データ ハブ**は、データのストレージおよびコンピューティング サービスの論理コンテナーです。たとえば、ストレージでは HDFS を使用する Hadoop クラスターがデータ ハブで、コンピューティング (処理) では Hive/Pig がデータ ハブです。同様に、エンタープライズ データ ウェアハウス (EDW) をデータ ハブとしてモデル化できます (ストレージとしてはデータベース、コンピューティング サービスとしてはストアド プロシージャや ETL ツール)。パイプラインは、データ ハブ内でデータ ストアを使用し、コンピューティング リソースを実行します。現時点でサポートされているのは、HDInsight ハブのみです。

データ ハブを使用して、Data Factory を論理的なグループやドメイン別のグループに分割できます。たとえば、"米国西部 Azure ハブ" を作成して、米国西部データ センター向けのリンクされたサービス (データ ストアとコンピューティング) とパイプラインを管理したり、"営業部門 EDW ハブ" を作成して、営業部門エンタープライズ データ ウェアハウスのデータの入力と処理に関連するすべてのリンクされたサービスとパイプラインを管理したりできます。

ハブの重要な特性は、パイプラインが 1 つのハブで実行されることです。つまり、パイプラインを定義する際、パイプライン内のテーブルまたはアクティビティによって参照されるすべてのリンクされたサービスには、パイプライン自体と同じハブ名を設定する必要があります。リンクされたサービスに HubName プロパティが指定されていない場合、リンクされたサービスは "Default" ハブに配置されます。

## 関連項目

1. [Azure Data Factory の概要][adf-intro]この記事では、Azure Data Factory サービス、もたらされる価値、およびサポートしているシナリオの概要について説明します。
2. [Data Factory を使ってみる][datafactory-getstarted]リンク先の記事では、Azure BLOB から Azure SQL データベースにデータをコピーする Azure Data Factory サンプルの作成方法についての詳細なチュートリアルが記載されています。


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-intro]: data-factory-introduction.md
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



 

<!---HONumber=Oct15_HO3-->