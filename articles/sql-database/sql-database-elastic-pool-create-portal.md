<properties
	pageTitle="スケーラブルなエラスティック データベース プールを作成する | Microsoft Azure"
	description="SQL Database 構成にスケーラブルなエラスティック データベース プールを追加して、多数のデータベースの管理とリソース共有を簡単にする方法について説明します。"
	keywords="スケーラブルなデータベース、データベースの構成"
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/24/2016"
	ms.author="sidneyh"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Azure ポータルを使用してエラスティック データベース プールを作成する

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)

この記事では、[Azure ポータル](https://portal.azure.com/)でスケーラブルな[エラスティック データベース プール](sql-database-elastic-pool.md)を作成する方法について説明します。プールを作成する方法は 2 つあります。目的のプール設定がわかっている場合は、一から作成できます。また、サービスによる推奨設定から始めることもできます。SQL Database には、データベースの過去の使用状況テレメトリに基づいてコスト効率が高いプール設定を推奨するインテリジェンスが組み込まれています。

サーバーに複数のプールを追加することはできますが、同じプールに異なるサーバーからデータベースを追加することはできません。プールを作成するには、V12 サーバーに 1 つ以上のデータベースが必要です。データベースがない場合は、「[SQL Database チュートリアル: サンプル データと Azure ポータルを使用して分単位で SQL Database を作成する](sql-database-get-started.md)」を参照してください。データベースが 1 つだけのプールを作成できますが、プールのコスト効率が高いのは、データベースが複数ある場合のみです。「[エラスティック データベース プールの価格およびパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)」を参照してください。

> [AZURE.NOTE] プールは、SQL Database V12 サーバーでのみ利用できます。データベースが V11 サーバーにある場合は、V12 サーバー上で [PowerShell スクリプトを使用してそれらをプールの候補として特定](sql-database-elastic-pool-database-assessment-powershell.md)してから、[PowerShell を使用して V12 へのアップグレードとプールの作成](sql-database-upgrade-server-powershell.md)を 1 つの手順で実行します。

##新しいプールの作成
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

    使用するデータベースに十分な過去の使用状況テレメトリがある場合、構成の決定に役立つ**推定される eDTU と GB の使用状況**グラフと**実際の eDTU 使用量**横棒グラフが更新されます。また、プールの適切なサイズ設定に役立つ推奨事項に関するメッセージが表示される場合もあります。「[プールの推奨事項について](#understand-pool-recommendations)」を参照してください。

    ![dynamic recommendations](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

6. **[プールの構成]** ページにあるコントロールを使用して、設定を確認し、次のガイドラインに従ってプールを構成します。

    ![エラスティック プールの構成](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

    | パフォーマンス設定 | 説明 |
    | :--- | :--- |
    | **[プールの eDTU]** と **[プール GB]** (プールごとの設定)| プール内のすべてのデータベースで使用でき、共有される最大 eDTU。プールで使用できる最大 eDTU は価格レベル (サービス レベル) によって異なります。**[プールの eDTU]** は、プールで使用できるストレージと連動しています。プールに割り当てる eDTU ごとに固定量のデータベース ストレージが割り当てられます。また、割り当てるデータベース ストレージの量に応じて、eDTU が割り当てられます。 |
    | **[eDTU の最小値]** (データベースごとの設定)| プール内のすべてのデータベースが常に保証される、プールの eDTU の最小数。**[eDTU の最小値]** は、通常、0 とデータベースあたりの過去の eDTU 平均使用率の間に設定されます。これは、プール内のすべてのデータベースに適用されるグローバル設定です。 |
    | **[eDTU の最大値]** (データベースごとの設定) | プール内の任意の 1 つのデータベースで使用可能な eDTU の最大数。この上限は、最大で **[プールの eDTU]** に設定できます。データベースあたりの **[eDTU の最大値]** は、バーストやスパイクを処理できるように、十分に高く (データベースの最大使用率に) 設定します。プールでは通常、ホットとコールドのデータベース使用パターンがあり、すべてのデータベースが同時に最大に使用されることはないため、グループではある程度高めに上限が設定されています。**例:** データベースあたりの最大使用率を 50 DTU とし、グループの 100 のデータベースの 20% のみが同時に最大で使用されると仮定します。データベースあたりの eDTU 上限が 50 eDTU に設定されている場合は、プールを 5 倍に設定し、**[プールの eDTU]** を 1,000 eDTU に設定しておいても問題はありません。**[eDTU の最大値]** はデータベースのリソース保証ではなく、使用可能な場合に達する可能性がある eDTU の上限であることに注意してください。これは、プール内のすべてのデータベースに適用されるグローバル設定です。 |

    各サービス レベルの上限の詳細については、「[エラスティック プールとエラスティック データベースの eDTU と記憶域の上限](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases)」を参照してください。プールの適切なサイズ設定に関する詳細なガイダンスについては、「[エラスティック データベース プールの価格およびパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)」を参照してください。

7. 完了したら、**[選択]** をクリックし、**[OK]** をクリックしてプールを作成します。

##プールの推奨事項について
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

<!---HONumber=AcomDC_0413_2016-->