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
	ms.date="07/29/2015" 
	ms.author="spelluru"/>

# データ移動アクティビティ
Data Factory には、以下に示すさまざまなデータ ストア間の[コピー アクティビティ](#copyactivity)によるデータ移動をサポートする[グローバルに利用可能なサービス](#global)があります。また、Data Factory には、Data Management Gateway を使用して[オンプレミスの場所とクラウドの間でデータを安全に移動する](#moveonpremtocloud)ための組み込みサポートもあります。

## コピー アクティビティでサポートされるデータ ストア
コピー アクティビティは、**ソース** データ ストアから**シンク** データ ストアにデータをコピーします。Data Factory は、以下のデータ ストアおよびソースとシンクの組み合わせをサポートします。データ ストアをクリックすると、そのストアとの間でデータをコピーする方法がわかります。

| **ソース** | **シンク** |
| ------ | ---- |
| [Azure BLOB](data-factory-azure-blob-connector.md) | Azure BLOB、Azure テーブル、Azure SQL Database、オンプレミスの SQL Server、IaaS 上の SQL Server、Azure DocumentDB､オンプレミスのファイル システム |
| [Azure テーブル](data-factory-azure-table-connector.md) | Azure BLOB、Azure テーブル、Azure SQL Database、オンプレミスの SQL Server、IaaS 上の SQL Server、Azure DocumentDB |
| [Azure SQL Dtabase](data-factory-azure-sql-connector.md) | Azure BLOB、Azure テーブル、Azure SQL Database、オンプレミスの SQL Server、IaaS 上の SQL Server、Azure DocumentDB |
| [Azure DocumentDB](data-factory-azure-documentdb-connector.md) | Azure BLOB、Azure テーブル、Azure SQL Database |
| [IaaS 上の SQL Server](data-factory-sqlserver-connector.md) | Azure BLOB、Azure テーブル、Azure SQL Database、オンプレミスの SQL Server、IaaS 上の SQL Server |
| [オンプレミスのファイル システム](data-factory-onprem-file-system-connector.md) | Azure BLOB、Azure テーブル、Azure SQL Database、オンプレミスの SQL Server、IaaS 上の SQL Server、オンプレミスのファイル システム |
| [オンプレミスの SQL Server](data-factory-sqlserver-connector.md) | Azure BLOB、Azure テーブル、Azure SQL Database、オンプレミスの SQL Server、IaaS 上の SQL Server |
| [オンプレミスの Oracle Database](data-factory-onprem-oracle-connector.md) | Azure BLOB、Azure テーブル、Azure SQL Database、オンプレミスの SQL Server、IaaS 上の SQL Server |
| [オンプレミスの MySQL データベース](data-factory-onprem-mysql-connector.md) | Azure BLOB、Azure テーブル、Azure SQL Database、オンプレミスの SQL Server、IaaS 上の SQL Server |
| [オンプレミスの DB2 データベース](data-factory-onprem-db2-connector.md) | Azure BLOB、Azure テーブル、Azure SQL Database、オンプレミスの SQL Server、IaaS 上の SQL Server |
| [オンプレミスの Teradata データベース](data-factory-onprem-teradata-connector.md) | Azure BLOB、Azure テーブル、Azure SQL Database、オンプレミスの SQL Server、IaaS 上の SQL Server |
| [オンプレミスの Sybase データベース](data-factory-onprem-sybase-connector.md) | Azure BLOB、Azure テーブル、Azure SQL Database、オンプレミスの SQL Server、IaaS 上の SQL Server |
| [オンプレミスの PostgreSQL データベース](data-factory-onprem-postgresql-connector.md) | Azure BLOB、Azure テーブル、Azure SQL Database、オンプレミスの SQL Server、IaaS 上の SQL Server |

## <a name="copyactivity"></a>コピー アクティビティ
コピー アクティビティは、1 つの入力データセット (**ソース**) を受け取り、アクティビティの構成に従って 1 つの出力データセット (**シンク**) にデータをコピーします。データのコピーは、アクティビティで指定されているスケジュールに従ってバッチ方式で行われます。

> [AZURE.NOTE]すべてのアクティビティで使用可能なさまざまな JSON セクションとプロパティなど、高レベルでの一般的なアクティビティ定義については、「[パイプラインとアクティビティの概要](data-factory-create-pipelines.md)」を参照してください。

コピー アクティビティには次の機能が備わっています。

### <a name="global"></a>グローバルに使用できるデータの移動
データ移動サービスを利用するコピー アクティビティは、以下の地域と場所でグローバルに使用できます。グローバルに使用できるトポロジは、ほとんどの場合に地域をまたがるホップを回避する効率的なデータ移動を保証します。

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

### <a name="moveonpremtocloud"></a>オンプレミスの場所とクラウドの間での安全なデータ移動
最新のデータ統合の課題の 1 つは、オンプレミスとクラウドの間でシームレスにデータを移動することです。Data Management Gateway はオンプレミスにインストールできるエージェントで、ハイブリッド データ パイプラインを可能にします。

Data Gateway には次の機能が備わっています。

1.	オンプレミスのデータ ストアへの安全なアクセスを管理します。
2.	同じ Data Factory 内にオンプレミスのデータ ストアとクラウド データ ストアをモデル化して、データを移動します。
3.	Data Factory のクラウド ベースのダッシュボードにゲートウェイのステータスを表示して、一元的に監視および管理を行います。


詳細については、「[オンプレミスとクラウドの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md)」を参照してください。

### 信頼性が高くコスト効果の高いデータの移動
コピー アクティビティは、さまざまなデータ ソース間の一時的なエラーに耐える信頼性の高い方法で大量のデータを移動するように設計されています。コスト効果の高い方法でデータをコピーでき、ネットワーク上でデータを圧縮するオプションがあります。

### 異なる型システム間での型変換
データ ストアが異なると、ネイティブな型システムも異なります。コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

それぞれのデータ ストアのコネクタの記事で、データ ストアに関する特定のネイティブ型システムと .NET のマッピングを検索できます。これらのマッピングを使用して、コピー アクティビティの間に適切な変換が実行されるように、テーブル作成時に適切な型を決定できます。

### 異なるファイル形式の使用
ファイル ベースのソースの場合、コピー アクティビティは、バイナリ、テキスト、Avro 形式などのさまざまなファイル形式をサポートします。コピー アクティビティを使用すると、ある形式を別の形式に変換できます(例: テキスト (CSV) から Avro)。

### コピー アクティビティのプロパティ
名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティがあらゆる種類のアクティビティで利用できます。一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。

コピー アクティビティの場合、**typeProperties** セクションはソースとシンクの種類によって異なります。上に示した各データ ストア固有のページで、データ ストアの種類に固有のこれらのプロパティが説明されています。


## フィードバックの送信
この記事に関するフィードバックをお待ちしています。少しのお時間をとって、[電子メール](mailto:adfdocfeedback@microsoft.com?subject=data-factory-data-movement-activities.md)でフィードバックをお寄せください。

<!---HONumber=Sept15_HO2-->