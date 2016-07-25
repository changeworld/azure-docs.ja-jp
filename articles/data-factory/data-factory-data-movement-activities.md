<properties 
	pageTitle="データ移動アクティビティ | Microsoft Azure" 
	description="Data Factory パイプラインでのデータの移動 (クラウド ストア間、オンプレミスとクラウドの間でのデータの移行) について説明します。コピー アクティビティを使用します。" 
	keywords="データの移動, データの移行, データのコピー, データ転送"
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
	ms.date="07/11/2016" 
	ms.author="spelluru"/>

# データ移動およびコピー アクティビティ: クラウドへのデータの移行およびクラウド ストア間でのデータの移行
ソースからシンク (コピー先) へのデータの移動は、Azure Data Factory の[コピー アクティビティ](#copyactivity)によって実行されます。コピー アクティビティは、安全で信頼性が高く、スケーラブルな、[グローバルに利用可能なサービス](#global)によって動作します。このサービスは、データ移動の実行に最も適したリージョンを自動的に選択します。通常は、シンク データ ストアに最も近いリージョンが使用されます。

ここでは、2 つのクラウド データ ストア間、オンプレミスのデータ ストアとクラウド データ ストア間、Azure Iaas VM 上のデータ ストア間でデータの移行がどのように行われるかについて説明します。

## 2 つのクラウド データ ストア間でのデータのコピー
ソース データ ストアとシンク (コピー先) データ ストアの両方がクラウドにある場合、コピー アクティビティは、次の段階を経てデータをソースからシンクにコピー/移動します。コピー アクティビティを実行するサービスは、次を行います。

1. ソース データ ストアからデータを読み取る
2.	入力データセット、出力データセット、コピー アクティビティの構成に基づいて、シリアル化/逆シリアル化、圧縮/圧縮解除、列マッピング、型変換を実行する
3.	コピー先データ ストアにデータを書き込む

![cloud-to-cloud copy](.\media\data-factory-data-movement-activities\cloud-to-cloud.png)


## オンプレミス データ ストアとクラウド データ ストア間でのデータのコピー
企業ファイアウォールの内側にあるオンプレミス データ ストアと、クラウド データ ストアの間でデータを安全に移動するには、Data Management Gateway をインストールする必要があります。これは、オンプレミスのコンピューター上でハイブリッドなデータ移動と処理を可能にするエージェントです。Data Management Gateway は、データ ストア自体と同じコンピューター、またはデータ ストアに到達するためのアクセス権を持つ別のコンピューターにインストールできます。このシナリオでは、シリアル化/逆シリアル化、圧縮/圧縮解除、列マッピング、型変換が Data Management Gateway によって実行されます。データが Azure Data Factory サービスを経由しないケースが、これに当てはまります。Data Management Gateway は、目的のストアに直接データを書き込みます。

![onprem-to-cloud copy](.\media\data-factory-data-movement-activities\onprem-to-cloud.png)

概要とチュートリアルについては、[オンプレミスのデータ ストアとクラウド データ ストアの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事を、Data Management Gateway の詳細情報については「[Data Management Gateway](data-factory-data-management-gateway.md)」の記事を参照してください。

## Azure Iaas VM のデータ ストア間でのデータのコピー 
Data Management Gateway を使用すると、Azure IaaS VM (サービスとしてのインフラストラクチャ仮想マシン) でホストされている、サポートされるデータ ストア間でデータを移動することもできます。この場合、Data Management Gateway は、データ ストア自体と同じ Azure VM、またはデータ ストアに到達するためのアクセス権を持つ別の VM にインストールできます。

## サポートされるデータ ストア
コピー アクティビティは、**ソース** データ ストアから**シンク** データ ストアにデータをコピーします。Data Factory は次のデータ ストアをサポートしています。また、**任意のソースのデータを任意のシンクに書き込むことができます**。データ ストアをクリックすると、そのストアとの間でデータをコピーする方法がわかります。

| ソース| シンク |
|:------- | :---- |
| <ul><li>[Azure BLOB](data-factory-azure-blob-connector.md)</li><li>[Azure Table](data-factory-azure-table-connector.md)</li><li>[Azure SQL Database](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (下記の注を参照)](data-factory-azure-documentdb-connector.md)</li><li>[Azure Data Lake Store](data-factory-azure-datalake-connector.md)</li><li>[オンプレミス/Azure IaaS の SQL Server](data-factory-sqlserver-connector.md)</li><li>[オンプレミス/Azure IaaS のファイル システム](data-factory-onprem-file-system-connector.md)</li><li>[オンプレミス/Azure IaaS の Oracle Database](data-factory-onprem-oracle-connector.md)</li><li>[オンプレミス/Azure IaaS の MySQL Database](data-factory-onprem-mysql-connector.md)</li><li>[オンプレミス/Azure IaaS の DB2 Database](data-factory-onprem-db2-connector.md)</li><li>[オンプレミス/Azure IaaS の Teradata Database](data-factory-onprem-teradata-connector.md)</li><li>[オンプレミス/Azure IaaS の Sybase Database](data-factory-onprem-sybase-connector.md)</li><li>[オンプレミス/Azure IaaS の PostgreSQL Database](data-factory-onprem-postgresql-connector.md)</li><li>[オンプレミス/Azure IaaS の ODBC データ ソース](data-factory-odbc-connector.md)</li><li>[オンプレミス/Azure IaaS の Hadoop 分散ファイル システム (HDFS)](data-factory-hdfs-connector.md)</li><li>[OData ソース](data-factory-odata-connector.md)</li><li>[Web テーブル (HTML のテーブル)](data-factory-web-table-connector.md)</li><li>[オンプレミス/Azure IaaS の GE Historian](data-factory-odbc-connector.md#ge-historian-store)</li></ul> | <ul><li>[Azure BLOB](data-factory-azure-blob-connector.md)</li><li>[Azure Table](data-factory-azure-table-connector.md)</li><li>[Azure SQL Database](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (下記の注を参照)](data-factory-azure-documentdb-connector.md)</li><li>[Azure Data Lake Store](data-factory-azure-datalake-connector.md)</li><li>[オンプレミス/Azure IaaS の SQL Server](data-factory-sqlserver-connector.md)</li><li>[オンプレミス/Azure IaaS のファイル システム](data-factory-onprem-file-system-connector.md)</li><li>[オンプレミス/Azure IaaS の Oracle Database](data-factory-onprem-oracle-connector.md)</li></ul> |


> [AZURE.NOTE] Azure DocumentDB、オンプレミス データ ストア、Azure IaaS データ ストアの間でのデータのコピーは、現時点ではサポートされていません。Azure DocumentDB の完全なマトリックスは、間もなく有効になります。

**コピー アクティビティ**でサポートされていないデータ ストアとの間でデータを移動する必要がある場合は、データのコピーと移動に独自のロジックを使用した、Data Factory の**カスタム アクティビティ**を使用できます。カスタム アクティビティの作成と使用の詳細については、「[Azure Data Factory パイプラインでカスタム アクティビティを使用する](data-factory-use-custom-activities.md)」を参照してください。

## チュートリアル
コピー アクティビティの使用のクイック チュートリアルについては、「[チュートリアル: Azure Data Factory パイプラインでコピー アクティビティを使用する](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)」を参照してください。このチュートリアルでは、コピー アクティビティを使用して、Azure Blob Storage から Azure SQL Database にデータをコピーします。

## <a name="copyactivity"></a>コピー アクティビティ
コピー アクティビティは、1 つの入力データセット (**ソース**) から 1 つの出力データセット (**シンク**) にデータをコピーします。データのコピーは、アクティビティで指定されているスケジュールに従ってバッチ方式で行われます。アクティビティを定義する一般的な方法については、[パイプラインとアクティビティの概要](data-factory-create-pipelines.md)に関する記事を参照してください。

コピー アクティビティには次の機能が備わっています。

### <a name="global"></a>グローバルに使用できるデータの移動
Azure Data Factory 自体は、米国西部、米国東部、および北ヨーロッパのリージョンのみで利用できますが、コピー アクティビティを利用するサービスは、以下のリージョンと場所でグローバルに使用できます。グローバルに使用できるトポロジは、ほとんどの場合にリージョンをまたがるホップを回避する効率的なデータ移動を保証します。

コピー操作のソースとコピー先のデータ ストアの場所に基づいて、**Data Management Gateway** または **Azure Data Factory** がデータ移動を実行します。詳細については、次のテーブルを参照してください。

ソース データ ストアの場所 | コピー先データ ストアの場所 | データ移動の実行者  
-------------------------- | ------------------------------- | ----------------------------- 
オンプレミス/Azure VM (IaaS) | クラウド | オンプレミスのコンピューターまたは Azure VM 上の **Data Management Gateway**。データはクラウドのサービスを経由しません。<br/><br/>注: Data Management Gateway はデータ ストアと同じオンプレミスのコンピューターまたは Azure VM に配置することができます。また、両方のデータ ストアに接続できる場合は、異なるオンプレミスのコンピューターまたは Azure VM に配置することもできます。
クラウド | オンプレミス/Azure VM (IaaS) | 上記と同じです。 
オンプレミス/Azure VM (IaaS) | オンプレミス/Azure VM | **ソースに関連付けられている Data Management Gateway**。データはクラウドのサービスを経由しません。上の注を参照してください。   
クラウド | クラウド | **コピー アクティビティを実行するクラウド サービス**。Azure Data Factory は地理的に同じの、シンクの場所に最も近いリージョンでこのサービスのデプロイメントを使用します。マッピングについては、次のテーブルを参照してください。<br/><br/><table><tr><th>コピー先データ ストアのリージョン</th> <th>データ移動に使用するリージョン</th></tr><tr><td>米国東部</td><td>米国東部</td></tr><tr><td>米国東部 2</td><td>米国東部 2</td><tr/><tr><td>米国中部</td><td>米国中部</td><tr/><tr><td>米国西部</td><td>米国西部</td></tr><tr><td>米国中北部</td><td>米国中北部</td></tr><tr><td>米国中南部</td><td>米国中南部</td></tr><tr><td>北ヨーロッパ</td><td>北ヨーロッパ</td></tr><tr><td>西ヨーロッパ</td><td>西ヨーロッパ</td></tr><tr><td>東南アジア</td><td>東南アジア</td></tr><tr><td>東アジア</td><td>東南アジア</td></tr><tr><td>東日本</td><td>東日本</td></tr><tr><td>西日本</td><td>東日本</td></tr><tr><td>ブラジル南部</td><td>ブラジル南部</td></tr><tr><td>オーストラリア東部</td><td>オーストラリア東部</td></tr><tr><td>オーストラリア南東部</td><td>オーストラリア南東部</td></tr></table>


> [AZURE.NOTE] コピー先データ ストアのリージョンが上のリストにない場合、代わりのリージョンに移動することなく、コピー アクティビティが失敗します。


### 信頼性が高くコスト効果の高いデータの移動
コピー アクティビティは、さまざまなデータ ソース間の一時的なエラーに耐える信頼性の高い方法で大量のデータを移動するように設計されています。コスト効果の高い方法でデータをコピーでき、ネットワーク上でデータを圧縮するオプションがあります。

### 異なる型システム間での型変換
データ ストアが異なると、ネイティブな型システムも異なります。コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

それぞれのデータ ストアのコネクタの記事で、データ ストアに関する特定のネイティブ型システムと .NET のマッピングを検索できます。これらのマッピングを使用して、コピー アクティビティの間に適切な変換が実行されるように、テーブル作成時に適切な型を決定できます。

### 異なるファイル形式の使用
コピー アクティビティは、ファイル ベース ストア用のバイナリ、テキスト、Avro、ORC、JSON 形式などのさまざまなファイル形式をサポートします。コピー アクティビティを使用すると、ある形式のデータを別の形式に変換できます(例: テキスト (CSV) から Avro)。非構造化データの場合は、[データセット](data-factory-create-datasets.md)の JSON 定義で **Structure** プロパティを省略できます。

### コピー アクティビティのプロパティ
名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティがあらゆる種類のアクティビティで利用できます。一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。

コピー アクティビティの場合、**typeProperties** セクションはソースとシンクの種類によって異なります。データ ストアのコピー アクティビティでサポートされている type プロパティについては、[サポートされているソース/シンク](#supported-data-stores)のセクションでソース/シンクをクリックしてください。

上に示した各データ ストア固有のページで、データ ストアの種類に固有のこれらのプロパティが説明されています。

### 順序指定されたコピー
複数のコピー操作を、順番にまたは順序を指定して 1 つずつ実行できます。パイプラインには、CopyActivity1 と CopyActivity 2 という 2 つのコピー アクティビティがあり、それぞれ入力データと出力データセットは次のようになります。

CopyActivity1: 入力: Dataset1 出力: Dataset2

CopyActivity2: 入力: Dataset2 出力: Dataset4

CopyActivity2 は、CopyActivity1 が正常に実行していて、Dataset2 を使用できた場合にのみ実行します。

上の例で、CopyActivity2 には異なる入力 (たとえば Dataset3) を使用できますが、Dataset2 も CopyActivity2 への入力として指定する必要があるため、このアクティビティは CopyActivity1 が完了するまで実行されません。次に例を示します。

CopyActivity1: 入力: Dataset1 出力: Dataset2

CopyActivity2: 入力: Dataset3、Dataset2 出力: Dataset4

複数の入力を指定すると、データのコピーに使用されるのは最初の入力データセットのみで、他のデータセットは依存関係として使用されます。CopyActivity2 は、次の条件が満たされた場合にのみ実行を開始します。

- CopyActivity1 が正常に完了していて、Dataset2 を使用できる。このデータセットは、データを Dataset4 にコピーする際に使用されません。これは、CopyActivity2 のスケジュールの依存関係としてのみ機能します。
- Dataset3 を使用できる。このデータセットは、コピー先にコピーされるデータを表します。


### コピー アクティビティのパフォーマンスとチューニング 
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因について、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」をご覧ください。また、内部テスト実行時の実際のパフォーマンスを一覧表示すると共に、コピー アクティビティのパフォーマンスを最適化するさまざまな方法を説明します。


## Data Factory コピー ウィザード
**Data Factory コピー ウィザード**により、リンクされたサービス、データセット、およびパイプラインの JSON 定義を書くことなく、サポートされているソースからコピー先にデータをコピーするパイプラインを作成できます。コピー ウィザードを起動するには、Data Factory のホーム ページの **[データをコピー]** タイルをクリックします。

![データのコピー ウィザード](./media/data-factory-data-movement-activities/copy-data-wizard.png)

### 特徴

#### データをコピーするための直観的でシームレスなウィザード 
このウィザードでは、次の簡単な手順で数分以内にソースからコピー先にデータを簡単に移動できます。

1.	**コピー元**を選択する
2.	**コピー先**を選択する
3.	**設定**を構成する

![データ ソースの選択](./media/data-factory-data-movement-activities/select-data-source-page.png)

#### 豊富なデータの探索とスキーマ マッピング
ウィザード内で、テーブル/フォルダーの参照、データのプレビュー、スキーマのマップ、式の検証、簡単なデータ変換の実行が可能です。

**テーブル/フォルダーの参照** ![テーブルやフォルダーの参照](./media/data-factory-data-movement-activities/browse-tables-folders.png)

#### 多様なデータとオブジェクトの種類のためのスケーラブルな操作性
この操作性は、最初からビッグ データを考慮して設計されています。数百のフォルダー、ファイル、またはテーブルを移動する Data Factory パイプラインを簡単かつ効率的に作成できます。

**データのプレビュー、スキーマのマップ、簡単な変換の実行** ![ファイル形式設定](./media/data-factory-data-movement-activities/file-format-settings.png) ![スキーマ マッピング](./media/data-factory-data-movement-activities/schema-mapping.png) ![式の検証](./media/data-factory-data-movement-activities/validate-expressions.png)

#### 多様なデータとオブジェクトの種類のためのスケーラブルな操作性
この操作性は、最初からビッグ データを考慮して設計されています。数百のフォルダー、ファイル、テーブルの移動は、コピー ウィザードを使用して、簡単かつ効率的に作成できます。

![データをコピーするテーブルの選択](./media/data-factory-data-movement-activities/select-tables-to-copy-data.png)

#### 豊富なスケジュール オプション
コピー操作は、1 回だけ、またはスケジュールに従って (毎時、毎日など) 実行できます。これらのオプションどちらも、オンプレミス、クラウド、ローカル デスクトップ コピーにわたる幅広いコネクタで使用できます。1 回限りのコピーは、ソースからコピー先への 1 回だけのデータ移動が可能で、任意のサイズと、サポートされる任意の形式のデータに適用されます。スケジュールされたコピーは、規定された繰り返しでデータをコピーできます。豊富な設定 (再試行、タイムアウト、アラートなど) を利用して、スケジュールされたコピーを構成できます。

![スケジュール プロパティ](./media/data-factory-data-movement-activities/scheduling-properties.png)


### 実際に使ってみる 
**Data Factory コピー ウィザード**を使用して、コピー アクティビティによってパイプラインを作成する簡単なチュートリアルについては、[Data Factory コピー ウィザードを使用してパイプラインを作成する方法のチュートリアル](data-factory-copy-data-wizard-tutorial.md)を参照してください。


### Azure BLOB フォルダー パスの変数
フォルダー パスで変数を使用して、[WindowStart システム変数](data-factory-functions-variables.md#data-factory-system-variables)に基づいて実行時に判断されるフォルダーから、データをコピーできます。サポートされている変数は、**year**、**month**、**day**、**hour**、**minute**、および **{custom}** です。例: inputfolder/{year}/{month}/{day}。

次の形式の入力フォルダーがあるとします。
	
	2016/03/01/01
	2016/03/01/02
	2016/03/01/03
	...

**[ファイルまたはフォルダー]** の **[参照]** ボタンをクリックして、これらのいずれかのフォルダー、たとえば 2016->03->01->02 に移動し、**[選択]** をクリックします。テキスト ボックスに **2016/03/01/02** と表示されます。ここで、**2016** を **{year}**、**03** を **{month}**、**01** を **{day}**、**02** を **{hour}** に置き換えて、**Tab** キーを押します。下に示すように、これらの 4 つの変数の**形式**を選択するドロップ ダウン リストが表示されます。

![システム変数の使用](./media/data-factory-data-movement-activities/blob-standard-variables-in-folder-path.png)

下に示すように、**custom** 変数を使用し、任意の[サポートされる書式文字列](https://msdn.microsoft.com/library/8kb3ddd4.aspx)を使用することもできます。最初に [参照] ボタンを使用して、構築するフォルダーを選択し、値を **{custom}** に置き換えて、**Tab** キーを押し、書式文字列を入力できるテキスト ボックスを表示します。

![カスタム変数の使用](./media/data-factory-data-movement-activities/blob-custom-variables-in-folder-path.png)

## 次のステップ
- コピー アクティビティを使用して、ソース データ ストアからシンク データ ストアにデータを移動する一般的な方法については、[Azure BLOB から Azure SQL へのデータのコピー](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関する記事をご覧ください。
- オンプレミスのデータ ストアからクラウド データ ストアへのデータ移動については、[オンプレミスのデータ ストアからクラウド データ ストアへのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事をご覧ください。
 

<!---HONumber=AcomDC_0713_2016-->