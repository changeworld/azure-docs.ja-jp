<properties
	pageTitle="Stretch Database の制限事項 | Microsoft Azure"
	description="Stretch Database の制限事項について説明します。"
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# Stretch Database の制限事項

Stretch が有効なテーブルの制限事項と、テーブルの Stretch の有効化を現在妨げている制限事項について説明します。

##  <a name="Caveats"></a> Stretch が有効なテーブルの制限事項

Stretch が有効なテーブルの制限事項を次に示します。

### 制約

-   移行されたデータが含まれる Azure テーブルの UNIQUE 制約と PRIMARY KEY 制約では一意性が強制されません。

### DML 操作

-   Stretch が有効なテーブル、または Stretch が有効なテーブルが含まれるビューでは行を更新または削除できません。

-   リンクされているサーバーの Stretch が有効なテーブルに行を挿入することはできません。

### インデックス

-   Stretch が有効なテーブルを含むビューのインデックスを作成することはできません。

-   SQL Server インデックスのフィルターは、リモート テーブルには反映されません。

##  <a name="Limitations"></a> テーブルの Stretch の有効化を現在妨げている制限事項

テーブルの Stretch の有効化を現在妨げている制限事項を次に示します。

### テーブルのプロパティ

-   1,023 よりも多くの列または 998 よりも多くのインデックスが含まれるテーブル

-   FILESTREAM データが格納されている FileTable またはテーブル

-   レプリケートされたテーブル、または変更追跡あるいは Change Data Capture が現在使用されているテーブル

-   メモリ最適化テーブル

### データの種類

-   text、ntext、および image

-   timestamp

-   sql\_variant

-   XML

-   CLR データ型。geometry、geography、hierarchyid、CLR ユーザー定義型など

### 列の型

-   COLUMN\_SET

-   計算列

### 制約

-   既定の制約と CHECK 制約

-   テーブルを参照する外部キー制約。親子関係 (Order と Order\_Detail など) では、子テーブル (Order\_Detail) については Stretch を有効にできますが、親テーブル (Order) に対して有効にすることはできません。

### インデックス

-   フル テキスト インデックス

-   XML インデックス

-   空間インデックス

-   テーブルを参照するインデックス付きビュー

## 関連項目

[Stretch Database Advisor を実行して Stretch Database のデータベースとテーブルを特定する](sql-server-stretch-database-identify-databases.md)

[データベースの Stretch Database を有効にする](sql-server-stretch-database-enable-database.md)

[テーブルの Stretch Database を有効にする](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0518_2016-->