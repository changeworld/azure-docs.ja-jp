<properties
	pageTitle="Stretch Database のセキュリティ制約とブロック問題 | Microsoft Azure"
	description="Stretch Database を有効にする前に解決する必要のあるブロック問題について説明します。"
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
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Stretch Database のセキュリティ制約とブロック問題

Stretch Database を有効にする前に解決する必要のあるブロック問題について説明します。

## <a name="Limitations"></a>ブロック問題
SQL Server 2016 の現在のプレビュー リリースでは、次の項目があればテーブルは Stretch の対象外となります。

**テーブルのプロパティ**
-   1,023 よりも多くの列

-   998 よりも多くのインデックス

-   FILESTREAM データを格納するテーブル

-   FileTables

-   レプリケートされたテーブル

-   変更の追跡または Change Data Capture が現在使用されているテーブル

-   メモリ最適化テーブル

**データ型と列のプロパティ**
-   timestamp

-   sql\_variant

-   XML

-   ジオメトリ

-   地理

-   hierarchyid

-   CLR ユーザー定義型 (UDT)

**列の型**
-   COLUMN\_SET

-   計算列

**制約**
-   CHECK 制約

-   既定の制約

-   テーブルを参照する外部キー制約

    Stretch Database を有効にできないテーブルは、外部キー制約によって参照されるテーブルです。親子関係 (たとえば、注文と注文明細) の場合、これは親テーブル (注文) になります。

**インデックス**
-   フル テキスト インデックス

-   XML インデックス

-   空間インデックス

-   テーブルを参照するインデックス付きビュー

## <a name="Caveats"></a>Stretch が有効なテーブルの制限事項と注意事項
SQL Server 2016 の現在のプレビュー リリースでは、Stretch が有効なテーブルに次の制限事項と注意事項があります。

-   Stretch が有効なテーブルで UNIQUE 制約と PRIMARY KEY 制約の一意性は強制されません。

-   Stretch が有効なテーブルで更新操作または削除操作を実行することはできません。

-   リンクされているサーバー上の Stretch が有効なテーブルにリモートで挿入することはできません。

-   Stretch が有効なテーブルを使用してレプリケーションを使用することはできません。

-   Stretch が有効なテーブルを含むビューのインデックスを作成することはできません。

-   Stretch が有効なテーブルを含むビューを更新または削除することはできません。ただし、Stretch が有効なテーブルを含むビューに挿入することはできます。

-   インデックスのフィルターは、リモート テーブルには反映されません。

## 関連項目

[Stretch Database Advisor を実行して Stretch Database のデータベースとテーブルを特定する](sql-server-stretch-database-identify-databases.md)

[データベースの Stretch Database を有効にする](sql-server-stretch-database-enable-database.md)

[テーブルの Stretch Database を有効にする](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0323_2016-->