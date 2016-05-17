<properties 
   pageTitle="Azure SQL Database Performance Insight | Microsoft Azure" 
   description="Azure SQL Database では、現在のクエリのパフォーマンスを向上させる余地がある領域を識別するのに役立つパフォーマンス ツールを提供します。" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="04/28/2016"
   ms.author="sstein"/>

# SQL Database Performance Insight

Azure SQL Database では、インテリジェントなチューニング操作と推奨事項を提供することにより、データベースのパフォーマンスを識別、向上するためのパフォーマンス ツールを提供します。

1. [Azure ポータル](http://portal.azure.com)でデータベースを参照し、**[All settings]**、[**パフォーマンス**]、**[概要]** の順にクリックし、**[パフォーマンス]** ページを開きます。


2. **[Recommendations]** をクリックして [[SQL Database Advisor]](#sql-database-advisor) を開き、**[Queries]** をクリックして [[Query Performance Insight]](#query-performance-insight) を開きます。

    ![ビュー パフォーマンス](./media/sql-database-performance/entries.png)



## パフォーマンスの概要

**[Overview]** か **[Performance]** タイルをクリックすると、データベースのパフォーマンス ダッシュボードが表示されます。このビューにデータベースのパフォーマンスの概要が表示され、パフォーマンスのチューニングとトラブルシューティングを実行できます。

![パフォーマンス](./media/sql-database-performance/performance.png)

- **[Recommendations]** タイルには、データベースのチューニングに関する推奨事項の内訳が表示されます (推奨事項が 4 つ以上ある場合は、上から 3 番目までが表示されます)。このタイルをクリックすると、**[SQL Database Advisor]** が表示されます。 
- **[Tuning activity]** タイルには、データベースに対して進行中のチューニング操作と完了したチューニング操作の概要が表示され、チューニング操作の履歴を簡単に確認できます。このタイルをクリックすると、データベースのチューニング履歴がすべて表示されます。
- **[Auto-tuning]** タイルには、データベースの自動チューニング構成 (どのチューニング操作をデータベースに自動的に適用するか) が表示されます。このタイルをクリックすると、自動構成のダイアログが開きます。
- **[Database queries]** タイルには、データベースのクエリ パフォーマンスの概要 (全体的な DTU 使用量と上位のリソース消費に関するクエリ) が表示されます。このタイルをクリックすると、 **[Query Performance Insight]** が表示されます。



## SQL Database Advisor


[SQL Database Advisor](sql-database-index-advisor.md) では、データベースのパフォーマンスの向上に役立つインテリジェントなチューニングの推奨事項を提供します。

- 作成または削除の対象となるインデックスの推奨事項 (およびユーザーが介入することなく自動的にインデックスの推奨事項を適用するためのオプション、パフォーマンスに悪影響を与える推奨事項を自動的にロールバックするためのオプション)。
- データベースでスキーマの問題が識別された場合の推奨事項。
- パラメーター化されたクエリがクエリに役立つ場合の推奨事項。




## Query Performance Insight

[Query Performance Insight](sql-database-query-performance.md) を使用すると、短時間でデータベース パフォーマンスのトラブルシューティングを行うことができます。以下の情報が提供されます。

- データベース リソース (DTU) の消費量の詳細な情報。 
- CPU 消費の多いクエリ。パフォーマンス向上のためのチューニングの対象となる可能性があります。 
- クエリの詳細にドリルダウンする機能。 


## その他のリソース

- [データベースが 1 台の場合の Azure SQL Database のパフォーマンス ガイダンス](sql-database-performance-guidance.md)
- [エラスティック データベース プールの使用に適した状況](sql-database-elastic-pool-guidance.md)

<!---HONumber=AcomDC_0504_2016-->