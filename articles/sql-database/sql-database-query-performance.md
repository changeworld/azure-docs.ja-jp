<properties 
   pageTitle="Azure SQL Database Query Performance Insight" 
   description="クエリのパフォーマンスを監視して、Azure SQL Database の CPU 消費量の多いクエリを明らかにします。" 
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
   ms.date="05/05/2016"
   ms.author="sstein"/>

# Azure SQL Database Query Performance Insight


リレーショナル データベースのパフォーマンスの管理とチューニングは、かなりの専門知識と時間の投入を必要とする困難な作業です。Query Performance Insight を使用すると、短時間でデータベース パフォーマンスのトラブルシューティングを行うことができます。以下の情報が提供されます。

- データベース リソース (DTU) の消費量の詳細な情報。 
- CPU 消費の多いクエリ。パフォーマンス向上のためのチューニングの対象となる可能性があります。 
- クエリの詳細にドリルダウンする機能。

## 前提条件

- Query Performance Insight は、Azure SQL Database V12 でのみ使用できます。
- Query Performance Insight では、[クエリ ストア](https://msdn.microsoft.com/library/dn817826.aspx)がデータベース上で実行されている必要があります。まだ実行されていない場合は、ポータルからクエリ ストアをオンにするように求められます。

 
## アクセス許可

Query Performance Insight を使用するには、次の[ロール ベースのアクセス制御](../active-directory/role-based-access-control-configure.md)権限が必要です。

- リソース消費上位クエリおよびチャートを表示するには、**リーダー**、**所有者**、**共同作成者**、**SQL DB の共同作業者**、または **SQL Server の共同作業者**の権限が必要です。 
- クエリ テキストを表示するには、**所有者**、**共同作成者**、**SQL DB の共同作業者**、または **SQL Server の共同作業者**の権限が必要です。



## Query Performance Insight の使用

Query Performance Insight は簡単に使用できます。

- リソース消費量上位のクエリの一覧を確認します。 
- 個別のクエリを選択して詳細を表示します。
- [SQL Database Advisor](sql-database-index-advisor.md) を開き、利用できる推奨事項があるかどうかを確認します。
- 拡大して詳細を確認します。

    ![パフォーマンス ダッシュボード](./media/sql-database-query-performance/performance.png)

> [AZURE.NOTE] SQL Database がクエリ パフォーマンスの内実を提供するには、2 時間分のデータをクエリ ストアでキャプチャする必要があります。一定の期間、データベースでアクティビティが発生しない場合、またはクエリ ストアがアクティブではない場合は、その時間帯を表示してもグラフは空になります。クエリ ストアが実行されていない場合はいつでも有効にできます。



## CPU 消費量上位クエリを確認する

[ポータル](http://portal.azure.com)で次のようにします。

1. SQL Database を参照し、**[すべての設定]**、**[パフォーマンス]**、**[クエリ]** の順にクリックします。 

    ![Query Performance Insight][1]

    上位クエリ ビューが開き、CPU 消費量上位クエリの一覧が表示されます。

1. グラフのあたりをクリックして詳細を確認します。<br>最初の行にはデータベース全体の DTU% が示されます。また、バーには、選択した期間に、選択したクエリによって消費された CPU% が示されます (**[過去 1 週間]** を選択した場合は各バーが 1 日を表します)。

    ![上位クエリ][2]

    下部のグリッドには、表示可能なクエリの集計情報が表されます。

    -	クエリ ID - データベース内に含まれるクエリの一意の識別子。 
    -	監視可能な期間のクエリあたりの CPU 使用率 (集計関数に依存)。
    -	1 つのクエリの実行時間 (集計関数に依存)。
    -	特定のクエリの実行回数の合計。


	グラフに表示する個別のクエリを選別するには、選択または選択解除します。


1. データが古くなった場合は、**[更新]** ボタンをクリックします。
1. オプションで **[設定]** をクリックして、CPU 消費データの表示方法をカスタマイズしたり、別の期間を表示したりできます。

    ![設定](./media/sql-database-query-performance/settings.png)

## 個々のクエリの詳細表示

クエリの詳細を表示するには:

1. 上位のクエリの一覧でクエリをクリックします。

    ![詳細](./media/sql-database-query-performance/details.png)

4. 詳細ビューが開き、クエリの CPU 消費量が時間ごとに分割されます。
3. グラフのあたりをクリックすると詳細を見られます。<br>最初の行には全体の CPU% が表示され、バーでは選択したクエリによって消費された CPU% が示されます。
4. クエリ実行の間隔ごとに、期間、実行回数、リソース使用率などの、詳細なメトリックが表示されます。
    
    ![クエリの詳細][3]

1. オプションで **[設定]** をクリックして、CPU 消費データの表示方法をカスタマイズしたり、別の期間を表示したりできます。


## 	Query Performance Insight 用のクエリ ストア構成の最適化

Query Performance Insight の使用中に、次のようなクエリ ストア メッセージが表示されることがあります。

- 「クエリ ストアは容量に達したため、新しいデータを収集していません。」
- 「このデータベースのクエリ ストアは読み取り専用モードのため、Performance Insigh のデータを収集していません。」
- 「クエリ ストアのパラメーターは、Query Performance Insight 用に最適に設定されていません。」

これらのメッセージは、通常、クエリ ストアが新しいデータを収集できないときに表示されます。この問題を解決するには、いくつかのオプションがあります。

-	クエリ ストアの保持とキャプチャのポリシーを変更します
-	クエリ ストアのサイズを増やします 
-	クエリ ストアをクリアします

### 推奨される保存とキャプチャのポリシー

保持ポリシーには 2 つの種類があります。

- サイズ ベース – AUTO に設定した場合、最大サイズの近づくとデータが自動的にクリーンアップされます。
- 時間ベース – 既定では 30 日に設定されます。つまり、クエリ ストアの領域が不足すると、30 日より古いクエリ情報が削除されます。 

キャプチャ ポリシーは次のように設定できます。

- **すべて** - すべてのクエリをキャプチャします。これが既定のオプションです。
- **自動** - 低頻度のクエリおよびコンパイル期間と実行期間が重要ではないクエリは無視されます。実行カウント、コンパイル期間、実行期間のしきい値は内部的に決定されます。
- **なし** - クエリ ストアは新しいクエリのキャプチャを停止します。
	
すべてのポリシーを AUTO に設定し、30 日でポリシーをクリーンアップすることをお勧めします。

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);
    	
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));
    
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

クエリ ストアのサイズを増やします。これは、データベースに接続して、次のクエリを発行することにより実行できます。

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

クエリ ストアをクリアします。現在クエリ ストア内にあるすべての情報が削除されることに注意してください。

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## 概要

Query Performance Insight を使うと、クエリ ワークロードの影響や、データベース リソース消費量との関係を理解できます。この機能では、消費量上位クエリがわかり、問題になる前に簡単に識別して修正できます。




## 次のステップ

SQL Database のパフォーマンス向上に関するその他の推奨事項を確認するために、**[Query Performance Insight]** ブレードで [[SQL Database Advisor]](sql-database-index-advisor.md) をクリックします。

![Performance Advisor](./media/sql-database-query-performance/ia.png)


<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png

<!---HONumber=AcomDC_0511_2016-->