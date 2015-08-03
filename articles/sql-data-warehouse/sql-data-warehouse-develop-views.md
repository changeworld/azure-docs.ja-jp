<properties
   pageTitle="SQL Data Warehouse のビュー | Microsoft Azure"
   description="ソリューション開発のための、Azure SQL Data Warehouse での Transact-SQL ビューの使用に関するヒント。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

 
# SQL Data Warehouse のビュー

SQL Data Warehouse では、ビューが特に役立ちます。ビューをさまざまな方法で使用して、ソリューションの品質を向上させることができます。

この記事では、ビューを実装してソリューションを強化する方法の例を示します。考慮する必要がある制限事項もいくつかあります。

## アーキテクチャの抽象化
一般的なアプリケーション パターンでは、データを読み込みながら、CREATE TABLE AS SELECT (CTAS) を使用し、その後にオブジェクトの名前変更パターンを使用してテーブルを再作成します。

次の例では、日付ディメンションに新しい日付レコードを追加します。まず、新しいオブジェクト DimDate_New を作成し、次に、このオブジェクトの名前を変更して、オブジェクトの元のバージョンを置き換えます。``` CREATE TABLE dbo.DimDate_New WITH (DISTRIBUTION = REPLICATE , CLUSTERED INDEX (DateKey ASC) ) AS SELECT * FROM dbo.DimDate AS prod UNION ALL SELECT * FROM dbo.DimDate_stg AS stg ;

RENAME OBJECT DimDate TO DimDate_Old; RENAME OBJECT DimDate_New TO DimDate;

``` ただし、この結果、SSDT SQL Server オブジェクト エクスプローラーのユーザーのビューに、テーブル オブジェクトが表示されたり、表示されなかったりする可能性があります。ビューを使用すると、基になるオブジェクトの名前は変更されますが、ウェアハウスのデータの利用者に一貫性のあるプレゼンテーション レイヤーを提供できます。ビューからデータにアクセスできるようにすると、ユーザーは基になるテーブルを表示する必要がなくなります。これにより、一貫性のあるユーザー エクスペリエンスを提供すると同時に、データ ウェアハウスの設計者は、データ モデルを進化させ、データの読み込みプロセスで CTAS を使用してパフォーマンスを最大限に高めることもできます。

## パフォーマンスの最適化
ビューは、パフォーマンスに最適化された結合をテーブル間に適用する優れた方法です。たとえば、ビューで結合条件の一部として冗長分散キーを組み込むことで、データの移動を最小限に抑えることができます。別の理由として、特定のクエリ ヒントや結合ヒントを強制できることが挙げられます。これにより、結合が常に最適な方法で実行されることが保証され、ユーザーに依存せずに結合を適切に作成できます。

## 制限事項
SQL Data Warehouse のビューはメタデータ専用です。

したがって、次のオプションは使用できません。- スキーマ バインド オプションはありません。- ビューからベース テーブルを更新することはできません。- 一時テーブルに対してビューを作成することはできません。- EXPAND/NOEXPAND ヒントはサポートされていません。 - SQL Data Warehouse にはインデックス付きビューはありません。


## 次のステップ
開発に関するその他のヒントについては、[SQL Data Warehouse の開発の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse の開発の概要]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO4-->