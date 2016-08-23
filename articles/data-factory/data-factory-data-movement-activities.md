<properties 
	pageTitle="コピー アクティビティを使用したデータの移動 | Microsoft Azure" 
	description="Data Factory パイプラインでのデータの移動 (クラウド ストア間、オンプレミスとクラウドの間でのデータの移行) について説明します。コピー アクティビティを使用します。" 
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
Azure Data Factory では、コピー アクティビティを使用して、オンプレミスやクラウドの各種データ ソースから Azure へさまざまな形式のデータをコピーできるため、より高度なデータの変換や分析を実行できます。また、コピー アクティビティを使用して変換や分析の結果を発行し、ビジネス インテリジェンス (BI) やアプリケーションで使用することもできます。

![Role of copy activity](media/data-factory-data-movement-activities/copy-activity.png)

コピー アクティビティは、安全性、信頼性、拡張性に優れた[グローバルに利用可能なサービス](#global)によって動作します。この記事では、Data Factory でのコピー アクティビティによるデータの移動について詳しく説明します。まず、2 つのクラウド データ ストアの間、およびオンプレミスのデータ ストアとクラウド データ ストアの間でデータの移行がどのように行われるかについて説明します。

> [AZURE.NOTE] アクティビティ全般については、[パイプラインとアクティビティの概要](data-factory-create-pipelines.md)に関する記事をご覧ください。

### 2 つのクラウド データ ストア間でのデータのコピー
ソース データ ストアとシンク (コピー先) データ ストアの両方がクラウドにある場合、コピー アクティビティは、次の段階を経てデータをソースからシンクにコピー/移動します。コピー アクティビティを実行するサービスは、次を行います。

1. ソース データ ストアからデータを読み取る
2. 入力データセット、出力データセット、コピー アクティビティの構成に基づいて、シリアル化/逆シリアル化、圧縮/圧縮解除、列マッピング、型変換を実行する
3.	コピー先データ ストアにデータを書き込む

このサービスは、データ移動の実行に最も適したリージョンを自動的に選択します。通常は、シンク データ ストアに最も近いリージョンが使用されます。

![cloud-to-cloud copy](./media/data-factory-data-movement-activities/cloud-to-cloud.png)


### オンプレミス データ ストアとクラウド データ ストア間でのデータのコピー
企業ファイアウォールの内側にあるオンプレミス データ ストアと、クラウド データ ストアの間でデータを安全に移動するには、Data Management Gateway をインストールする必要があります。これは、オンプレミスのコンピューター上でハイブリッドなデータ移動と処理を可能にするエージェントです。Data Management Gateway は、データ ストア自体と同じコンピューター、またはデータ ストアに到達するためのアクセス権を持つ別のコンピューターにインストールできます。このシナリオでは、シリアル化/逆シリアル化、圧縮/圧縮解除、列マッピング、型変換が Data Management Gateway によって実行されます。データが Azure Data Factory サービスを経由しないケースが、これに当てはまります。Data Management Gateway は、目的のストアに直接データを書き込みます。

![onprem-to-cloud copy](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

概要とチュートリアルについては、[オンプレミスのデータ ストアとクラウド データ ストアの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事を、Data Management Gateway の詳細情報については「[Data Management Gateway](data-factory-data-management-gateway.md)」を参照してください。

Data Management Gateway を使用すると、Azure IaaS VM (サービスとしてのインフラストラクチャ仮想マシン) でホストされている、サポートされるデータ ストア間でデータを移動することもできます。この場合、Data Management Gateway は、データ ストア自体と同じ Azure VM、またはデータ ストアに到達するためのアクセス権を持つ別の VM にインストールできます。

## サポートされるデータ ストアと形式
コピー アクティビティは、**ソース** データ ストアから**シンク** データ ストアにデータをコピーします。Data Factory は次のデータ ストアをサポートしています。また、**任意のソースのデータを任意のシンクに書き込むことができます**。データ ストアをクリックすると、そのストアとの間でデータをコピーする方法がわかります。

カテゴリ | データ ストア | ソースとしてサポート | シンクとしてサポート
:------- | :--------- | :------------------ | :-----------------
Azure | [Azure Blob](data-factory-azure-blob-connector.md) <br/> [Azure Data Lake Store](data-factory-azure-datalake-connector.md) <br/> [Azure SQL Database](data-factory-azure-sql-connector.md) <br/> [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) <br/> [Azure テーブル](data-factory-azure-table-connector.md) <br/> [Azure DocumentDB](data-factory-azure-documentdb-connector.md) <br/> | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ 
データベース | [SQL Server](data-factory-sqlserver-connector.md)* <br/> [Oracle](data-factory-onprem-oracle-connector.md)* <br/> [MySQL](data-factory-onprem-mysql-connector.md)* <br/> [DB2](data-factory-onprem-db2-connector.md)* <br/> [Teradata](data-factory-onprem-teradata-connector.md)* <br/> [PostgreSQL](data-factory-onprem-postgresql-connector.md)* <br/> [Sybase](data-factory-onprem-sybase-connector.md)* <br/>[Cassandra](data-factory-onprem-cassandra-connector.md)* <br/>[MongoDB](data-factory-on-premises-mongodb-connector.md)* | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓<br/> ✓ <br/> ✓ <br/> ✓ | ✓ <br/> ✓ <br/> &nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp;<br/> &nbsp; 
ファイル | [ファイル システム](data-factory-onprem-file-system-connector.md)* <br/> [Hadoop 分散ファイル システム (HDFS)](data-factory-hdfs-connector.md)* | ✓ <br/> ✓ <br/> | ✓ <br/> &nbsp; 
その他 | [Salesforce](data-factory-salesforce-connector.md)<br/> [汎用 ODBC](data-factory-odbc-connector.md)* <br/> [汎用 OData](data-factory-odata-connector.md) <br/> [Web テーブル (HTML から生成したテーブル)](data-factory-web-table-connector.md) <br/> [GE Historian](data-factory-odbc-connector.md#ge-historian-store)* | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ | &nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp;

> [AZURE.NOTE] * が付いたデータ ストアは、オンプレミスと Azure IaaS のどちらでもサポートされます。ただし、オンプレミス/Azure IaaS のマシンに [Data Management Gateway](data-factory-data-management-gateway.md) をインストールする必要があります。

**コピー アクティビティ**でサポートされていないデータ ストアとの間でデータを移動する必要がある場合は、データのコピーと移動に独自のロジックを使用した、Data Factory の**カスタム アクティビティ**を使用できます。カスタム アクティビティの作成と使用の詳細については、「[Azure Data Factory パイプラインでカスタム アクティビティを使用する](data-factory-use-custom-activities.md)」をご覧ください。

### サポートされるファイル形式
コピー アクティビティでは、Azure BLOB、ファイル システム、Hadoop 分散ファイル システム (HDFS) などの、2 つのファイル ベース データ ストアの間でファイルをそのままコピーできます。これを行うには、入力と出力の両方のデータセット定義で [format セクション](data-factory-create-datasets.md)をスキップします。これにより、シリアル化/逆シリアル化を実行することなく、データが効率的にコピーされます。

また、コピー アクティビティでは、テキスト、Avro、ORC、JSON など、指定した形式でファイルの読み取りと書き込みを実行します。次に、実行可能なコピー アクティビティの例をいくつか示します。

-	Azure BLOB からテキスト (CSV) 形式でデータをコピーし、Azure SQL に書き込む
-	オンプレミスのファイル システムからテキスト (CSV) 形式でファイルをコピーし、Azure BLOB に Avro 形式で書き込む
-	Azure SQL Database のデータをコピーし、オンプレミスの HDFS に ORC 形式で書き込み



## <a name="global"></a>グローバルに使用できるデータの移動
Azure Data Factory 自体は、米国西部、米国東部、および北ヨーロッパのリージョンのみで利用できますが、コピー アクティビティを利用するサービスは、以下のリージョンと場所でグローバルに使用できます。グローバルに使用できるトポロジは、一般に、リージョンをまたがるホップを回避する効率的なデータ移動を保証します。特定のリージョンにおける Data Factory サービスとデータ移動の提供状況については、「[リージョン別のサービス](https://azure.microsoft.com/regions/#services)」をご覧ください。

### クラウド データ ストア間でのデータのコピー
ソース データとシンク データが両方ともクラウド上にある場合、Azure Data Factory は同じ地域内で対象シンクの場所に最も近いリージョンにあるサービスのデプロイメントを使用して、データ移動を実行します。リージョンのマッピングについては、以下の表をご覧ください。

コピー先データ ストアのリージョン | データ移動に使用するリージョン
:----------------------------------- | :----------------------------
米国東部 | 米国東部
米国東部 2 | 米国東部 2
米国中央部 | 米国中央部
米国西部 | 米国西部
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


> [AZURE.NOTE] コピー先データ ストアのリージョンが上のリストにない場合、代わりのリージョンには移動せず、コピー アクティビティは失敗します。

### オンプレミス データ ストアとクラウド データ ストア間でのデータのコピー
データがオンプレミス (または Azure IaaS VM) とクラウドの間でコピーされる場合、データ移動はオンプレミス マシンか Azure IaaS VM にある [Data Management Gateway](data-factory-data-management-gateway.md) によって実行されます。データは、[ステージング コピー](data-factory-copy-activity-performance.md#staged-copy)機能を使用しない限り、クラウド上のサービスを経由しません。この機能を使用した場合は、データはシンク データ ストアに書き込まれる前に、ステージング Azure BLOB ストレージを経由します。


## コピー アクティビティのあるパイプラインの作成 
コピー アクティビティがあるパイプラインを、次の複数の方法で作成できます。

### コピー ウィザードの使用
**Data Factory コピー ウィザード**を使用すると、リンクされたサービス、データセット、パイプラインの **JSON 定義を作成しなくても**、サポートされているソースからコピー先にデータをコピーする、コピー アクティビティがあるパイプラインを作成できます。このウィザードの詳細については、チュートリアル「[Data Factory Copy Wizard (Data Factory コピー ウィザード)](data-factory-copy-wizard.md)」をご覧ください。

### JSON スクリプトの使用
Azure ポータルの Data Factory エディター、Visual Studio、Azure PowerShell のいずれかを使用して、コピー アクティビティのあるパイプラインの JSON 定義を作成してデプロイし、Data Factory にパイプラインを作成することができます。詳しい手順については、[Azure Data Factory パイプラインでコピー アクティビティを使用する方法](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関するチュートリアルをご覧ください。

名前、説明、入力テーブル、出力テーブル、ポリシーなどの JSON プロパティは、あらゆる種類のアクティビティで利用できます。一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。

コピー アクティビティの場合、**typeProperties** セクションはソースとシンクの種類によって異なります。データ ストアのコピー アクティビティでサポートされている type プロパティについては、[サポートされているソース/シンク](#supported-data-stores)に関するセクションでソース/シンクをクリックしてください。

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

出力データセットで定義されているスケジュールに従って、アクティビティが実行されるタイミングが決定されます (たとえば、「frequency: day」および「interval: 1」と設定すると、**日単位**で実行されます)。コピー アクティビティでは、1 つの入力データセット (**ソース**) から 1 つの出力データセット (**シンク**) にデータがコピーされます。コピー アクティビティに複数の入力データセットを指定することもできますが、アクティビティが実行される前に依存関係が検証され、最初のデータセットのデータのみがコピー先のデータセットにコピーされます。詳細については、[スケジュール設定と実行](data-factory-scheduling-and-execution.md)に関する記事をご覧ください。

## パフォーマンスとチューニング 
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因について、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」をご覧ください。また、内部テスト実行時の実際のパフォーマンスを一覧表示すると共に、コピー アクティビティのパフォーマンスを最適化するさまざまな方法を説明します。

## スケジュール設定と順次コピー
Data Factory でのスケジュール設定と実行のしくみに関する詳細については、[スケジュール設定と実行のしくみ](data-factory-scheduling-and-execution.md)に関する記事をご覧ください。

複数のコピー操作を、順番にまたは順序を指定して 1 つずつ実行できます。詳細については、「[順序指定されたコピー](data-factory-scheduling-and-execution.md#ordered-copy)」をご覧ください。

## 型の変換 
データ ストアが異なると、ネイティブな型システムも異なります。コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

各データ ストアに関する記事で、データ ストアの特定のネイティブ型システムと .NET のマッピングを確認できます ([サポートされるデータ ストア](#supported-data-stores)の表中の関連するリンクをクリックしてください)。これらのマッピングを使用して、コピー アクティビティの間に適切な変換が実行されるように、テーブル作成時に適切な型を決定できます。

 
## 次のステップ
- コピー アクティビティを使用して、ソース データ ストアからシンク データ ストアにデータを移動する方法全般については、[Azure BLOB から Azure SQL へのデータのコピー](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関する記事をご覧ください。
- オンプレミス データ ストアからクラウド データ ストアへのデータの移動については、[オンプレミス データ ストアからクラウド データ ストアへのデータの移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事をご覧ください。
 

<!---HONumber=AcomDC_0810_2016-->