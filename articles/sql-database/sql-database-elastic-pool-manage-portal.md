<properties
	pageTitle="Azure ポータルを使用したエラスティック データベース プールの監視と管理 | Microsoft Azure"
	description="Azure ポータルと SQL Database の組み込みのインテリジェンスを使用して、スケーラブルなエラスティック データベース プールの管理、監視を行い、適切なサイズを設定して、データベース パフォーマンスの最適化とコストの管理を行う方法について説明します。"
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="ninarn"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="05/02/2016"
	ms.author="ninarn"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Azure ポータルを使用したエラスティック データベース プールの監視と管理

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Azure ポータルを使用すると、プール内のエラスティック データベース プールとデータベースを監視、管理、構成できます。SQL Database に組み込まれているインテリジェンスは、サーバー上のすべてのデータベース (プール内とプール外の両方) の過去の使用状況を分析し、コスト効果が向上する場合にデータベース用のプールをプロアクティブに推奨します。

ポータルを使用すると、プールとデータベースの設定を変更し、変更をプレビューして、すべての変更を同時にコミットできます。データベースの追加や削除などの変更をプレビューできます。パフォーマンスと料金に対する影響の可能性も表示されます。

この記事の手順を使用するには、データベースとプールがいくつか必要です。既存のデータベースがある場合は、[プールの作成](sql-database-elastic-pool-create-portal.md)を参照してください。データベースがない場合は、[SQL Database のチュートリアル](sql-database-get-started.md)を参照してください。

## 使用するプールを選択する

1. [Azure ポータル](https://portal.azure.com)で **[参照]** をクリックします。
2. **[SQL 可変プール]** をクリックします。
3. リストで使用するプールをクリックします。

## エラスティック プールへのデータベースの移動

既存のプールのデータベースを追加または削除することができます。データベースは他のプールにあってもかまいません。ただし、追加できるのは同じ論理サーバー上にあるデータベースだけです。

1. プールのブレードで、**[エラスティック データベース]** の **[プールの構成]** をクリックします。

    ![[プールの構成] をクリックする][1]

2. **[プールの構成]** ブレードで、**[プールに追加]** をクリックします。

	![[プールに追加] をクリックする](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

	
3. **[データベースの追加]** ブレードで、プールに追加するデータベースを選択します。**[選択]** をクリックします。

	![追加するデータベースを選択する](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    **[プールの構成]** ブレードに、追加したデータベースが一覧表示されます。状態は **[保留中]** に設定されています。

    ![保留中のプールの追加](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. [プールの構成] ブレードで、**[保存]** をクリックします。

    ![[保存] をクリックします。](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## エラスティック プールからのデータベースの移動

1. **[プールの構成]** ブレードで、削除するデータベースを選択します。

    ![データベース一覧](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. **[プールから削除]** をクリックします。

    ![データベース一覧](./media/sql-database-elastic-pool-manage-portal/remove-from-pool.png)

	選択したデータベースが、UI に "削除対象として選択されたデータベース" として表示されます。


## プールのリソース使用率の監視


1. 対象のプールを選択します。
2. **[エラスティック プールの監視]** のグラフとライブ タイルに、プールの重要な使用率情報が表示されます。

![エラスティック プールの監視](./media/sql-database-elastic-pool-manage-portal/monitor-elastic-pool.png)

**グラフと表示を変更するには:**

- **[編集]** をクリックします。

	![[編集] をクリックする](./media/sql-database-elastic-pool-manage-portal/edit-resource-utlization.png)

- **[グラフの編集]** ブレードで、新しい時間範囲 (過去 1 時間、今日、過去 1 週間) を選択するか、**[カスタム]** をクリックして他の時間範囲を設定します。グラフの種類 (棒または線) を選択し、監視するリソースを選択します。

	![[編集] をクリックする](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- 次に、 **[OK]** をクリックします


## プール リソースへのアラートの追加

リソースにルールを追加して、設定した使用率のしきい値にリソースが達したときに、ユーザーへの電子メールの送信や URL エンドポイントへのアラート文字列の送信を行うことができます。

**任意のリソースへのアラートの追加**

1. **[リソース使用率]** グラフをクリックして **[メトリック]** ブレードを開きます。次に、**[アラートの追加]** をクリックし、**[アラート ルールの追加]** ブレードで情報を入力します (**[リソース]** は使用するプールとして自動的に設定されます)。
2. **[名前]** と **[説明]** を入力して、自分と受信者がアラートを識別できるようにします。
3. 一覧でアラートの対象となる **[メトリック]** を選択します。

    グラフにはそのメトリックのリソース使用率が動的に表示されるため、しきい値の選択に役立ちます。

4. **[条件]** (より大きい、より小さいなど) と **[しきい値]** を選択します。
5. **[OK]** をクリックします。

## プールのパフォーマンス設定を変更する

プールのリソース使用率を監視すると、プールにさらに多くの eDTU が必要な状況、またはプール内の個々のデータベースで異なる eDTU 設定が必要な状況を検出できます。パフォーマンスとコストの最適なバランスを確保するために、プールの設定はいつでも変更できます。詳細については、「[エラスティック データベース プールの使用に適した状況](sql-database-elastic-pool-guidance.md)」を参照してください。

**プールあたりの eDTU 数およびデータベースあたりの eDTU 数を変更するには:**

1. **[プールの構成]** ブレードを開きます。

    **[エラスティック データベース プールの設定]** で、スライダーを使用してプールの設定を変更します。

    ![エラスティック プール リソースの使用率](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. 設定を変更すると、変更によって予想される毎月のコストが表示されます。

    ![プールと新しい月額料金の更新](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

## データベースの操作をプレビューする

データベースの追加および削除をコミットする前に、**[プールの構成]** ブレードでプレビューできます。

![データベースの追加と削除をプレビューする](./media/sql-database-elastic-pool-manage-portal/pools-tab.png)


## エラスティック ジョブの作成と管理

エラスティック ジョブにより、プール内の任意の数のデータベースに対して Transact-SQL スクリプトを実行できます。ジョブを使用する前に、エラスティック ジョブ コンポーネントをインストールし、自分の資格情報を入力します。詳細については、「[エラスティック データベース ジョブの概要](sql-database-elastic-jobs-overview.md)」をご覧ください。

エラスティック データベース ツールを使用してスケールアウト、データの移動、クエリ、トランザクションの作成を行う方法については、「[Azure SQL Database によるスケール アウト](sql-database-elastic-scale-introduction.md)」を参照してください。


## その他のリソース

- [SQL Database エラスティック プール](sql-database-elastic-pool.md)
- [ポータルを使用したエラスティック データベース プールの作成](sql-database-elastic-pool-create-csharp.md)
- [PowerShell を使用したエラスティック データベース プールの作成](sql-database-elastic-pool-create-powershell.md)
- [C# を使用したエラスティック データベース プールの作成](sql-database-elastic-pool-create-csharp.md)
- [エラスティック データベース プールの価格およびパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png

<!---HONumber=AcomDC_0504_2016-->