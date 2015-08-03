<properties
   pageTitle="SQL Data Warehouse へのデータのロード | Microsoft Azure"
   description="SQL Data Warehouse にデータをロードするための一般的なシナリオを学習します"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/21/2015"
   ms.author="mausher;barbkess"/>

# SQL Data Warehouse へのデータのロード
SQL Data Warehouse には、さまざまなシナリオに合わせてデータをロードするための多数のオプションが用意されています。たとえば、次のようにロードする場合があります。

- 1 日に 1 回大規模なバッチをロードする
- 1 日を通して小規模なバッチをロードする
- より小さい (ディメンション) テーブルへの簡単な更新を行う

各シナリオのニーズは、SQL Data Warehouse でロードするデータのタイプによって異なります。この記事では、データをロードする際に選択できる方法をいくつか説明します。

## テクノロジ
SQL Data Warehouse では、次の標準データ ロード ツールがサポートされています。

- Azure Data Factory
- bcp コマンド ライン ユーティリティ
- PolyBase
- SQL Server Integration Services (SSIS)
- サード パーティ製のデータ ロード ツール

### Azure Data Factory (ADF)
ADF は、データの保存、処理、移動の各サービスを効率的で拡張性が高く信頼性も高いデータ生成パイプラインとして構成する、完全に管理されたサービスです。SQL Data Warehouse は、ADF [コピー アクティビティ][]の[サポートされたソース/シンク][]です。

### bcp コマンド ライン ユーティリティ
**bcp** コマンド ライン実行可能ファイルは、SQL Server、SQL Database、および SQL Data Warehouse からデータのロードや抽出を行うことができる Microsoft ユーティリティです。開始するには、「[bcp を使用したデータのロード][]」チュートリアルに従ってください。

### PolyBase
PolyBase は、Hadoop や Azure Storage BLOB ストレージをクエリする方法を提供することでデータ分析を簡略化する Microsoft テクノロジです。すべては標準の Transact-SQL を使用して実行可能であり、MapReduce を使用する必要はありません。PolyBase は Azure BLOB ストレージから SQL Data Warehouse にデータをロードすることもできます。開始するには、「[PolyBase を使用したデータのロード][]」チュートリアルに従ってください。

### SQL Server Integration Services (SSIS)
[SSIS][] は、エンタープライズ レベルのデータの統合と変換ソリューションを構築するためのプラットフォームです。SQL Data Warehouse に接続するパッケージを構築するには、ADO.Net 接続マネージャーを使用して標準の OLE DB 変換先アダプターを使用します。

### サード パーティ製のツール
SQL Data Warehouse は、データをロードするための主要な業界ソリューションをサポートします。詳細については、[ソリューション パートナー][]のリストを参照してください。

## 次のステップ
開発に関するその他のヒントについては、[開発の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[bcp を使用したデータのロード]: sql-data-warehouse-load-with-bcp.md
[PolyBase を使用したデータのロード]: sql-data-warehouse-load-with-polybase.md
[ソリューション パートナー]: sql-data-warehouse-solution-partners.md
[開発の概要]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[サポートされたソース/シンク]: https://msdn.microsoft.com/library/dn894007.aspx
[コピー アクティビティ]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141237.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!--Other Web references-->

<!---HONumber=July15_HO4-->