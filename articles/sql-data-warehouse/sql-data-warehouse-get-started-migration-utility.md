<properties
   pageTitle="移行: Data Warehouse 移行ユーティリティ |Microsoft Azure"
   description="SQL Data Warehouse に移行します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


#Data Warehouse 移行ユーティリティ (プレビュー)
Data Warehouse 移行ユーティリティは、SQL Server と Azure SQL Database から Azure SQL Data Warehouse にスキーマとデータを移行するように設計されたツールです。スキーマの移行中、このツールによって、対応するスキーマが移行元から移行先へ自動的にマップされます。スキーマの移行が完了すると、自動生成されたスクリプトを使用してデータを移動するオプションも表示されます。

このツールを使用すると、ユーザーは、スキーマとデータの移行だけでなく、互換性レポートを生成することもできます。このレポートでは、合理化された移行を妨げる、移行元インスタンスと移行先インスタンスの間の非互換性がまとめられています。

##作業の開始
Data Warehouse 移行ユーティリティは、[ここ][]からダウンロードできます。インストールの前提条件として、移行用のスクリプトを実行するための BCP コマンド ライン ユーティリティと、互換性レポートを表示するための Office が必要です。ダウンロードした実行可能ファイルを起動すると、ツールをインストールする前に、標準的な使用許諾契約書への同意が求められます。

###ツールの起動と接続
インストール後に表示されるデスクトップ アイコンをクリックすると、このツールを簡単に起動できます。このツールを開くと、最初の接続ページで入力が求められます。このページでは、移行ツールの移行元と移行先を選択できます。現時点では、移行元として SQL Server と Azure SQL Database、移行先として SQL Data Warehouse がサポートされています。これを選択すると、移行元サーバーに接続するよう求められます。これには、サーバー名を入力して認証し、[Connect] をクリックします。
 
認証後、接続しているサーバーに存在するデータベースの一覧がツールに表示されます。移行を開始するには、移行するデータベースを選択し、[Migrate selected] をクリックします。
 
##移行レポート
ツールで [Check Database Compatibility] を選択すると、移行する予定のデータベースのオブジェクトの非互換性をすべてまとめたレポートが生成されます。SQL Data Warehouse に存在しない SQL Server 機能の一部をまとめた、より広範な一覧については、[移行に関するドキュメント][]を参照してください。生成されたレポートは、Excel で保存して開くことができます。

移行スキーマを生成すると、"Object" として識別されるほとんどの結果がそのデータを即座に移行できるように調整されます。スキーマを適用する前に、追加の調整が必要ないかどうかを確認するために、変更を確認してください。

##スキーマの移行

接続後に [Migrate Schema] を選択すると、選択したテーブルのスキーマ移行スクリプトが生成されます。このスクリプトにより、テーブルの構造が移植され、互換性のないデータ型がより互換性のある形式にマップされます。さらに、ユーザーが移行の設定でセキュリティ資格情報とスキーマを指定した場合は、それらが作成されます。このコードは、移行先となる SQL Data Warehouse インスタンスに対して実行できます。また、ファイルへの保存、クリップボードへのコピー、追加操作の実行前のインラインでの編集が可能です。
 
既に説明したように、スキーマを移行する際は、ツールによって行われた移行の変更を十分に理解できるようにその変更を確認してください。

##データの移行

[Migrate Data] オプションをクリックすると、BCP スクリプトを生成できます。このスクリプトにより、データは、まずサーバー上のフラット ファイルに移動された後、SQL Data Warehouse に直接移動されます。少量のデータを移動する場合は、このプロセスをお勧めします。また、再試行機能が組み込まれていないため、エラーが発生するとネットワークが停止する可能性があることに注意してください。このプロセスを実行するには、BCP コマンド ライン ユーティリティをインストールしておく必要があります。また、そのデータのスキーマが既に作成されている必要もあります。
 
上記のパラメーターを入力したら、[run migration] をクリックするだけです。2 つのパッケージのセットが指定した場所に生成されます。移行元からフラット ファイルにデータをエクスポートするためにエクスポート ファイルを実行し、SQL Data Warehouse にデータをインポートするためにインポート ファイルを実行します。

## 次のステップ
これで、一部のデータが移行されました。次に、[開発][]方法を確認します。

<!--Image references-->

<!--Article references-->
[移行に関するドキュメント]: https://azure.microsoft.com/ja-JP/documentation/articles/sql-data-warehouse-overview-migrate/
[開発]: https://azure.microsoft.com/ja-JP/documentation/articles/sql-data-warehouse-overview-develop/
[ここ]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip

<!---HONumber=AcomDC_0114_2016-->