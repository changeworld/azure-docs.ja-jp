<properties
	pageTitle="Azure ポータルによる新しいエラスティック プールの作成 | Microsoft Azure"
	description="SQL Database 構成にスケーラブルなエラスティック データベース プールを追加して、多数のデータベースの管理とリソース共有を簡単にする方法について説明します。"
	keywords="スケーラブルなデータベース、データベースの構成"
	services="sql-database"
	documentationCenter=""
	authors="ninarn"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/28/2016"
	ms.author="ninarn"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Azure ポータルによる新しい Elastic Database プールの作成

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)

[プール](sql-database-elastic-pool.md)を作成する方法は、2 つあります。

1. 独自に作成する (特に、必要なプールの設定を把握している場合)。
2. SQL Database サービスから提案された設定を使用する。SQL Database サービスがテレメトリを使用してユーザーの過去の使用状況を分析し、データベースにとって費用効率の高い推奨構成を提案します。

サーバーに複数のプールを追加することはできますが、同じプールに異なるサーバーからデータベースを追加することはできません。プールを作成するには、V12 サーバーに 1 つ以上のデータベースが必要です。V12 サーバーがない場合は、「[SQL Database チュートリアル: サンプル データと Azure ポータルを使用して分単位で SQL Database を作成する](sql-database-get-started.md)」を参照してください。データベースが 1 つだけのプールを作成できますが、プールのコスト効率が高いのは、データベースが複数ある場合のみです。「[エラスティック データベース プールの価格およびパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)」を参照してください。

プールは、SQL Database V12 サーバーでのみ利用できます。データベースが V11 サーバーにある場合は、V12 サーバー上で [PowerShell スクリプトを使用してそれらをプールの候補として特定](sql-database-elastic-pool-database-assessment-powershell.md)してから、[PowerShell を使用して V12 へのアップグレードとプールの作成](sql-database-upgrade-server-powershell.md)を 1 つの手順で実行します。

## 新しいプールの作成

1. [Azure ポータル](http://portal.azure.com/)で、**[SQL Server]** をクリックし、プールに追加するデータベースが含まれているサーバーをクリックします。
2. **[新しいプール]** をクリックします。

    ![サーバーへのプールの追加](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **- または -**

    メッセージをクリックして、データベースの過去の使用状況テレメトリに基づいた推奨のプールを表示します。次に、レベルをクリックして詳細を確認し、プールをカスタマイズします。推奨のしくみについては、このトピックで後述する「[プールの推奨事項について](#understand-pool-recommendations)」を参照してください。

    ![推奨されるプール](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

    **[エラスティック データベース プール]** ブレードが表示されます。ここで、プールを設定します。前の手順で **[新しいプール]** をクリックした場合は、**[価格レベル]** の **[Standard プール]**、プールの一意の**名前**、プールの既定の構成が選択されます。推奨されるプールを選択した場合は、推奨されるレベルとプールの構成が既に選択されていますが、それらは変更することができます。

    ![エラスティック プールの構成](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

3. プールの価格レベルを変更するには、**[価格レベル]**、目的の価格レベル、**[選択]** の順にクリックします。

    > [AZURE.IMPORTANT] 価格レベルを選択し、最後の手順で **[OK]** をクリックして変更を確定すると、プールの価格レベルを変更できなくなります。

4. **[プールの構成]** をクリックします。ここでは、データベースを追加し、プールのリソース設定を選択できます。
5. データベースを追加するには、**[データベースの追加]**、追加するデータベース、**[選択]** の順にクリックします。

    ![データベースの追加](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    使用するデータベースに十分な過去の使用状況テレメトリがある場合、構成の決定に役立つ**推定される eDTU と GB の使用状況**グラフと**実際の eDTU 使用量**横棒グラフが更新されます。

    ![dynamic recommendations](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

6. **[プールの構成]** ページにあるコントロールを使用して設定を確認し、プールを構成します。各サービス レベルの上限の詳細については、「[エラスティック プールとエラスティック データベースの eDTU と記憶域の上限](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases)」を参照してください。プールの適切なサイズ設定に関する詳細なガイダンスについては、「[エラスティック データベース プールの価格およびパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)」を参照してください。
7. 完了したら、**[選択]** をクリックし、**[OK]** をクリックしてプールを作成します。

## プールの推奨事項について

SQL Database サービスでは使用状況の履歴を評価し、単一のデータベースを使用するよりコスト効率が高い場合は、1 つ以上のプールを推奨します。それぞれの推奨事項は、サーバーのデータベースでプールに最適なデータベースを一意の小集合にしたもので構成されます。プールの推奨事項は、以下で構成されます。

- プールの価格レベル (Basic、Standard、Premium)
- 適切な**プールの eDTU** (プールあたりの最大 eDTU とも呼ばれます)
- データベースあたりの **eDTU の最大値**と **eDTU の最小値**
- プールの推奨データベースの一覧

プールを推奨するとき、このサービスでは過去 30 日間のテレメトリが考慮されます。あるデータベースをエラスティック データベース プールの候補と見なすとき、それは 7 日間以上存在している必要があります。エラスティック データベース プールに既に存在するデータベースはエラスティック データベース プール推奨の候補として考慮されません。

このサービスでは、各サービス レベルの単一のデータベースを同じレベルのプールに移動することの必要性と対費用効果が評価されます。たとえば、サーバーのすべての Standard データベースの Standard エラスティック プールに対する適合性が評価されます。つまり、このサービスでは、Standard データベースを Premium プールに移動することなど、レベル間の推奨は行われません。

## その他のリソース

- [Azure ポータルを使用したエラスティック データベース プールの監視、管理、サイズ設定](sql-database-elastic-pool-manage-portal.md)
- [エラスティック データベース プールの監視と管理 (PowerShell)](sql-database-elastic-pool-manage-powershell.md)
- [C# で SQL Database を作成し、管理する](sql-database-elastic-pool-manage-csharp.md)
- [Azure SQL Database によるスケールアウト](sql-database-elastic-scale-introduction.md) 

<!---HONumber=AcomDC_0504_2016-->