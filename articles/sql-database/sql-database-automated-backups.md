<properties
   pageTitle="SQL Database のバックアップについての説明 | Microsoft Azure" 
   description="SQL Database 組み込みデータベース バックアップについて説明します。この機能を使用すると、Azure SQL Database を以前の時点に復元したり、データベースを地理的リージョン内の新しいデータベースにコピーしたりすることができます (最大 35 日)。"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="carlrab"/>




<!-- # H1 Title

 H1 title should answer the question "What is in this topic?" Write the title in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  
-->

# <a name="learn-about-sql-database-backups"></a>SQL Database バックアップについての詳細情報

<!-- Introduction

1. Sentence #1 begins with "Learn about ..." and gives the scope of what the article will cover.
2. Sentence #2 explains the key capability or selling point of the feature.
3. Sentence #3 begins with "Use this feature to ..." and gives a common use case.

-->

SQL Database は 5 分おきにローカル データベースをバックアップし、Azure 読み取りアクセス Geo 冗長ストレージ (RA-GRS) を利用してデータベース バックアップの一部を別の地理的領域にコピーします。 バックアップは**自動的に行われ、追加料金は発生しません**。 ローカル データベースのバックアップを使用すると、同じサーバーの[ある時点にデータベースを復元](sql-database-point-in-time-restore-portal.md)できます。 Geo 冗長バックアップを使用すると、[異なる地理的領域にデータベースを復元できます](sql-database-geo-restore-portal.md)。  

>[AZURE.NOTE] ローカル バックアップと Geo 冗長バックアップはいずれも自動的に行われます。 ユーザー側の操作は必要ありません。追加料金は発生しません。 

次の図では、SQL Database が米国東部リージョンで実行されています。 5 分おきにデータベースが作成され、Azure 読み取りアクセス Geo 冗長ストレージ (RA-GRS) にローカル保存されます。 Azure により、データベース バックアップが米国西部リージョンのペアになるデータ センターにコピーされます。

![地理リストア](./media/sql-database-geo-restore/geo-restore-1.png)

<!--## What is <feature>?" -->

## <a name="what-is-a-sql-database-backup"></a>SQL Database バックアップとは何か。  

<!-- 
First sentence begins with "The <feature> is ..." followed by a definition of the feature. Provide a 1-2 paragraph intro to explain what the feature is, how it works, and the importance of the feature for solving business problems.
-->
SQL Database のバックアップは、特定の時点のデータベースの状態に関する情報を格納したファイルです。 SQL Database は SQL Server 技術を利用し、ローカルの[完全](https://msdn.microsoft.com/library/ms186289.aspx)バックアップ、[差分](https://msdn.microsoft.com/library/ms175526.aspx )バックアップ、[トランザクション ログ](https://msdn.microsoft.com/library/ms191429.aspx) バックアップを作成します。 トランザクション ログ バックアップは 5 分おきに行われます。データベースをホストする同じサーバーにポイントインタイム リストアを実行できます。 データベースを復元するとき、どのバックアップを復元する必要があるかをサービスが判定します (完全、差分、トランザクション ログ)。

>[AZURE.NOTE] SQL Database はローカル データベース バックアップと Geo 冗長バックアップの両方を自動的に作成します。 ユーザー側の操作は必要ありません。 追加料金は発生しません。

データベース バックアップを利用してできること:

- リテンション期間内の特定の時点にデータベースを復元します。 データベースのバックアップを使用すると、データベースを特定の時点に復元したり、削除されたデータベースを削除時の状態に復元したりできます。また、データベースを他の地理的リージョンに復元することもできます。 復元するには、「[データベース バックアップからデータベースを復元する](sql-database-recovery-using-backups.md)」を参照してください。

- 同じか別のリージョンにある SQL サーバーにデータベースをコピーします。 このコピーは、現在の SQL Database とトランザクション上の一貫性を保ちます。 コピーするには、「[データベース コピー](sql-database-copy.md)」を参照してください。

- バックアップのリテンション期間を超えてデータベース バックアップをアーカイブします。 アーカイブするには、[SQL Database を BACPAC ファイルにエクスポート](sql-database-export.md)します。 BACPAC を長期的なストレージにアーカイブし、リテンション期間を超えて保管できます。 または、BACPAC を使用して、オンプレミスまたは Azure の仮想マシン (VM) の SQL サーバーにデータベースのコピーを転送できます。

## <a name="backups-have-geographical-redundancy"></a>バックアップには地理的冗長性があります

SQL Database は [Azure Storage レプリケーション](../storage/storage-redundancy.md)を利用し、データベースを別の地理的場所にバックアップします。 Geo 冗長ストレージを提供するために、SQL Database は[読み取りアクセス Geo 冗長ストレージ (RA-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) アカウントにローカル データベース バックアップ ファイルを保存します。 Azure は、バックアップ ファイルを[ペアのデータ センター](../best-practices-availability-paired-regions.md)にレプリケートします。 この geo レプリケーションにより、プライマリ データベース リージョンからデータベースのバックアップにアクセスできない場合でもデータベースを復元できます。 

>[AZURE.NOTE] Azure Storage の " *レプリケーション* " という用語は、ある場所から別の場所にファイルをコピーすることを表します。 SQL の " *データベース レプリケーション* " は、複数のセカンダリ データベースとプライマリ データベースとの同期を保つことを意味します。 


## <a name="how-much-backup-storage-is-included-at-no-cost"></a>無償のバックアップ ストレージはどのくらい含まれますか。

SQL Database は、バックアップ ストレージとして、プロビジョニングされている最大のデータベース ストレージの 200% までを追加のコストなしで提供します。 たとえば、プロビジョニングされたデータベース サイズが 250 GB の Standard データベース インスタンスの場合、500 GB のバックアップ ストレージを追加のコストなしで利用できます。 データベースのサイズがプロビジョニングされたバックアップ ストレージを超える場合は、Azure サポートに連絡してリテンション期間を短縮できます。 もう 1 つのオプションとして、標準の読み取りアクセス geo 冗長ストレージ (RA-GRS) の料金で課金される追加のバックアップ ストレージに対して料金を支払うこともできます。 

## <a name="how-often-do-backups-happen"></a>バックアップはどのくらいの頻度で行われますか。

完全データベース バックアップが毎週作成され、差分データベース バックアップは 1 時間に 1 回作成されます。また、トランザクション ログのバックアップは 5 分間隔で実行されます。 初回の完全バックアップは、データベースの作成直後にスケジュールされます。 通常この操作は 30 分以内に終了しますが、データベースのサイズが大きい場合はそれ以上かかることがあります。 たとえば、復元されたデータベースまたはデータベースのコピーでは、初期バックアップに時間がかかります。 初回の完全バックアップ以降のバックアップは、すべて自動的にスケジュールされ、バックグラウンドで自動的に管理されます。 データベースの完全バックアップと[差分](https://msdn.microsoft.com/library/ms175526.aspx)バックアップの正確なタイミングは、全体的なシステムのワークロードのバランスを図りながら決定されます。 

## <a name="how-long-do-you-keep-my-backups"></a>バックアップはどのくらいの期間保存されますか。

各 SQL Database バックアップには、データベースの[サービス レベル](sql-database-service-tiers.md)に基づくリテンション期間が指定されます。 データベースのリテンション期間:

- Basic サービス レベルの場合、7 日間です。
- Standard サービス レベルの場合、35 日間です。
- Premium サービス レベルの場合、35 日間です。


Standard または Premium サービス レベルから Basic にデータベースをダウングレードした場合、バックアップは 7 日間保存されます。 7 日間を過ぎた場合、既存のバックアップはすべて利用できなくなります。 

Basic サービス レベルから Standard または Premium にデータベースをアップグレードした場合、SQL Database は 35 日間が経過するまで既存のバックアップを保持します。 新しいバックアップは 35 日間保持されます。
 
データベースを削除した場合、SQL Database はオンライン データベースの場合と同じようにバックアップを保持します。 たとえば、リテンション期間が 7 日間の Basic データベースを削除するとします。 3 日経過したバックアップはあと 4 日間保存されます。

> [AZURE.IMPORTANT] SQL Database をホストする Azure SQL サーバーを削除すると、サーバーに属するすべてのデータベースも削除され、復元できなくなります。 削除されたサーバーを復元することはできません。

## <a name="next-steps"></a>次のステップ

データの不慮の破損または削除から保護するデータベース バックアップは、ビジネス継続性および障害復旧戦略の最も重要な部分です。 データベース バックアップがビジネス戦略に適しているか確認するには、「[ビジネス継続性の概要](sql-database-business-continuity.md)」を参照してください。





<!--HONumber=Oct16_HO2-->


