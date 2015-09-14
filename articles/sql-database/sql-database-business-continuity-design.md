<properties 
   pageTitle="ビジネス継続性のための SQL Database の設計"
	description="選択するためのガイダンス。このセクションでは、どの BCDR 機能をいつ使用するかを選択する方法についてのガイダンスが提供されます。これには、SQL DB を使用して自動的に取得できることに関する説明が含まれます。"
	services="sql-database"
	documentationCenter=""
	authors="elfisher"
	manager="jeffreyg"
	editor="monicar"/>

<tags
   ms.service="sql-database"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-management"
	ms.date="07/14/2015"
	ms.author="elfish"/>

#ビジネス継続性のための設計

ビジネス継続性のためにアプリケーションを設計するには、次の質問に答える必要があります。

1. アプリケーションを障害から保護するために適切なビジネス継続性の機能はどれでしょうか。
2. どのレベルの冗長性とレプリケーション トポロジを使用するでしょうか。

##地理リストアを使用する場合

SQL Database では、既定ですべてのデータベースにおける基本的な保護が組み込まれています。これは地理冗長 Azure ストレージ (GRS) にデータベースのバックアップを格納することで行われます。この方法を選択する場合、特別に構成したり、別のリソースを割り当てる必要はありません。これらのバックアップを使用すると、あらゆるリージョンで地理リストアのコマンドを使用してデータベースを回復できます。地理リストアを使用したアプリケーションの回復についての詳細は、「[障害からの回復](sql-database-disaster-recovery.md)」セクションをご覧ください。

アプリケーションが次の条件を満たす場合は組み込まれている保護を使用する必要があります。

1. ミッション クリティカルとして考慮されない場合。拘束力のある SLA はないため、24 時間以上のダウンタイムで財務責任が課せられることはありません。
2. データ変更レートが低い場合 (1 時間あたりのトランザクションなど)。RPO が 1 時間でも大規模なデータの損失は発生しません。
3. アプリケーションは費用重視なので、geo レプリケーションの追加コストは正当化できません。 

> [AZURE.NOTE]地理リストアでは、特定のリージョンにコンピューティング容量を事前に割り当て、システム停止時にバックアップからアクティブなデータベースを復元することはありません。サービスでは、そのリージョンの既存のデータベースへの影響を最小限に抑え、容量の要求を最優先する形で地理リストアのリクエストに関連するワークロードを管理します。そのため、データベースの復旧時間は、同じリージョンで同時に回復するその他のデータベースの量によって異なります。

##geo レプリケーションを使用する場合

geo レプリケーションは、プライマリから別のリージョンにレプリカ データベース (セカンダリ) を作成します。これにより、データベースには必要なデータとコンピューティング リソースを持たせ、回復後のアプリケーションのワークロードをサポートすることが保証されます。フェールオーバーを使用して、アプリケーションを回復させるには、「[障害からの回復](sql-database-disaster-recovery.md)」セクションをご覧ください。

アプリケーションが次の条件を満たす場合は geo レプリケーションを使用する必要があります。

1. ミッション クリティカルな場合。アグレッシブな RPO と RTO で、拘束力のある SLA がある場合。データや可用性の損失により財務責任が発生します。 
2. データ変更レートが高い場合 (1 分または 1 秒あたりのトランザクションなど)。既存の保護に関連した RPO が 1 時間の場合は、許容できないデータ損失が発生する可能性が高くなります。
3. geo レプリケーションの使用に関連するコストは、潜在的な財務責任とビジネスの関連する損失を大幅に下回る場合。

> [AZURE.NOTE]アプリケーションで Basic レベルのデータベースを使用する場合は、geo レプリケーションはサポートされません。

##Standard かアクティブ geo レプリケーションを選択するとき

Standard レベルのデータベースには、アクティブ geo レプリケーションを使用するオプションがないため、Standard のデータベースを使用し、前述の条件を満たす場合は、標準 geo レプリケーションが有効にできるはずです。一方で Premium のデータベースではどちらのオプションも選択できます。標準 geo レプリケーションは、よりシンプルで低コストの障害復旧ソリューションとして設計されており、特に停電などの予期しないイベントから保護する場合にのみソリューションを使用するアプリケーションに最適です。標準 geo レプリケーションでは、障害復旧のペアのリージョンのみを復旧に使用でき、セカンダリを 2 つ以上作成することはできません。この後者の機能は、アプリケーションのアップグレード シナリオにおいて重要になります。したがって、このシナリオが使用するアプリケーションにとって重要な場合は、代わりにアクティブ geo レプリケーションを有効にする必要があります。詳細については「[ダウンタイムのないアプリケーションのアップロード](sql-database-business-continuity-application-upgrade.md)」をご覧ください。

> [AZURE.NOTE]アクティブ geo レプリケーションでは、セカンダリ データベースへの読み取り専用アクセスもサポートするため、読み取り専用ワークロードに対して追加の容量を提供します。

##geo レプリケーションを有効にする方法

geo レプリケーションは Azure ポータルを使用するか REST API や PowerShell コマンドを呼び出すことによって有効にできます。

###Azure ポータル

[AZURE.VIDEO sql-database-enable-geo-replication-in-azure-portal]

1. [Azure ポータル](https://portal.Azure.com)にログインします。
2. 画面の左側にある、**[参照]** をクリックして、**[SQL Database]** を選択します。
3. データベースのブレードに移動し **[geo レプリケーション マップ]** を選択して**[geo レプリケーションの構成]** をクリックします。
4. geo レプリケーションのブレードに移動します。ターゲット リージョンを選択します。 
5. [Create Secondary] のブレードに移動します。ターゲット リージョンから既存のサーバーを選択するか、新しく作成します。
6. セカンダリ タイプを選択します (*Readable* または *Non-readable*)。
7. **[作成]** をクリックして、構成を完了します。

> [AZURE.NOTE]geo レプリケーションのブレードでは、障害復旧のペアのリージョンが *[推奨]* とマークされます。Premium レベルのデータベースを使用する場合は、別のリージョンを選択できます。Standard のデータベースを使用している場合は、変更できません。Premium データベースでは、セカンダリ タイプが選択できます (*Readable* か *Non-readable*)。Standard データベースでは、*Non-readable* セカンダリのみ選択できます。


###PowerShell

[Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx) PowerShell コマンドレットを使用して geo レプリケーションの構成を自動化します。

Premium または Standard データベースの読み取りできないセカンダリで geo レプリケーションを作成するには:
		
		Start-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName" -PartnerServer "PartnerServerName" –ContinuousCopy -OfflineSecondary
Premium データベースの読み取り可能なセカンダリで geo レプリケーションを作成するには:

		Start-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName" -PartnerServer "PartnerServerName" –ContinuousCopy
		 
このコマンドは非同期になります。返された後に、[Get AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720235.aspx) コマンドレットを使用してこの操作の状態を確認します。返されたオブジェクトの ReplicationState フィールドには操作完了後に CATCH\_UP の値がつきます。

		Get-AzureSqlDatabaseCopy -ServerName "PrimaryServerName" -DatabaseName "PrimaryDatabaseName" -PartnerServer "SecondaryServerName"


###REST API 

[Start Database Copy](https://msdn.microsoft.com/library/azure/dn509576.aspx) API を使用して geo レプリケーションの構成をプログラムで作成します。

この API は非同期になります。返された後に、[Get Database Copy](https://msdn.microsoft.com/library/azure/dn509570.aspx) API を使用してこの操作の状態を確認します。応答本文の ReplicationState フィールドには、操作が完了すると CATCH\_UP の値がつきます。


##フェールオーバーの構成を選択する方法 

ビジネス継続性向けのアプリケーションを設計する際は、いくつかの構成オプションを検討する必要があります。アプリケーションのデプロイメント トポロジと、停止時に最も脆弱になるアプリケーションの部分によって選択肢が異なります。詳細は「[アクティブな geo レプリケーションを使用した災害復旧用のクラウド ソリューションの設計](https://msdn.microsoft.com/library/azure/dn741328.aspx)」をご覧ください。


 

<!---HONumber=September15_HO1-->