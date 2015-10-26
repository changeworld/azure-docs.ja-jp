<properties 
   pageTitle="Azure SQL Database Query Performance Insight" 
   description="クエリのパフォーマンスを監視して、Azure SQL Database の DTU 消費量の多いクエリを明らかにします。" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="09/30/2015"
   ms.author="sstein"/>

# Azure SQL Database Query Performance Insight


リレーショナル データベースのパフォーマンスの管理とチューニングは、かなりの専門知識と時間の投入を必要とする困難な作業です。Query Performance Insight を使用すると、短時間でデータベース パフォーマンスのトラブルシューティングを行うことができます。以下の情報が提供されます。

- データベース リソース (DTU) の消費量の詳細な情報。 
- DTU 消費の多いクエリ。パフォーマンス向上のためのチューニングの対象となる可能性があります。 
- クエリの詳細にドリルダウンする機能。

> [AZURE.NOTE]Query Performance Insight は現在プレビュー段階にあるため、[Azure プレビュー ポータル](https://portal.azure.com/)でのみ使用できます。



## 前提条件

- Query Performance Insight は、Azure SQL Database V12 でのみ使用できます。
- Query Performance Insight を使用するには [Query Store](https://msdn.microsoft.com/library/dn817826.aspx) が稼働している必要があるので、Query Performance Insight にサインアップすると自動的に有効になります。
 
 
## アクセス許可

Query Performance Insight を使用するには、次の[ロール ベースのアクセス制御](role-based-access-control-configure.md)権限が必要です。

- リソース消費上位クエリおよびチャートを表示するには、**リーダー**、**所有者**、**共同作成者**、**SQL DB の共同作業者**、または **SQL Server の共同作業者**の権限が必要です。 
- クエリ テキストを表示するには、**所有者**、**共同作成者**、**SQL DB の共同作業者**、または **SQL Server の共同作業者**の権限が必要です。



## Query Performance Insight の使用

Query Performance Insight は簡単に使用できます。

- リソース消費量上位のクエリの一覧を確認します。 
- 個別のクエリを選択して詳細を表示します。
- **チャートを編集**して、DTU 消費データの表示方法をカスタマイズしたり、別の期間を表示したりします。



> [AZURE.NOTE]SQL Database がクエリ パフォーマンスの内実を提供するには、2 時間分のデータを Query Store でキャプチャする必要があります。一定の期間、データベースでアクティビティが発生しない場合、または Query Store がアクティブではない場合は、その時間帯を表示してもグラフは空になります。Query Store が実行していない場合はいつでも有効にできます。





## DTU 消費量上位クエリを確認する

[プレビュー ポータル](https://portal.azure.com)で次のようにします。

1. SQL Database を参照して、**[Query Performance Insight]** をクリックします。 

    ![Query Performance Insight][1]

    上位クエリ ビューが開き、DTU 消費量上位クエリの一覧が表示されます。

1. グラフのあたりをクリックすると詳細を見られます。<br>最初の行にはデータベース全体の DTU% が表示され、バーでは選択したクエリによって消費された DTU% が示されます。グラフに表示する個別のクエリを選別するには、選択または選択解除します。

    ![上位クエリ][2]

1. オプションで **[Edit chart]** をクリックして、DTU 消費データの表示方法をカスタマイズしたり、別の期間を表示したりできます。

## 個々のクエリの詳細表示

クエリの詳細を表示するには:

1. 上位クエリの一覧で任意のクエリをクリックします。<br>詳細ビューが開き、クエリの DTU 消費量が時間ごとに分割されます。 
3. グラフのあたりをクリックすると詳細を見られます。<br>最初の行には全体の DTU% が表示され、バーでは選択したクエリによって消費された DTU% が示されます。
4. クエリ実行の間隔ごとに、期間、実行回数、リソース使用率などの、詳細なメトリックが表示されます。
    
    ![クエリの詳細][3]

1. オプションで、**[View script]** をクリックするとクエリ テキストが表示され、**[Edit chart]** をクリックすると DTU 消費データの表示方法をカスタマイズしたり、別の期間を表示したりできます。




## 概要

Query Performance Insight を使うと、クエリ ワークロードの影響や、データベース リソース消費量との関係を理解できます。この機能では、消費量上位クエリがわかり、問題になる前に簡単に識別して修正できます。リソース (DTU) 消費量上位クエリを見るには、データベース ブレードで **[Query Performance Insight]** タイルをクリックします。




## 次のステップ

データベースのワークロードは動的であり、継続的に変化します。クエリを監視し、パフォーマンスの改善のために調整を続けます。

SQL Database のパフォーマンス向上のために、[Index Advisor](sql-database-index-advisor.md) も利用することをお勧めします。

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png

<!---HONumber=Oct15_HO3-->