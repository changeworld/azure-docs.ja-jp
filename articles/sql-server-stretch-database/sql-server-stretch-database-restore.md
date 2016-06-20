<properties
	pageTitle="Stretch 対応データベースを復元する | Microsoft Azure"
	description="Stretch が有効なデータベースを復元する方法について説明します。"
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
	ms.date="06/03/2016"
	ms.author="douglasl"/>

# Stretch 対応データベースを復元する

必要な場合に、バックアップされたデータベースを回復し、さまざまな種類のエラーや障害から復元します。

バックアップの詳細については、[Stretch 対応データベースのバックアップ](sql-server-stretch-database-backup.md)に関する記事を参照してください。

>   [AZURE.NOTE] バックアップは、高い可用性と事業継続性を実現するための完全ソリューションの一部に過ぎません。高可用性の詳細については、「[高可用性ソリューション](https://msdn.microsoft.com/library/ms190202.aspx)」を参照してください。

## SQL Server データを復元する
ハードウェアの障害または破損から復元するには、 Stretch 対応の SQL Server データベースをバックアップから復元します。現在お使いの SQL Server 復元方法を引き続き使用することもできます。詳細については、[復旧と復元の概要](https://msdn.microsoft.com/library/ms191253.aspx)に関する記事を参照してください。

SQL Server データベースを復元したら、ストアド プロシージャの **sys.sp\_rda\_reauthorize\_db** を実行し、Stretch 対応 SQL Server データベースと Azure リモート データベース間の接続を再確立する必要があります。詳細については、「[Restore the connection between the SQL Server database and the remote Azure database](#Restore-the-connection-between-the-SQL-Server-database-and-the-remote-Azure-database)」(SQL Server データベースと Azure リモート データベース間の接続を復元する)を参照してください。

## Azure リモート データを復元する

### Azure ライブ データベースを復元する
Azure の SQL Server Stretch Database サービスは、 Azure Storage Snapshots を使用して、少なくとも 8 時間ごとにすべてのライブ データのスナップショットを取得します。これらのスナップショットは、7 日間保持されます。これにより、最後のスナップショットが取得された時刻から過去 7 日間の少なくとも 21 の時点のデータを復元できます。

Azure ポータルを使用して、Azure ライブ データベースを前の時点の状態に復元する手順は、次の通りです。

1. Azure ポータルにログインします。
2. 画面の左側にある **[参照]** をクリックし、**[SQL データベース]** を選択します。
3. 目的のデータベースに移動し、それを選択します。
4. データベース ブレードの上部にある **[復元]** をクリックします。
5. 新しい**データベース名**を指定し、**[復元ポイント]** を選択して、**[作成]** をクリックします。
6. データベースの復元処理が開始され、**[通知]** を使用して処理を監視することができます。

### 削除された Azure データベースを復元する
Azure の SQL Server Stretch Database サービスは、データベースが削除される前にデータベース スナップショットを取得し、7 日間保持します。この場合、ライブ データベースからのスナップショットは保持されなくなります。これにより、削除済みのデータベースを削除された時点の状態に復元することができます。

Azure ポータルを使用して、削除済みの Azure データベースを削除された時点の状態に復元する手順は、次の通りです。

1. Azure ポータルにログインします。
2. 画面の左側にある **[参照]** をクリックし、**[SQL Server]** を選択します。
3. 目的のサーバーに移動して選択します。
4. サーバーのブレードで下にスクロールして [操作] を表示し、**[削除済みデータベース]** タイルをクリックします。
5. 復元する削除済みデータベースを選択します。
5. 新しい**データベース名**を指定し、**[作成]** をクリックします。
6. データベースの復元処理が開始され、**[通知]** を使用して処理を監視することができます。

### 別の Azure リージョンで Azure データベースを復元する  
Azure の SQL Server Stretch Database サービスは、リージョンに障害が発生した場合の回復性を高めるために、地理的に異なる Azure リージョンにスナップショットを非同期にコピーします。Azure リージョンの障害のためにデータベースにアクセスできない場合は、地理冗長化されたスナップショットのいずれかにデータベースを復元できます。

>   [AZURE.NOTE] 別の Azure リージョンで Azure データベースを復元するには、 復元後にクライアント アプリケーションの接続文字列を変更する必要がありますが、これは永続的なデータ損失につながる可能性があります。この種類の復元は、障害が長期間にわたる可能性が高い場合にのみ行うようにします。

Azure ポータルを使用して、別の Azure リージョンで Azure データベースを前の時点の状態に復元する手順は、次の通りです。

1. Azure ポータルにログインします。
2. 画面の左側にある、**[新規]** を選択し、次に **[データ + ストレージ]** を選択し、次に **[SQL Data Warehouse]** を選択します。
3. ソースとして **[バックアップ]** を選択し、復元する geo 冗長バックアップを選択します。
4. データベースのプロパティの残りの部分を指定して **[作成]** をクリックします。
5. データベースの復元処理が開始され、**[通知]** を使用して処理を監視することができます。

別のリージョンで Azure データベースを復元したら、ストアド プロシージャの **sys.sp\_rda\_deauthorize\_db** と **sys.sp\_rda\_reauthorize\_db**を実行し、Stretch 対応 SQL Server データベースと Azure リモート データベース間の接続を再確立する必要があります。詳細については、「[Restore the connection between the SQL Server database and the remote Azure database](#Restore-the-connection-between-the-SQL-Server-database-and-the-remote-Azure-database)」(SQL Server データベースと Azure リモート データベース間の接続を復元する)を参照してください。

## SQL Server データベースと Azure リモート データベース間の接続を復元する

1.  別の名前で、あるいは別のリージョンで復元された Azure データベースに接続する場合は、ストアド プロシージャ の [sys.sp\_rda\_deauthorize\_db](https://msdn.microsoft.com/library/mt703716.aspx) を実行して、以前の Azure データベースから切断します。  

2.  ストアド プロシージャ [sys.sp\_rda\_reauthorize\_db ](https://msdn.microsoft.com/library/mt131016.aspx) を実行し、Stretch を有効にしたローカル データベースを Azure データベースに再接続します。

	-   既存のデータベース スコープ資格情報を sysname または varchar(128) 値として指定します。(varchar(max) は使用しないでください。) 資格情報の名前は、**sys.database\_scoped\_credentials** ビューで調べることができます。  

	-   リモート データのコピーを作成して、そのコピーに接続するかを指定します (推奨)。

	```tsql  
	DECLARE @credentialName nvarchar(128);   
	SET @credentialName = N'<existing_database_scoped_credential_name>';   
	EXEC sp_rda_reauthorize_db @credential = @credentialName, @with_copy = 1;  

	```  

## 関連項目

[Stretch Database を管理し、問題を解決します。](sql-server-stretch-database-manage.md)

[sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[SQL Server データベースのバックアップと復元](https://msdn.microsoft.com/library/ms187048.aspx)

<!---HONumber=AcomDC_0608_2016-->