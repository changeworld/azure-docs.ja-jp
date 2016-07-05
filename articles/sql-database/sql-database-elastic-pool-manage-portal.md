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
	ms.date="06/22/2016"
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


Azure ポータルを使用すると、プール内のエラスティック データベース プールとデータベースを監視および管理できます。ポータルでは、エラスティック プールおよびプール内のデータベースの使用状況を監視できます。また、エラスティック プールに対して複数の変更を行い、すべての変更を同時に送信できます。このような変更としては、データベースの追加または削除、エラスティック プールの設定の変更、データベース設定の変更などがあります。

次の図では、エラスティック プールの例を示します。ビューには次のものが含まれます。

*  エラスティック プールおよびプールに含まれるデータベースの両方についてリソースの使用状況を監視するためのグラフ。 
*  エラスティック プールを変更するための **[プールの構成]** ボタン。 
*  新しいデータベースを作成して現在のエラスティック プールに追加する **[データベースの作成]** ボタン。
*  リスト内のすべてのデータベースに対して Transact SQL スクリプトを実行することによって多数のデータベースを管理できるエラスティック ジョブ。 

![プール ビュー][2]

この記事の手順を行うには、Azure 内に少なくとも 1 つのデータベースとエラスティック プールを含む SQL Server が必要です。エラスティック プールがない場合は、[プールの作成](sql-database-elastic-pool-create-portal.md)を参照してください。データベースがない場合は、[SQL Database のチュートリアル](sql-database-get-started.md)を参照してください。

## エラスティック プールの監視

特定のプールのリソース使用状況を見ることができます。既定では、プールは過去 1 時間のストレージと eDTU の使用状況を表示するように構成されます。さまざまな時間枠で異なるメトリックを表示するようにグラフを構成できます。

1. 対象のプールを選択します。
2. **[弾力性プールの監視]** の下には、**[リソース使用率]** というグラフがあります。グラフをクリックします。

	![エラスティック プールの監視][3]

	**[メトリック]** ブレードが開き、指定した時間範囲についての指定したメトリックの詳細ビューが表示されます。

	![[メトリック] ブレード][9]

### グラフの表示をカスタマイズするには

グラフおよびメトリックのブレードを編集して、使用された CPU の割合、データ IO の割合、ログ IO の割合など、他のメトリックを表示できます。
 
2. [メトリック] ブレードで、**[編集]** をクリックします。

	![[編集] をクリックする][6]

- **[グラフの編集]** ブレードで、新しい時間範囲 (過去 1 時間、今日、過去 1 週間) を選択するか、**[カスタム]** をクリックして過去 2 週間の任意の日付範囲を選択します。グラフの種類 (棒または線) を選択し、監視するリソースを選択します。 

	![[編集] をクリックする](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- 次に、 **[OK]** をクリックします


## エラスティック データベースの監視

個々のデータベースで潜在的な問題を監視することもできます。

1. **[弾力性データベースの監視]** には、5 つのデータベースのメトリックを表示するグラフがあります。既定では、プール内のデータベースのうち過去 1 時間の平均 eDTU 使用率で上位の 5 つがグラフに表示されます。グラフをクリックします。

	![エラスティック プールの監視][4]

2. **[データベース リソース使用率]** ブレードが表示されます。プール内のデータベースの使用状況の詳細が表示されます。ブレード下部のグリッドを使用すると、プール内の任意のデータベースを選択して (最大 5 データベース) その使用率をグラフに表示できます。また、**[グラフの編集]** をクリックすると、グラフに表示されるメトリックと時間範囲をカスタマイズできます。

	![[データベース リソース使用率] ブレード][8]

### ビューをカスタマイズするには

1. **[データベース リソース使用率]** ブレードで、**[グラフの編集]** をクリックします。

	![[グラフの編集] をクリックする](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. **[グラフの編集]** ブレードで、新しい時間範囲 (過去 1 時間または過去 24 時間) を選択するか、**[カスタム]** をクリックして過去 2 週間以内の異なる日を選択します。

	![[カスタム] をクリックする](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. **[データベースの比較]** ドロップダウンをクリックして、データベースの比較に使用するメトリックを選択します。

	![グラフを編集する](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### 監視するデータベースを選択するには

**[データベース リソース使用率]** ブレードのデータベースの一覧では、リスト内のページを検索するかデータベースの名前を入力して、特定のデータベースを見つけることができます。データベースを選択するにはチェック ボックスを使用します。

![監視するデータベースを探す][7]
  

## プール リソースへのアラートの追加

リソースにルールを追加して、設定した使用率のしきい値にリソースが達したときに、ユーザーへの電子メールの送信や URL エンドポイントへのアラート文字列の送信を行うことができます。

> [AZURE.IMPORTANT]エラスティック プールのリソース使用率の監視には、20 分以上の遅れがあります。エラスティック プールに対して 30 分未満のアラートを設定することは現在サポートされていません。30 分未満の期間 (「PowerShell API の「-WindowSize」というパラメーター) を含むエラスティック プールのアラート設定は、実行されない場合があります。エラスティック プールに対して定義するアラートでは、30 分以上の期間 (WindowSize) を使用してください。

**任意のリソースへのアラートの追加**

1. **[リソース使用率]** グラフをクリックして **[メトリック]** ブレードを開きます。次に、**[アラートの追加]** をクリックし、**[アラート ルールの追加]** ブレードで情報を入力します (**[リソース]** は使用するプールとして自動的に設定されます)。
2. **[名前]** と **[説明]** を入力して、自分と受信者がアラートを識別できるようにします。
3. 一覧でアラートの対象となる **[メトリック]** を選択します。

    グラフにはそのメトリックのリソース使用率が動的に表示されるため、しきい値の選択に役立ちます。

4. **[条件]** (より大きい、より小さいなど) と **[しきい値]** を選択します。
5. **[OK]** をクリックします。



## エラスティック プールへのデータベースの移動

既存のプールのデータベースを追加または削除することができます。データベースは他のプールにあってもかまいません。ただし、追加できるのは同じ論理サーバー上にあるデータベースだけです。

1. プールのブレードで、**[エラスティック データベース]** の **[プールの構成]** をクリックします。

    ![[プールの構成] をクリックする][1]

2. **[プールの構成]** ブレードで、**[プールに追加]** をクリックします。

	![[プールに追加] をクリックする](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

	
3. **[データベースの追加]** ブレードで、プールに追加するデータベースを選択します。**[選択]** をクリックします。

	![追加するデータベースを選択する](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    **[プールの構成]** ブレードに、追加するものとして選択したデータベースが一覧表示されます。状態は **[保留中]** に設定されています。

    ![保留中のプールの追加](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. **[プールの構成]** ブレードで、**[保存]** をクリックします。

    ![[保存] をクリックします。](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## エラスティック プールからのデータベースの移動

1. **[プールの構成]** ブレードで、削除するデータベースを選択します。

    ![データベース一覧](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. **[プールから削除]** をクリックします。

    ![データベース一覧](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    **[プールの構成]** ブレードに、削除するものとして選択したデータベースが一覧表示されます。状態は **[保留中]** に設定されています。
    
    ![データベースの追加と削除をプレビューする](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. **[プールの構成]** ブレードで、**[保存]** をクリックします。

    ![[保存] をクリックします。](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## プールのパフォーマンス設定を変更する

プールのリソース使用率を監視すると、必要な調整が見つかることがあります。プールのパフォーマンスやストレージ制限の変更が必要な場合、プール内のデータベースの設定を変更することになります。パフォーマンスとコストの最適なバランスを確保するために、プールの設定はいつでも変更できます。詳細については、「[エラスティック データベース プールの使用に適した状況](sql-database-elastic-pool-guidance.md)」を参照してください。

**プールあたりの eDTU 数またはストレージ制限、およびデータベースあたりの eDTU 数を変更するには:**

1. **[プールの構成]** ブレードを開きます。

    **[エラスティック データベース プールの設定]** で、スライダーを使用してプールの設定を変更します。

    ![エラスティック プール リソースの使用率](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. 設定を変更すると、変更によって予想される毎月のコストが表示されます。

    ![プールと新しい月額料金の更新](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)


## エラスティック ジョブの作成と管理

エラスティック ジョブにより、プール内の任意の数のデータベースに対して Transact-SQL スクリプトを実行できます。新しいジョブを作成したり、ポータルを使用して既存のジョブを管理したりすることができます。

![エラスティック ジョブの作成と管理][5]


ジョブを使用する前に、エラスティック ジョブ コンポーネントをインストールし、自分の資格情報を入力します。詳細については、「[エラスティック データベース ジョブの概要](sql-database-elastic-jobs-overview.md)」をご覧ください。

エラスティック データベース ツールを使用してスケールアウト、データの移動、クエリ、トランザクションの作成を行う方法については、「[Azure SQL Database によるスケール アウト](sql-database-elastic-scale-introduction.md)」を参照してください。



## その他のリソース

- [SQL Database エラスティック プール](sql-database-elastic-pool.md)
- [ポータルを使用したエラスティック データベース プールの作成](sql-database-elastic-pool-create-csharp.md)
- [PowerShell を使用したエラスティック データベース プールの作成](sql-database-elastic-pool-create-powershell.md)
- [C# を使用したエラスティック データベース プールの作成](sql-database-elastic-pool-create-csharp.md)
- [エラスティック データベース プールの価格およびパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png

<!---HONumber=AcomDC_0622_2016-->