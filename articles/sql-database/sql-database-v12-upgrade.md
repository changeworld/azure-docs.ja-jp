<properties 
	pageTitle="SQL Database V12 へのアップグレード" 
	description="以前のバージョンの Azure SQL Database から Azure SQL Database V12 にアップグレードする方法について説明します。" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="09/30/2015" 
	ms.author="sstein"/>


# SQL Database V12 へのアップグレード


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


SQL Database V12 は SQL Database の最新バージョンであり、[前の V2 バージョンに比べ、多くの利点があります](sql-database-v12-whats-new.md)。この記事では、Azure プレビュー ポータルを使用して V2 サーバーを V12 にアップグレードする方法を示します。

SQL Database V12 へのアップグレードの処理中に、[すべての Web/Business データベースを新しいサービス層に更新する](sql-database-upgrade-new-service-tiers.md)必要もあります。次の手順には、Web/Business データベースを、データベースの使用履歴に基づいて推奨される価格レベルに更新するための手順が含まれています。



1. [Azure プレビュー ポータル](http://portal.azure.com/)で、 **[すべて参照]**、**[SQL サーバー]** の順に選択し、目的のサーバーを選択することで、アップグレードするサーバーを参照します。
2. **[最新の SQL Database 更新]**、**[このサーバーをアップグレード]** の順に選択します。

      ![サーバーのアップグレード][1]

## Web/Business データベースをアップグレードする

2. すべての Web/Business データベースをアップグレードします。サーバーに Web データベースまたは Business データベースがある場合は、それらをアップグレードする必要があります。アップグレードする際、SQL Database サービスによって、各データベースに適したサービス層とパフォーマンス レベル (価格レベル) が推奨されます。サービスは、各データベースの使用履歴を分析することによって、既存のデータベースのワークロードを実行するために最適なレベルを推奨します。 
    
    各データベースを選択して見直しを行い、アップグレードするための推奨価格レベルを選択します。利用できる価格レベルはいつでも参照でき、お使いの環境に最適な価格レベルを選択できます。

     ![データベース][2]



7. 提案されているレベルをクリックすると、**[価格レベルの選択]** ブレードが表示されます。このブレードでレベルを選択し、**[選択]** をクリックすることで、そのレベルに変更できます。Web データベースまたは Business データベースごとに新しいレベルを選択します。

    ![推奨事項][6]


サーバー上のすべてのデータベースを処理した後、アップグレードを開始できます。

## アップグレードを開始する

3. サーバー上のすべてのデータベースをアップグレードの対象にしたら、アップグレードを実行することを確認するために**サーバー名を入力**し、**[OK]** をクリックします。 

    ![アップグレードの確認][3]


4. アップグレードが開始され、進行中であることを通知するメッセージが表示されます。アップグレード処理が始まります。お使いのデータベースの詳細によっては、V12 へのアップグレードに時間がかかることがあります。その間、サーバー上のすべてのデータベースはオンラインのままですが、サーバーとデータベースの管理アクションは制限されます。

    ![アップグレードが進行中][4]

    新しいパフォーマンス レベルに実際に移行するときには、データベースへの接続が非常に短い時間 (通常は数秒)、一時的に解除される場合があります。接続が終了した場合に発生する一時的なエラーに対処するための機能 (再試行ロジック) がアプリケーションに備わっている場合は、アップグレードの終了に発生する接続解除に備えるだけで十分です。

5. アップグレード操作が完了すると、SQL Database V12 サーバーの機能が有効になります。

    ![V12 の有効化][5]


## 関連リンク

- [SQL Database V12 の新機能](sql-database-v12-whats-new.md)
- [SQL Database V12 へのアップグレードの計画と準備](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-v12-upgrade/latest-sql-database-update.png
[2]: ./media/sql-database-v12-upgrade/upgrade-server2.png
[3]: ./media/sql-database-v12-upgrade/upgrade-server3.png
[4]: ./media/sql-database-v12-upgrade/online-during-upgrade.png
[5]: ./media/sql-database-v12-upgrade/enabled.png
[6]: ./media/sql-database-v12-upgrade/recommendations.png

<!---HONumber=Oct15_HO1-->