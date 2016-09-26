<properties
	pageTitle="コピー アクティビティを使用したデータの移動 | Microsoft Azure"
	description="Data Factory パイプラインでのデータの移動 (クラウド ストア間、およびオンプレミスのストアとクラウド ストアの間でのデータ移行) について説明します。コピー アクティビティの使用。"
	keywords="データのコピー, データの移動, データの移行, データ転送"
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
	ms.date="08/08/2016"
	ms.author="spelluru"/>

# コピー アクティビティを使用したデータの移動

## Overview
Azure Data Factory では、コピー アクティビティを使用して、さまざまな形式のデータを、オンプレミスやクラウドの各種データ ソースから Azure にコピーできます。コピーしたデータは、高度な変換や分析を実行できます。また、コピー アクティビティを使用して、変換や分析の結果を発行し、ビジネス インテリジェンス (BI) やアプリケーションで使用することもできます。

![コピー アクティビティの役割](media/data-factory-data-movement-activities/copy-activity.png)

コピー アクティビティは、安全性、信頼性、拡張性に優れた[グローバルに利用可能なサービス](#global)によって動作します。この記事では、Data Factory でのコピー アクティビティによるデータの移動について詳しく説明します。

まず、2 つのクラウド データ ストアの間、およびオンプレミスのデータ ストアとクラウド データ ストアの間でデータの移行がどのように行われるかについて説明します。

> [AZURE.NOTE] アクティビティ全般については、[パイプラインとアクティビティの概要](data-factory-create-pipelines.md)に関するページをご覧ください。

### 2 つのクラウド データ ストア間でのデータのコピー
ソース データ ストアとシンク データ ストアの両方がクラウドにある場合、コピー アクティビティは、次の段階を経てデータをソースからシンクにコピーします。コピー アクティビティを実行するサービスが行う処理を次に示します。

1. ソース データ ストアからデータを読み取る。
2. シリアル化/逆シリアル化、圧縮/圧縮解除、列マッピング、型変換を実行する。この操作は、入力データセット、出力データセット、およびコピー アクティビティの構成に基づいて実行されます。
3.	コピー先データ ストアにデータを書き込む。

このサービスでは、データ移動の実行に適したリージョンが自動的に選択されます。選択されるのは、通常、シンク データ ストアに最も近いリージョンです。

![クラウド間のコピー](./media/data-factory-data-movement-activities/cloud-to-cloud.png)


### オンプレミス データ ストアとクラウド データ ストア間でのデータのコピー
オンプレミス データ ストアと、クラウド データ ストアの間でデータを安全に移動するには、Data Management Gateway を、オンプレミスのコンピューターにインストールする必要があります。Data Management Gateway は、ハイブリッド データの移動と処理を可能にするエージェントです。この Data Management Gateway は、データ ストア自体と同じコンピューター、またはデータ ストアにアクセスできる別のコンピューターにインストールできます。

このシナリオでは、Data Management Gateway によって、シリアル化/逆シリアル化、圧縮/圧縮解除、列マッピング、型変換が実行されます。データは Azure Data Factory サービスを経由せず、Data Management Gateway によって、直接目的のストアに書き込まれます。

![オンプレミスとクラウドの間のコピー](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

概要とチュートリアルについては、[オンプレミスのデータ ストアとクラウド データ ストアの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事をご覧ください。このエージェントの詳細については、「[Data Management Gateway](data-factory-data-management-gateway.md)」をご覧ください。

Data Management Gateway を使用すると、Azure IaaS 仮想マシン (VM) でホストされている、サポートされるデータ ストア間でデータを移動することもできます。この場合、Data Management Gateway は、データ ストア自体と同じ VM、またはデータ ストアにアクセスできる別の VM にインストールできます。

## サポートされるデータ ストアと形式
コピー アクティビティは、ソース データ ストアからシンク データ ストアにデータをコピーします。Data Factory は次のデータ ストアをサポートしています。また、任意のソースのデータを任意のシンクに書き込むことができます。データ ストアをクリックすると、そのストアとの間でデータをコピーする方法がわかります。

カテゴリ | データ ストア | ソースとしてサポート | シンクとしてサポート
:------- | :--------- | :------------------ | :-----------------
Azure | [Azure BLOB ストレージ](data-factory-azure-blob-connector.md) <br/> [Azure Data Lake Store](data-factory-azure-datalake-connector.md) <br/> [Azure SQL Database](data-factory-azure-sql-connector.md) <br/> [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) <br/> [Azure テーブル ストレージ](data-factory-azure-table-connector.md) <br/> [Azure DocumentDB](data-factory-azure-documentdb-connector.md) <br/> | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓
データベース | [SQL Server](data-factory-sqlserver-connector.md)* <br/> [Oracle](data-factory-onprem-oracle-connector.md)* <br/> [MySQL](data-factory-onprem-mysql-connector.md)* <br/> [DB2](data-factory-onprem-db2-connector.md)* <br/> [Teradata](data-factory-onprem-teradata-connector.md)* <br/> [PostgreSQL](data-factory-onprem-postgresql-connector.md)* <br/> [Sybase](data-factory-onprem-sybase-connector.md)* <br/>[Cassandra](data-factory-onprem-cassandra-connector.md)* <br/>[MongoDB](data-factory-on-premises-mongodb-connector.md)*<br/>[Amazon Redshift](data-factory-amazon-redshift-connector.md) | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓<br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ | ✓ <br/> ✓ <br/> &nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp;<br/> &nbsp; <br/>&nbsp;
ファイル | [ファイル システム](data-factory-onprem-file-system-connector.md)* <br/> [HDFS](data-factory-hdfs-connector.md)* <br/> [Amazon S3](data-factory-amazon-simple-storage-service-connector.md) | ✓ <br/> ✓ <br/> ✓ | ✓ <br/> &nbsp;<br/>&nbsp;
その他 | [Salesforce](data-factory-salesforce-connector.md)<br/> [汎用 ODBC](data-factory-odbc-connector.md)* <br/> [汎用 OData](data-factory-odata-connector.md) <br/> [Web テーブル (HTML から生成したテーブル)](data-factory-web-table-connector.md) <br/> [GE Historian](data-factory-odbc-connector.md#ge-historian-store)* | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ | &nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp;

> [AZURE.NOTE] * が付いたデータ ストアは、オンプレミスと Azure IaaS のどちらでもサポートされます。ただし、オンプレミス/Azure IaaS のコンピューターに [Data Management Gateway](data-factory-data-management-gateway.md) をインストールする必要があります。

コピー アクティビティでサポートされていないデータ ストアとの間でデータを移動する必要がある場合は、データのコピーと移動に独自のロジックを使用した、Data Factory の**カスタム アクティビティ**を使用します。カスタム アクティビティの作成と使用の詳細については、「[Azure Data Factory パイプラインでカスタム アクティビティを使用する](data-factory-use-custom-activities.md)」をご覧ください。

### サポートされるファイル形式
コピー アクティビティを使用すると、Azure BLOB、ファイル システム、HDFS など、2 つのファイル ベース データ ストアの間でファイルをそのままコピーできます。これを行うには、入力と出力の両方のデータセット定義で [format セクション](data-factory-create-datasets.md)をスキップします。これにより、シリアル化/逆シリアル化を実行することなく、データが効率的にコピーされます。

また、コピー アクティビティでは、テキスト、Avro、ORC、JSON など、指定した形式でファイルの読み取りと書き込みを実行します。コピー アクティビティの例をいくつか示します。

-	Azure BLOB からテキスト (CSV) 形式でデータをコピーし、Azure SQL Database に書き込む。
-	オンプレミスのファイル システムからテキスト (CSV) 形式でファイルをコピーし、Azure BLOB に Avro 形式で書き込む。
-	Azure SQL Database のデータをコピーし、オンプレミスの HDFS に ORC 形式で書き込む。



## <a name="global"></a>グローバルに使用できるデータの移動
Azure Data Factory は、米国西部、米国東部、北ヨーロッパ リージョンでのみ使用できます。ただし、コピー アクティビティを実行するサービスは、以下のリージョンと場所でグローバルに使用できます。グローバルに使用できるトポロジでは効率的なデータ移動が保証されます。このデータ移動では、通常、リージョンをまたがるホップが回避されます。リージョンにおける Data Factory とデータ移動の提供状況については、「[リージョン別のサービス](https://azure.microsoft.com/regions/#services)」をご覧ください。

### クラウド データ ストア間でのデータのコピー
ソース データとシンク データが両方ともクラウドにある場合、Data Factory は同じ地域内で対象シンクに最も近いリージョンにあるサービスのデプロイメントを使用して、データを移動します。リージョンのマッピングについては、以下の表をご覧ください。

コピー先データ ストアのリージョン | データ移動に使用するリージョン
:----------------------------------- | :----------------------------
米国東部 | 米国東部
米国東部 2 | 米国東部 2
米国西部 | 米国西部
米国西部 2 | 米国西部
米国中央部 | 米国中央部
米国中西部 | 米国中央部
米国中北部 | 米国中北部
米国中南部 | 米国中南部
北ヨーロッパ | 北ヨーロッパ
西ヨーロッパ | 西ヨーロッパ
東南アジア | 東南アジア
東アジア | 東南アジア
東日本 | 東日本
西日本 | 東日本
ブラジル南部 | ブラジル南部
オーストラリア東部 | オーストラリア東部
オーストラリア南東部 | オーストラリア南東部
インド中部 | インド中部
インド南部 | インド中部
インド西部 | インド中部


> [AZURE.NOTE] コピー先データ ストアのリージョンが前のリストにない場合、代わりのリージョンには移動せず、コピー アクティビティは失敗します。

### オンプレミス データ ストアとクラウド データ ストア間でのデータのコピー
データがオンプレミス (または Azure 仮想マシン/IaaS) のストアとクラウド ストアの間でコピーされる場合は、[Data Management Gateway](data-factory-data-management-gateway.md) が、オンプレミスのコンピューターまたは仮想マシンでデータ移動を実行します。データは、[ステージング コピー](data-factory-copy-activity-performance.md#staged-copy)機能を使用しない限り、クラウド上のサービスを経由しません。この場合、データはシンク データ ストアに書き込まれる前に、ステージング Azure BLOB ストレージを経由します。

## コピー アクティビティのあるパイプラインの作成
コピー アクティビティのあるパイプラインを作成する方法はいくつかあります。

### コピー ウィザードを使用
Data Factory コピー ウィザードは、コピー アクティビティのあるパイプラインを作成するのに役立ちます。このパイプラインを使用すると、リンクされたサービス、データセット、およびパイプラインの "*JSON 定義を作成しなくても*"、サポートされているソースからデータをコピーできます。このウィザードの詳細については、「[Data Factory コピー ウィザード](data-factory-copy-wizard.md)」をご覧ください。

### JSON スクリプトを使用
Azure ポータル、Visual Studio、または Azure PowerShell で Data Factory エディターを使用すると、パイプラインの JSON 定義を作成できます (コピー アクティビティを使用)。その後、その定義をデプロイして、Data Factory にパイプラインを作成することができます。詳しい手順については、[Azure Data Factory パイプラインでコピー アクティビティを使用する方法](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関するチュートリアルをご覧ください。

JSON プロパティ (名前、説明、入力テーブル、出力テーブル、ポリシーなど) は、あらゆる種類のアクティビティで使用できます。アクティビティの `typeProperties` セクションで使用できるプロパティは、各アクティビティの種類によって異なります。

コピー アクティビティの場合、`typeProperties` セクションはソースとシンクの種類によって異なります。そのデータ ストアについて、コピー アクティビティでサポートされている type プロパティについては、[サポートされているソースとシンク](#supported-data-stores)に関するセクションでソース/シンクをクリックしてください。

JSON 定義のサンプルを次に示します。

	{
	  "name": "ADFTutorialPipeline",
	  "properties": {
	    "description": "Copy data from Azure blob to Azure SQL table",
	    "activities": [
	      {
	        "name": "CopyFromBlobToSQL",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "InputBlobTable"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "OutputSQLTable"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "SqlSink",
	            "writeBatchSize": 10000,
	            "writeBatchTimeout": "60:00:00"
	          }
	        },
	        "Policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	    ],
	    "start": "2016-07-12T00:00:00Z",
	    "end": "2016-07-13T00:00:00Z"
	  }
	}

出力データセットで定義されているスケジュールに従って、アクティビティが実行されるタイミングが決まります (たとえば、frequency を **day**、interval を **1** に設定すると、**日単位**で実行されます)。コピー アクティビティでは、入力データセット (**ソース**) から出力データセット (**シンク**) にデータがコピーされます。

コピー アクティビティには複数の入力データセットを指定できます。この複数の入力データセットを使用して、アクティビティが実行される前に依存関係が検証されます。ただし、コピーされるのは、最初のデータセットのデータだけです。詳細については、[スケジュール設定と実行](data-factory-scheduling-and-execution.md)に関するページをご覧ください。

## パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」をご覧ください。このガイドでは、内部テスト実行時の実際のパフォーマンスを一覧表示すると共に、コピー アクティビティのパフォーマンスを最適化するさまざまな方法についても説明します。

## スケジュール設定と順次コピー
Data Factory でのスケジュール設定と実行のしくみに関する詳細については、[スケジュール設定と実行のしくみ](data-factory-scheduling-and-execution.md)に関するページをご覧ください。複数のコピー操作を、順番にまたは順序を指定して 1 つずつ実行できます。「[順序指定されたコピー](data-factory-scheduling-and-execution.md#ordered-copy)」セクションをご覧ください。

## 型の変換
データ ストアが異なると、ネイティブな型システムも異なります。コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する。
2. .NET 型からネイティブの sink 型に変換する。

データ ストアに関するネイティブ型システムと .NET 型のマッピングは、それぞれのデータ ストアの記事にあります。([サポートされるデータ ストア](#supported-data-stores)の表に示されているリンクをクリックしてください)。このマッピングを使用して、テーブル作成時に適切な型を決定でき、コピー アクティビティによって適切な変換が実行されます。


## 次のステップ
- コピー アクティビティの詳細については、「[Azure Blob Storage から Azure SQL Database にデータをコピーする](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)」を参照してください。
- オンプレミスのデータ ストアからクラウド データ ストアへのデータの移動については、[オンプレミスのデータ ストアからクラウド データ ストアへのデータの移動](data-factory-move-data-between-onprem-and-cloud.md)に関するページをご覧ください。

<!---HONumber=AcomDC_0914_2016-->