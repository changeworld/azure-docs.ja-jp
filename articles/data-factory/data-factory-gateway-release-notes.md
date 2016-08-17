<properties 
	pageTitle="Data Management Gateway のリリース ノート | Azure Data Factory" 
	description="Data Management Gateway のリリース ノート" 
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
	ms.date="07/17/2016" 
	ms.author="spelluru"/>

# Data Management Gateway のリリース ノート

最新のデータ統合の課題の 1 つは、オンプレミスとクラウドの間でシームレスにデータを移動することです。Data Factory によって、オンプレミスでインストールできるエージェントであり、ハイブリッドなデータ移行を可能にする Data Management Gateway との統合がシームレスになります。

詳細については、「[Azure Data Factory を使用してオンプレミスとクラウドの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md)」と「[Data Management Gateway](data-factory-data-management-gateway.md)」の記事を参照してください。

## 最新バージョン (2.1.6040.1)

- DB2 ドライバーは現在、ゲートウェイ インストール パッケージに含まれています。別途インストールする必要はありません。
- DB2 ドライバーは、既にサポートされているプラットフォーム (Linux、Unix、Windows) に加え、現在では z/OS と DB2 for i (AS/400) をサポートしています。
- オンプレミス データ ストアの送信元または送信先として DocumentDB をサポートします。
- 既にサポートされている汎用ストレージ アカウントに加え、コールド/ホット Blob Storage との間でデータをコピーできるようになりました。
- ゲートウェイを介し、リモート ログイン特権でオンプレミスの SQL Server に接続することができます。

## 以前のバージョン

## 2\.0.6013.1

- ゲートウェイで手動インストール時に使用する言語/カルチャを選択できます。
- ゲートウェイが正常に動作しない場合、問題のトラブルシューティングを支援するために、過去 7 日間にわたるゲートウェイのログを Microsoft に送信できます。ゲートウェイがクラウド サービスに接続されていない場合は、ゲートウェイのログを保存してアーカイブするように選択できます。
- Data Management Gateway 構成マネージャーのユーザー インターフェイスが強化されています。
	- ゲートウェイのステータスが [ホーム] タブで見やすくなりました。
	- コントロールの配置が見直され、簡素化されました。
- [コーディング不要のコピー プレビュー ツール](data-factory-copy-data-wizard-tutorial.md)を使用し、Azure Blob 以外のストレージから Polybase とステージング BLOB を介して Azure SQL Data Warehouse にデータをコピーできます。この機能全般について詳しくは、「[ステージング コピー](data-factory-copy-activity-performance.md#staged-copy)」を参照してください。
- Data Management Gateway を使用すると、オンプレミスの SQL Server データベースから直接 Azure Machine Learning にデータを取り込むことができます。
- パフォーマンスの向上
	- コーディング不要のコピー プレビュー ツールで SQL Server に対するスキーマ/プレビューを表示する際のパフォーマンスが向上します。



## 1\.12.5953.1
- バグの修正

## 1\.11.5918.1

- ゲートウェイのイベント ログの最大サイズが 1 MB から 40 MB に増えました。
- ゲートウェイの自動更新中に再起動が必要な場合は、警告ダイアログが表示されます。すぐに再起動することも、後で再起動することもできます。
- 自動更新が失敗した場合、ゲートウェイ インストーラーは自動更新を最大 3 回再試行します。
- パフォーマンスの向上
	- コーディング不要のコピー シナリオでオンプレミス サーバーから大規模なテーブルを読み込む場合のパフォーマンスが向上します。
- バグの修正

## 1\.10.5892.1

- パフォーマンスの向上
- バグの修正

## 1\.9.5865.2

- ゼロ タッチの自動更新機能
- ゲートウェイの状態インジケーターの付いた新しいトレイ アイコン
- クライアントから「今すぐ更新」する機能
- スケジュール更新時刻を設定する機能
- 自動更新のオン/オフを切り替えるための PowerShell スクリプト
- JSON 形式のサポート
- パフォーマンスの向上
- バグの修正

## 1\.8.5822.1

- トラブルシューティングのエクスペリエンスの改善
- パフォーマンスの向上
- バグの修正

### 1\.7.5795.1

- パフォーマンスの向上
- バグの修正

### 1\.7.5764.1

- パフォーマンスの向上
- バグの修正

### 1\.6.5735.1

- オンプレミスの HDFS ソース/シンクのサポート
- パフォーマンスの向上
- バグの修正

### 1\.6.5696.1

- パフォーマンスの向上
- バグの修正

### 1\.6.5676.1

- 構成マネージャーでの診断ツールのサポート
- Azure Data Factory の表形式データ ソースのテーブル列のサポート
- Azure Data Factory の SQL DW のサポート
- Azure Data Factory の BlobSource と FileSource の個別サポート
- CopyBehavior のサポート – Azure Data Factory のバイナリ コピーを含む BlobSink と FileSink の MergeFiles、PreserveHierarchy および FlattenHierarchy
- Azure Data Factory のコピー アクティビティ レポートの進行のサポート
- Azure Data Factory のデータ ソース接続確認のサポート
- バグの修正


### 1\.6.5672.1

- Azure Data Factory の ODBC データ ソースのテーブル名のサポート
- パフォーマンスの向上
- バグの修正

### 1\.6.5658.1

- Azure Data Factory のファイル シンクのサポート
- Azure Data Factory のバイナリ コピーの保存階層のサポート
- Azure Data Factory のコピー アクティビティのべき等性のサポート
- バグの修正

### 1\.6.5640.1

- Azure Data Factory の 3 つのデータ ソース (ODBC、OData、HDFS) のサポート
- Azure Data Factory の csv パーサーの引用符文字のサポート
- 圧縮のサポート (BZip2)
- バグの修正

### 1\.5.5612.1

- Azure Data Factory の 5 つのリレーショナル データベース (MySQL、PostgreSQL、DB2、Teradata、および Sybase) のサポート
- 圧縮のサポート (Gzip およびデフレート)
- パフォーマンスの向上
- バグの修正


### 1\.4.5549.1

- Azure Data Factory の Oracle データ ソースのサポートの追加
- パフォーマンスの向上
- バグの修正

### 1\.4.5492.1

- Microsoft Azure Data Factory と Office 365 Power BI の両方のサービスをサポートする統合されたバイナリ
- 改善された構成 UI と登録プロセス
- Azure Data Factory – SQL Server データ ソースに対する Azure の受信および送信のサポート

### 1\.2.5303.1

- 	時間がかかるデータ ソース接続をサポートするための、タイムアウトの問題の修正
 	
### 1\.1.5526.8

- セットアップの前提条件として .NET Framework 4.5.1 が必要になる

### 1\.0.5144.2

- Azure Data Factory のシナリオに影響する変更なし

## 質問/回答

### データ ソース マネージャーがゲートウェイに接続しようとするのはなぜですか。
これはセキュリティ上の設計で、構成できるのは企業ネットワーク内のクラウドにアクセスするためのオンプレミスなデータ ソースに限られています。資格情報が企業のファイアウォールの外部に流れることはありません。ゲートウェイがインストールされているコンピューターに、お使いのコンピューターからアクセスできることを確認してください。

<!---HONumber=AcomDC_0803_2016-->