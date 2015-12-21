<properties 
	pageTitle="SQL Data Warehouse Transparent Data Encryption (TDE) TSQL の概要| Microsoft Azure" 
	description="SQL Data Warehouse Transparent Data Encryption (TDE) TSQL の概要" 
	services="sql-data-warehouse" 
	documentationCenter="" 
	authors="twounder" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-data-warehouse" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/21/2015" 
	ms.author="twounder"/>
 
# Transparent Data Encryption (TDE) の概要
> [AZURE.SELECTOR]
- [Azure Classic Portal](sql-data-warehouse-encryption-tde.md)
- [TSQL](sql-data-warehouse-encryption-tde-tsql.md)

Azure SQL Data Warehouse の Transparent Data Encryption (TDE) を使用すると、データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、悪意のあるアクティビティの脅威からデータを保護できます。アプリケーションを変更する必要はありません。

TDE は、データベース暗号化キーと呼ばれる対称キーを使用してデータベース全体のストレージを暗号化します。SQL Database では、データベース暗号化キーは組み込まれているサーバー証明書によって保護されます。組み込みのサーバー証明書は、SQL Database サーバーごとに一意です。Microsoft は、少なくとも 90 日ごとにこれらの証明書を自動的にローテーションします。TDE の一般的な説明については、「[透過的なデータ暗号化 (TDE)]」を参照してください。

##暗号化の有効化

SQL Data Warehouse の TDE を有効にするには、次の手順を実行します。

1. 管理者のログインまたは master データベースの **dbmanager** ロールのメンバーであるログインを使用して、データベースをホストしているサーバーの *master* データベースに接続します
2. データベースの暗号化するには、次のステートメントを実行します。

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

##暗号化の無効化

SQL Data Warehouse の TDE を無効にするには、次の手順を実行します。

1. 管理者のログインまたは master データベースの **dbmanager** ロールのメンバーであるログインを使用して、*master* データベースに接続します
2. データベースの暗号化するには、次のステートメントを実行します。

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

##暗号化の検証

SQL Data Warehouse の暗号化状態を確認するには、次の手順を実行します。

1. 管理者のログインまたは master データベースの **dbmanager** ロールのメンバーであるログインを使用して、*master* データベースまたはインスタンス データベースに接続します
2. データベースの暗号化するには、次のステートメントを実行します。

```
SELECT
	[name],
	[is_encrypted]
FROM
	sys.databases;
```

結果が ```1``` の場合はデータベースが暗号化されていることを示し、```0``` の場合は暗号化されていないことを示します。

 
<!--Anchors-->
[透過的なデータ暗号化 (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_1210_2015-->