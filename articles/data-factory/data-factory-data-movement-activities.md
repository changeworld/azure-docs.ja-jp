<properties 
	pageTitle="データ移動アクティビティ" 
	description="Data Factory パイプラインでデータを移動するために使用できる Data Factory のエンティティについて説明します。" 
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
	ms.date="12/01/2015" 
	ms.author="spelluru"/>

# データ移動アクティビティ
[コピー アクティビティ](#copyactivity)により、Azure Data Factory ではデータ移動が実行されます。また、このアクティビティは、安全で信頼性が高いスケーラブルな方法によってさまざまなデータ ストア間でデータをコピーできる、[グローバルに利用可能な Data Movement Service](#global) によって動作します。このサービスでは、ソース データ ストアとシンク データ ストアの場所に基づいて、データ移動操作を実行するのに最適なリージョンが自動的に選択されます。現時点では、シンク データ ストアに最も近いリージョンが使用されています。
 
このデータ移動がさまざまなシナリオでどのように行われるかを説明します。

## 2 つのクラウド データ ストア間でのデータのコピー
ソース データ ストアとシンク (コピー先) データ ストアの両方がクラウドにある場合、コピー アクティビティは、次の段階を経てデータをソースからシンクにコピー/移動します。Data Movement Service では、次の処理が行われます。

1. ソース データ ストアからデータを読み取る
2.	入力データセット、出力データセット、コピー アクティビティの構成に基づいて、シリアル化/逆シリアル化、圧縮/圧縮解除、列マッピング、型変換を実行する 
3.	コピー先データ ストアにデータを書き込む

![cloud-to-cloud copy](.\media\data-factory-data-movement-activities\cloud-to-cloud.png)


## オンプレミス データ ストアとクラウド データ ストア間でのデータのコピー
[企業ファイアウォールの内側にあるオンプレミス データ ストアと、クラウド データ ストアの間でデータを安全に移動する](#moveonpremtocloud)には、Data Management Gateway をインストールする必要があります。これは、オンプレミスのコンピューター上でハイブリッドなデータ移動と処理を可能にするエージェントです。Data Management Gateway は、データ ストア自体と同じコンピューター、またはデータ ストアに到達するためのアクセス権を持つ別のコンピューターにインストールできます。このシナリオでは、シリアル化/逆シリアル化、圧縮/圧縮解除、列マッピング、型変換が Data Management Gateway によって実行されます。Data Movement Service は、このシナリオに関与していません。

![onprem-to-cloud copy](.\media\data-factory-data-movement-activities\onprem-to-cloud.png)

## Azure Iaas VM のデータ ストア間でのデータのコピー 
Data Management Gateway を使用すると、Azure IaaS VM (サービスとしてのインフラストラクチャ仮想マシン) でホストされている、サポートされるデータ ストア間でデータを移動することもできます。この場合、Data Management Gateway は、データ ストア自体と同じ Azure VM、またはデータ ストアに到達するためのアクセス権を持つ別の VM にインストールできます。

## サポートされるデータ ストア
コピー アクティビティは、**ソース** データ ストアから**シンク** データ ストアにデータをコピーします。Data Factory は次のデータ ストアをサポートしています。また、**任意のソースのデータを任意のシンクに書き込むことができます**。データ ストアをクリックすると、そのストアとの間でデータをコピーする方法がわかります。

| ソース| シンク |
|:------- | :---- |
| <ul><li>[Azure Blob](data-factory-azure-blob-connector.md)</li><li>[Azure テーブル](data-factory-azure-table-connector.md)</li><li>[Azure SQL Database](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB](data-factory-azure-documentdb-connector.md)</li><li>[Azure Data Lake Store](data-factory-azure-datalake-connector.md)</li><li>[オンプレミス/Azure IaaS の SQL Server](data-factory-sqlserver-connector.md)</li><li>[オンプレミス/Azure IaaS のファイル システム](data-factory-onprem-file-system-connector.md)</li><li>[オンプレミス/Azure IaaS の Oracle Database](data-factory-onprem-oracle-connector.md)</li><li>[オンプレミス/Azure IaaS の MySQL Database](data-factory-onprem-mysql-connector.md)</li><li>[オンプレミス/Azure IaaS の DB2 Database](data-factory-onprem-db2-connector.md)</li><li>[オンプレミス/Azure IaaS の Teradata Database](data-factory-onprem-teradata-connector.md)</li><li>[オンプレミス/Azure IaaS の Sybase Database](data-factory-onprem-sybase-connector.md)</li><li>[オンプレミス/Azure IaaS の PostgreSQL Database](data-factory-onprem-postgresql-connector.md)</li></ul> | <ul><li>[Azure Blob](data-factory-azure-blob-connector.md)</li><li>[Azure テーブル](data-factory-azure-table-connector.md)</li><li>[Azure SQL Database](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB](data-factory-azure-documentdb-connector.md)</li><li>[Azure Data Lake Store](data-factory-azure-datalake-connector.md)</li><li>[オンプレミス/Azure IaaS の SQL Server](data-factory-sqlserver-connector.md)</li><li>[オンプレミス/Azure IaaS のファイル システム](data-factory-onprem-file-system-connector.md)</li></ul> |


## チュートリアル
コピー アクティビティの使用のクイック チュートリアルについては、「[チュートリアル: Azure Data Factory パイプラインでコピー アクティビティを使用する](data-factory-get-started.md)」を参照してください。このチュートリアルでは、コピー アクティビティを使用して、Azure Blob Storage から Azure SQL Database にデータをコピーします。次のセクションは、コピー アクティビティでサポートされているすべてのソースとシンクの一覧です。

## <a name="copyactivity"></a>コピー アクティビティ
コピー アクティビティは、1 つの入力データセット (**ソース**) と 1 つの出力データセット (**シンク**) を受け取ります。データのコピーは、アクティビティで指定されているスケジュールに従ってバッチ方式で行われます。すべてのアクティビティで使用可能なさまざまな JSON セクションとプロパティなど、高レベルでの一般的なアクティビティ定義については、「[パイプラインとアクティビティの概要](data-factory-create-pipelines.md)」を参照してください。

コピー アクティビティには次の機能が備わっています。

### <a name="global"></a>グローバルに使用できるデータの移動
Azure Data Factory 自体は、米国西部と北ヨーロッパのリージョンのみで利用できますが、Data Movement Service を利用したコピー アクティビティは、以下のリージョンと場所でグローバルに使用できます。グローバルに使用できるトポロジは、ほとんどの場合にリージョンをまたがるホップを回避する効率的なデータ移動を保証します。

| リージョン | [地理的な場所] |
| ------ | --------- | 
| 米国中央部 | 米国 |
| 米国東部 | 米国 |
| 米国東部 2 | 米国 |
| 米国中北部 | 米国 |
| 米国中南部 | 米国 |
| 米国西部 | 米国 |
| ブラジル南部 | LATAM |
| 北ヨーロッパ | ヨーロッパ・中東・アフリカ |
| 西ヨーロッパ | ヨーロッパ・中東・アフリカ |
| 東南アジア | APAC |
| 東日本 | APAC |

以下の点に注意してください。

- **オンプレミスのデータ ソース**から**クラウド**へデータをコピーする場合またはその逆の場合 (たとえば、オンプレミスの SQL Server から Azure BLOB)、データの移動は、事実上オンプレミス環境の **Data Management Gateway** によって行われ、Data Movement Service は関与しません。
- **クラウドのコピー元**から**クラウドのコピー先**へコピーする場合 (たとえば、Azure BLOB から Azure SQL)、**Data Movement Service** によって、**同じ地域でシンクの場所に最も近い**デプロイが選択され、転送が実行されます。たとえば、東南アジアから西日本にコピーする場合は、東日本の Data Movement Service のデプロイを使用してコピーを実行します。コピー元とコピー先の両方が同じ地域にあり、その地域に利用可能な Data Movement Service がない場合 (現時点ではオーストラリアなど)、コピー アクティビティは、代替の地域で行われることなく、失敗します。注: Data Movement Service はオーストラリアにも拡張されます。 

### <a name="moveonpremtocloud"></a>オンプレミスの場所とクラウドの間での安全なデータ移動
最新のデータ統合の課題の 1 つは、オンプレミスとクラウドの間でシームレスにデータを移動することです。Data Management Gateway はオンプレミスにインストールできるエージェントで、ハイブリッド データ パイプラインを可能にします。

データ ゲートウェイには次の機能が備わっています。

1.	オンプレミスのデータ ストアへの安全なアクセスを管理します。
2.	同じ Data Factory 内にオンプレミスのデータ ストアとクラウド データ ストアをモデル化して、データを移動します。
3.	Data Factory のクラウド ベースのダッシュボードにゲートウェイのステータスを表示して、一元的に監視および管理を行います。

**ExpressRoute** を使用する場合であっても (ファイアウォールの背後にある) オンプレミスのデータ ソースとしてデータ ソースを取り扱い、**Gateway を使用して**サービスとデータ ソースの間の接続を確立する必要があります。

詳細については、「[オンプレミスとクラウドの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md)」を参照してください。

### 信頼性が高くコスト効果の高いデータの移動
コピー アクティビティは、さまざまなデータ ソース間の一時的なエラーに耐える信頼性の高い方法で大量のデータを移動するように設計されています。コスト効果の高い方法でデータをコピーでき、ネットワーク上でデータを圧縮するオプションがあります。

### 異なる型システム間での型変換
データ ストアが異なると、ネイティブな型システムも異なります。コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

それぞれのデータ ストアのコネクタの記事で、データ ストアに関する特定のネイティブ型システムと .NET のマッピングを検索できます。これらのマッピングを使用して、コピー アクティビティの間に適切な変換が実行されるように、テーブル作成時に適切な型を決定できます。

### 異なるファイル形式の使用
コピー アクティビティは、ファイル ベース ストア用のバイナリ、テキスト、Avro 形式などのさまざまなファイル形式をサポートします。コピー アクティビティを使用すると、ある形式のデータを別の形式に変換できます(例: テキスト (CSV) から Avro)。非構造化データの場合は、[データセット](data-factory-create-datasets.md)の JSON 定義で **Structure** プロパティを省略できます。

### コピー アクティビティのプロパティ
名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティがあらゆる種類のアクティビティで利用できます。一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。

コピー アクティビティの場合、**typeProperties** セクションはソースとシンクの種類によって異なります。上に示した各データ ストア固有のページで、データ ストアの種類に固有のこれらのプロパティが説明されています。

<!---HONumber=AcomDC_1203_2015-->