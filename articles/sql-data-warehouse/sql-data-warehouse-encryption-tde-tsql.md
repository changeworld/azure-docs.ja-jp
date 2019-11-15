---
title: 透過的なデータ暗号化 (T-SQL)
description: SQL Data Warehouse での透過的なデータ暗号化 (TDE) (T-SQL)
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 4e7f4f31cd8b899e3fcf79568ea62830313936b9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822608"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Transparent Data Encryption (TDE) の概要
> [!div class="op_single_selector"]
> * [セキュリティの概要](sql-data-warehouse-overview-manage-security.md)
> * [認証](sql-data-warehouse-authentication.md)
> * [暗号化 (ポータル)](sql-data-warehouse-encryption-tde.md)
> * [暗号化 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>必要なアクセス許可
Transparent Data Encryption (TDE) を有効にするには、管理者か dbmanager ロールのメンバーである必要があります。

## <a name="enabling-encryption"></a>暗号化の有効化
SQL Data Warehouse の TDE を有効にするには、次の手順を実行します。

1. 管理者のログインまたは master データベースの *dbmanager* ロールのメンバーであるログインを使用して、データベースをホストしているサーバーの **master** データベースに接続します
2. データベースの暗号化するには、次のステートメントを実行します。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>暗号化の無効化
SQL Data Warehouse の TDE を無効にするには、次の手順を実行します。

1. 管理者のログインまたは master データベースの *dbmanager* ロールのメンバーであるログインを使用して、 **master** データベースに接続します
2. データベースの暗号化するには、次のステートメントを実行します。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> 一時停止した SQL Data Warehouse は、TDE 設定を変更する前に再開する必要があります。
> 
> 

## <a name="verifying-encryption"></a>暗号化の検証
SQL Data Warehouse の暗号化状態を確認するには、次の手順を実行します。

1. 管理者のログインまたは master データベースの *dbmanager* ロールのメンバーであるログインを使用して、 **master** データベースまたはインスタンス データベースに接続します
2. データベースの暗号化するには、次のステートメントを実行します。

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

結果が ```1``` の場合はデータベースが暗号化されていることを示し、```0``` の場合は暗号化されていないことを示します。

## <a name="encryption-dmvs"></a>暗号化の DMV
* [sys.databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
