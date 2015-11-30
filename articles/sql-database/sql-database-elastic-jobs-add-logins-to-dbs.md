<properties 
	pageTitle="エラスティック データベース プールにユーザーを追加する方法" 
	description="プール内の各 db に特権を持つユーザーを追加する必要があります。" 
	metaKeywords="azure sql database elastic databases credentials" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="ddove; sidneyh" />

# エラスティック データベース プールにユーザーを追加する方法

**エラスティック データベース ジョブ**機能 (プレビュー) を使用すると、Azure SQL Database で、カスタム定義のデータベース コレクション、**エラスティック データベース プール**、**エラスティック データベース シャード セット**など、データベースのグループ全体に対して Transact-SQL スクリプトを実行できます。スクリプトを実行するには、ジョブを実行する各データベースに適切なアクセス許可を持つユーザーを追加する必要があります。詳細については、「[Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)」か「[Adding Users to Your SQL Azure Database (SQL Azure Database にユーザーを追加する)](http://azure.microsoft.com/blog/2010/06/21/adding-users-to-your-sql-azure-database/)」をご覧ください。

## 前提条件
* [エラスティック ジョブ コンポーネント](sql-database-elastic-jobs-service-installation.md)をインストールします。 

## データベースにユーザーを追加する方法

1.	まず、エラスティック データベース プール内のデータベースが存在する Azure SQL Database サーバーの**マスター**に接続し、**エラスティック データベース ジョブ**をインストールしたときに提供されたものと同じ資格情報を使用して新しいログインを作成します。

		CREATE LOGIN login1 WITH password='<ProvidePassword>';

2. プール内の各データベースにログインし、同じ名前とパスワードを使用してユーザーを作成します。ユーザーには、ジョブを実行するための十分な権限が必要です。このコードは、各データベースで実行する必要があります。

		CREATE USER admin1 FROM LOGIN login1;
		
3. ユーザーには、ジョブに指定されたスクリプトを実行するための十分な権限が必要です。[ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx) を使用して、ユーザーにスクリプトを正常に実行するために必要な最低限の権限を提供します。

## 次のステップ

Azure ポータルでジョブの作成と管理を行うには、「[エラスティック データベース ジョブの作成と管理](sql-database-elastic-jobs-create-and-manage.md)」を参照してください。PowerShell を使用してジョブを作成するには、「[PowerShell を使用した SQL Database のエラスティック データベース ジョブの作成と管理 (プレビュー)](sql-database-elastic-jobs-powershell.md)」を参照してください。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=Nov15_HO4-->