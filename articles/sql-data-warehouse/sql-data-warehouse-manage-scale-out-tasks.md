<properties
   pageTitle="Azure SQL Data Warehouse のスケーラビリティ管理タスク (Azure ポータル) | Microsoft Azure"
   description="Azure SQL Data Warehouse のパフォーマンスをスケールアウトする Azure ポータル タスク。DWU を調整してコンピューティング リソースを変更します。また、コストを節約するために、コンピューティング リソースを一時停止および再開します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Azure SQL Data Warehouse のスケーラビリティ管理タスク (Azure ポータル)

> [AZURE.SELECTOR]
- [概要](sql-data-warehouse-overview-scalability.md)
- [ポータル](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST ()](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)


コンピューティング リソースとメモリを、ワークロードの変化する需要に合わせて柔軟にスケール アウトし、ピーク時以外にはリソースをスケール バックしてコストを削減します。

このタスクのコレクションでは、Azure ポータルを使用して次の操作を実行します。

- DWU の調整によるパフォーマンスのスケーリング
- コンピューティング リソースの一時停止
- コンピューティング リソースの再開

詳細については、[パフォーマンスのスケーラビリティの概要][]に関する記事をご覧ください。

<a name="scale-performance-bk"></a>

## パフォーマンスのスケーリング

[AZURE.INCLUDE [SQL Data Warehouse の DWU のスケーリングの説明](../../includes/sql-data-warehouse-scale-dwus-description.md)]

コンピューティング リソースを変更するには、次の手順に従います。

1. [Azure ポータル][]を開き、データベースを開いて、**[スケール]** をクリックします。

    ![[スケール] をクリックします。][1]

1. [スケール] ブレードで、スライダーを左または右に移動して DWU 設定を変更します。

    ![スライダーの移動][2]

1. **[保存]** をクリックします。確認メッセージが表示されます。**[はい]** をクリックして確定します。キャンセルするには、**[いいえ]** をクリックします。

    ![[保存] をクリックします。][3]

<a name="pause-compute-bk"></a>

## コンピューティングの一時停止

[AZURE.INCLUDE [SQL Data Warehouse の一時停止の説明](../../includes/sql-data-warehouse-pause-description.md)]

データベースを一時停止するには、次の手順に従います。

1. [Azure ポータル][]を開き、データベースを開きます。[状態] が **[オンライン]** になっていることがわかります。 

    ![オンライン状態][6]

1. コンピューティング リソースとメモリ リソースを一時停止するには、**[一時停止]** をクリックします。確認メッセージが表示されます。**[はい]** をクリックして確定します。キャンセルするには、**[いいえ]** をクリックします。

    ![一時停止の確認][7]

1. SQL Data Warehouse がデータベースを起動している間、状態が **一時停止中" になります。
2. 状態が **[一時停止]** になると、一時停止操作が完了し、DWU に対して課金されなくなります。

    ![一時停止状態][4]

<a name="resume-compute-bk"></a>

## コンピューティングの再開

[AZURE.INCLUDE [SQL Data Warehouse の再開の説明](../../includes/sql-data-warehouse-resume-description.md)]データベースを再開するには、次の手順に従います。

1. [Azure ポータル][]を開き、データベースを開きます。状態が **[一時停止]** になっていることがわかります。 

    ![データベースの一時停止][4]

1. データベースを再開するには、**[開始]** をクリックします。確認メッセージが表示されます。**[はい]** をクリックして確定します。キャンセルするには、**[いいえ]** をクリックします。

    ![再開の確認][5]

1. SQL Data Warehouse がデータベースを開始している間、状態が "再開中" になります。
2. 状態が **[オンライン]** になったら、データベースをいつでも使用できます。

    ![オンライン状態][6]

<a name="next-steps-bk"></a>

## 次のステップ
詳細については、[管理の概要][]に関する記事をご覧ください。

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-scale-out-tasks/click-scale.png
[2]: ./media/sql-data-warehouse-manage-scale-out-tasks/move-slider.png
[3]: ./media/sql-data-warehouse-manage-scale-out-tasks/click-save.png
[4]: ./media/sql-data-warehouse-manage-scale-out-tasks/resume-database.png
[5]: ./media/sql-data-warehouse-manage-scale-out-tasks/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-scale-out-tasks/pause-database.png
[7]: ./media/sql-data-warehouse-manage-scale-out-tasks/pause-confirm.png

<!--Article references-->
[管理の概要]: ./sql-data-warehouse-overview-manage.md
[パフォーマンスのスケーラビリティの概要]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->


<!--Other Web references-->

[Azure ポータル]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->