<properties
   pageTitle="SQL Data Warehouse での Transparent Data Encryption (T-SQL) | Microsoft Azure"
   description="SQL Data Warehouse での Transparent Data Encryption (TDE) (T-SQL)"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# Transparent Data Encryption (TDE) の概要


> [AZURE.SELECTOR]
- [セキュリティの概要](sql-data-warehouse-overview-manage-security.md)
- [認証](sql-data-warehouse-authentication.md)
- [暗号化 (ポータル)](sql-data-warehouse-encryption-tde.md)
- [暗号化 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## 必要なアクセス許可

Transparent Data Encryption (TDE) を有効にするには、管理者か dbmanager ロールのメンバーである必要があります。

## 暗号化の有効化

SQL Data Warehouse の TDE を有効にするには、次の手順を実行します。

1. 管理者のログインまたは master データベースの **dbmanager** ロールのメンバーであるログインを使用して、データベースをホストしているサーバーの *master* データベースに接続します
2. データベースの暗号化するには、次のステートメントを実行します。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## 暗号化の無効化

SQL Data Warehouse の TDE を無効にするには、次の手順を実行します。

1. 管理者のログインまたは master データベースの **dbmanager** ロールのメンバーであるログインを使用して、*master* データベースに接続します
2. データベースの暗号化するには、次のステートメントを実行します。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [AZURE.NOTE] 一時停止した SQL Data Warehouse は、TDE 設定を変更する前に再開する必要があります。

## 暗号化の検証

SQL Data Warehouse の暗号化状態を確認するには、次の手順を実行します。

1. 管理者のログインまたは master データベースの **dbmanager** ロールのメンバーであるログインを使用して、*master* データベースまたはインスタンス データベースに接続します
2. データベースの暗号化するには、次のステートメントを実行します。

```sql
SELECT
	[name],
	[is_encrypted]
FROM
	sys.databases;
```

結果が ```1``` の場合はデータベースが暗号化されていることを示し、```0``` の場合は暗号化されていないことを示します。

## 暗号化の DMV  

- [sys.databases][]
- [sys.dm\_pdw\_nodes\_database\_encryption\_keys][]


<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm\_pdw\_nodes\_database\_encryption\_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_0928_2016-->