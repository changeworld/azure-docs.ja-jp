<properties 
    pageTitle="Azure ポータルを使用して Azure SQL Database の geo レプリケーションを構成する | Microsoft Azure" 
    description="Azure ポータルを使用して Azure SQL Database の geo レプリケーションを構成する" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="06/14/2016"
    ms.author="sstein"/>

# Azure ポータルを使用して Azure SQL Database の geo レプリケーションを構成する


> [AZURE.SELECTOR]
- [概要](sql-database-geo-replication-overview.md)
- [Azure ポータル](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

この記事では、[Azure ポータル](http://portal.azure.com)を使用して SQL Database のアクティブ geo レプリケーションを構成する方法について説明します。

フェールオーバーを開始する方法については、[Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始](sql-database-geo-replication-failover-portal.md)に関する記事をご覧ください。

>[AZURE.NOTE] すべてのサービス レベルのすべてのデータベースでアクティブ geo レプリケーション (読み取り可能なセカンダリ) を使用できるようになりました。2017 年 4 月に、読み取り不能なタイプのセカンダリが廃止され、既存の読み取り不能なデータベースは読み取り可能なセカンダリに自動的にアップグレードされます。

Azure ポータルを使用して geo レプリケーションを構成するには、次のものが必要です。

- Azure サブスクリプション。 
- Azure SQL Database データベース。別の地理的リージョンにレプリケートするプライマリ データベースです。



## セカンダリ データベースの追加

次の手順では、geo レプリケーション パートナーシップに新しいセカンダリ データベースを作成します。

セカンダリ データベースを追加するには、サブスクリプションの所有者または共同所有者でなければなりません。

セカンダリ データベースは、プライマリ データベースと同じ名前となります。また、既定でのサービス レベルはプライマリ データベースと同じになります。セカンダリ データベースは、読み取り可能または読み取り不可とすることができるほか、単一データベースまたはエラスティック データベースとすることができます。詳細については、「[サービス レベル](sql-database-service-tiers.md)」を参照してください。セカンダリ データベースを作成しシード処理を行うと、プライマリ データベースから新しいセカンダリ データベースへのデータのレプリケートが開始されます。

> [AZURE.NOTE] パートナー データベースが既に存在する場合 (たとえば、前の geo レプリケーションのリレーションシップを終了した結果として)、コマンドは失敗します。




### セカンダリ データベースの追加

1. [Azure ポータル](http://portal.azure.com)で、geo レプリケーションについてセットアップするデータベースを参照します。
2. [SQL Database] ブレードで、**[すべての設定]**、**[geo レプリケーション]** の順に選択します。
3. セカンダリ データベースを作成するリージョンを選択します。


    ![セカンダリ データベースの追加][1]


4. **[セカンダリの種類]** (**[読み取り可能]** または **[読み取り不能]**) を構成します。
5. セカンダリ データベース用のサーバーを選択または構成します。

    ![セカンダリ データベースの作成][3]

5. 必要に応じて、以下のようにセカンダリ データベースをエラスティック データベース プールに追加できます。

       - **[エラスティック データベース プール]** をクリックし、ターゲット サーバー上でセカンダリ データベースを作成するプールを選択します。このワークフローでは新しいプールは作成されないので、ターゲット サーバー上にプールが既に存在している必要があります。

6. **[作成]** をクリックして、セカンダリ データベースを追加します。
 
6. セカンダリ データベースが作成され、シード処理が始まります。
 
    ![シード処理][6]

7. シード処理が完了すると、セカンダリ データベースは自身の状態 (読み取り不可) を表示します。

    ![セカンダリ データベース準備完了][9]



## セカンダリ データベースを削除する

この操作では、セカンダリ データベースへのレプリケーションを完全に終了し、セカンダリ データベースのロールを通常の読み取り/書き込みデータベースに変更します。セカンダリ データベースへの接続が切断された場合、コマンドは成功しますが、接続が復元するまでセカンダリ データベースは読み取り/書き込み状態になりません。

1. [Azure ポータル](http://portal.azure.com)で、geo レプリケーション パートナーシップのプライマリ データベースを参照します。
2. [SQL Database] ブレードで、**[すべての設定]**、**[geo レプリケーション]** の順に選択します。
3. **[セカンダリ]** ボックスの一覧で、geo レプリケーション パートナーシップから削除するデータベースを選択します。
4. **[レプリケーションを停止する]** をクリックします。

    ![セカンダリ データベースの削除][7]


5. **[レプリケーションの停止]** をクリックすると、確認ウィンドウが開きます。**[はい]** をクリックして、geo レプリケーション パートナーシップからデータベースを削除します (これにより、データベースはどのレプリケーションにも属さない読み取り/書き込みデータベースに設定されます)。


    ![削除の確認][8]



   

## 次のステップ

- [Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始](sql-database-geo-replication-failover-portal.md)
- [Geo レプリケーションを使用してビジネス継続性を実現するクラウド アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [災害復旧訓練](sql-database-disaster-recovery-drills.md)


## その他のリソース

- [geo レプリケーションのセキュリティ構成](sql-database-geo-replication-security-config.md)
- [新しい geo レプリケーション機能に関するスポットライト](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [SQL Database BCDR の FAQ](sql-database-bcdr-faq.md)
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)
- [クラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [復旧された Azure SQL データベースの最終処理を行う](sql-database-recovered-finalize.md)


<!--Image references-->
[1]: ./media/sql-database-geo-replication-portal/configure-geo-replication.png
[2]: ./media/sql-database-geo-replication-portal/add-secondary.png
[3]: ./media/sql-database-geo-replication-portal/create-secondary.png
[4]: ./media/sql-database-geo-replication-portal/secondary-type.png
[5]: ./media/sql-database-geo-replication-portal/create.png
[6]: ./media/sql-database-geo-replication-portal/seeding0.png
[7]: ./media/sql-database-geo-replication-portal/remove-secondary.png
[8]: ./media/sql-database-geo-replication-portal/stop-confirm.png
[9]: ./media/sql-database-geo-replication-portal/seeding-complete.png
[10]: ./media/sql-database-geo-replication-portal/failover.png

<!---HONumber=AcomDC_0615_2016-->