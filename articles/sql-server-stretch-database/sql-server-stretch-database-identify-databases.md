<properties
	pageTitle="Stretch Database Advisor を実行して Stretch Database のデータベースとテーブルを特定する | Microsoft Azure "
	description="Stretch Database の候補となるデータベースとテーブルを特定する方法について説明します。"
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

# Stretch Database Advisor を実行して Stretch Database のデータベースとテーブルを特定する

Stretch Database の候補となるデータベースとテーブルを特定するには、SQL Server 2016 Upgrade Advisor をダウンロードし、Stretch Database Advisor を実行します。Stretch Database Advisor はブロック問題も特定します。

## Upgrade Advisor のダウンロードとインストール
Upgrade Advisor を[ここ](http://go.microsoft.com/fwlink/?LinkID=613421)からダウンロードし、インストールします。このツールは、SQL Server インストール メディアには含まれていません。

## Stretch Database Advisor の実行

1.  Upgrade Advisor を実行します。

2.  **[シナリオ]** を選択してから、**[STRETCH DATABASE ADVISOR の実行]** を選択します。

3.  **[Stretch Database Advisor の実行]** ブレードで、**[分析するデータベースの選択]** をクリック します。

4.  **[データベースの選択]** ブレードで、**[SQL インスタンス]** をクリックします。

5.  **[SQL インスタンスへの接続]** ブレードで、SQL Server インスタンスの名前を入力します。次いで **[接続]** をクリックします。

6.  **[データベースの選択]** ブレードで、分析するデータベースを選択します。**[選択]** をクリックします。

7.  **[Stretch Database Advisor の実行]** ブレードで、**[実行]** をクリック します。分析が実行されます。

## 結果の確認

1.  分析が完了したら、**[Stretch Database Advisor]** ブレードで、分析したデータベースのいずれかを選択し、**[分析結果]** ブレードを表示します。

    **[分析結果]** ブレードには、既定の推奨基準に適合する、選択されたデータベースの推奨テーブルが一覧表示されます。必要に応じて、最小サイズと行数を調整し、推奨テーブルの一覧を拡大または縮小します。

2.  **[分析結果]** ブレード上の推奨テーブルの一覧で、推奨テーブルのいずれかを選択し、**[テーブル結果]** ブレードを表示します。

    **[テーブル結果]** ブレードには、選択されたテーブルのブロック問題が一覧表示されます。Stretch Database Advisor によって検出されるブロック問題については、「[Stretch Database のセキュリティ制約とブロック問題](sql-server-stretch-database-limitations.md)」をご覧ください。

3.  **[テーブル結果]** ブレード上のブロック問題の一覧で、問題のいずれかを選択し、**[ルール結果]** ブレードを表示します。

    **[ルール結果]** ブレードでは、選択した問題が説明され、軽減手順が提案されます。選択したテーブルを Stretch Database 用に構成する場合は、推奨される軽減手順を実行します。

## 次のステップ
Stretch Database を有効にします。

-   **データベース**の Stretch Database を有効にするには、「[Enable Stretch Database for a database](sql-server-stretch-database-enable-database.md)」 (データベースの Stretch Database を有効にする) を参照してください。

-   Stretch が データベースで既に有効な場合に、別の**テーブル**の Stretch Database を有効にするには、「[Enable Stretch Database for a table](sql-server-stretch-database-enable-table.md)」 (テーブルの Stretch Database を有効にする) を参照してください。

## 関連項目
[Surface area limitations and blocking issues for Stretch Database (Stretch Database のセキュリティ制約とブロック問題)](sql-server-stretch-database-limitations.md) [Enable Stretch Database for a database (データベースの Stretch Database を有効にする)](sql-server-stretch-database-enable-database.md) [Enable Stretch Database for a table (テーブルの Stretch Database を有効にする)](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0309_2016-->