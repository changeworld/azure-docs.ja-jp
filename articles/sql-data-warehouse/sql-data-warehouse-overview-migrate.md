<properties
   pageTitle="SQL Data Warehouse へのソリューションの移行 | Microsoft Azure"
   description="Azure SQL Data Warehouse プラットフォームにソリューションを組み込むための移行ガイダンス。"
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
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL Data Warehouse へのソリューションの移行

SQL Data Warehouse は、ニーズに合わせて柔軟に拡張できる分散データベース システムです。パフォーマンスと拡張性の両方を維持するために、SQL Server 内のすべての機能が SQL Data Warehouse に実装されるわけではありません。次の移行に関するトピックでは、SQL Data Warehouse にソリューションを移行する際のいくつかの主な要素について説明します。拡張性を考えてデータ ウェアハウスを設計すると、さまざまな設計パターンが導入されるため、従来の手法が常に最適であるとは限りません。おそらく、SQL Data Warehouse によって提供される分散プラットフォームを最大限に活用できるソリューションを採用したいと考えるでしょう。

また、SQL Data Warehouse は Microsoft Azure に基づいたプラットフォームであることに留意することも重要です。このため、移行の一環としてクラウドへのデータ転送が含まれる場合もよくあります。データ転送はそれ自体が問題になるものであり、特に容量が増加する場合には慎重に検討する必要があります。データ転送はデータ ロードと混同しないようにする必要もあります。データ ロードはデータ転送とは異なります。

## 移行ガイダンス
移行を開始する前に、次の記事を読んでいつかの製品の相違点と基本的な概念を必ず理解しておいてください。

- [スキーマの移行][]
- [データの移行][]
- [コードの移行][]
 
## 次のステップ
開発に関するその他のヒントについては、[開発の概要][]に関するページをご覧ください。

さらに詳細については、「[Transact-SQL リファレンス][]」を参照してください。

最後に、さまざまなデータ ロード オプションの説明と、段階的なガイダンスが記載されている「[SQL Data Warehouse へのデータのロード][]」も確認してください。

<!--Image references-->

<!--Article references-->
[スキーマの移行]: sql-data-warehouse-migrate-schema.md
[データの移行]: sql-data-warehouse-migrate-data.md
[コードの移行]: sql-data-warehouse-migrate-code.md

[開発の概要]: sql-data-warehouse-overview-develop.md
[SQL Data Warehouse へのデータのロード]: sql-data-warehouse-overview-load.md
[Transact-SQL リファレンス]: sql-data-warehouse-overview-migrate.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=Oct15_HO1-->