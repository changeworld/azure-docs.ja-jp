---
title: "Azure SQL Database のコピーの作成 | Microsoft Docs"
description: "Azure SQL Database のコピーの作成"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 5aaf6bcd-3839-49b5-8c77-cbdf786e359b
ms.service: sql-database
ms.custom: move data
ms.devlang: NA
ms.date: 04/05/2017
ms.author: sashan;carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: 2781eacff6eea456f7f22b73bbd2544015b9af32
ms.lasthandoff: 04/12/2017


---
# <a name="copy-an-azure-sql-database"></a>Azure SQL Database のコピー

Azure SQL Database では、同じサーバーまたは別のサーバーのいずれかに、トランザクション上一貫性のある、既存の Azure SQL Database のコピーを作成するための複数の方法が用意されています。 Azure Portal、PowerShell、または T-SQL を使用して、SQL データベースを作成できます。 

## <a name="overview"></a>概要

データベースのコピーとは、コピーが要求された時点でのソース データベースのスナップショットのことです。 同じサーバーまたは別のサーバー、そのサービス層とパフォーマンス レベル、別のパフォーマンス レベルを同じサービス層 (エディション) 内で選択できます。 コピーの完了後、コピーは完全に機能する独立したデータベースになります。 この時点では、任意のエディションにアップグレードまたはダウン グレードできます。 ログイン、ユーザー、アクセス許可は非依存で管理できます。  

## <a name="logins-in-the-database-copy"></a>データベースのコピーへのログイン

データベースを同じ論理サーバーにコピーすると、両方のデータベースで同じログインを利用できます。 データベースをコピーするために使用するセキュリティ プリンシパルが、新しいデータベースのデータベース所有者 (DBO) になります。 すべてのデータベース ユーザー、アクセス許可、セキュリティ識別子 (SID) がデータベースのコピーにコピーされます。  

別の論理サーバーにデータベースをコピーする場合は、新しいサーバーのセキュリティ プリンシパルが新しいデータベースのデータベース所有者になります。 データのアクセスに[包含データベース ユーザー](sql-database-manage-logins.md)を使用する場合は、プライマリとセカンダリの両方のデータベースが常に同じユーザー資格情報を持つようにします。そうすることで、コピーの完了後すぐに同じ資格情報でアクセスできるようになります。 

[Azure Active Directory](../active-directory/active-directory-whatis.md) を使用すると、コピーで資格情報を管理する必要がなくなります。 ただし、新しいサーバーにデータベースをコピーする場合、ログインに基づくアクセスは動作しないことがあります。これは、そのログインが新しいサーバーに存在しないためです。 別の論理サーバーにデータベースをコピーする場合のログイン管理の詳細については、「[障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md)」をご覧ください。 

コピーが成功した後、他のユーザーが再マップされるまでは、コピーを開始したログイン、つまりデータベース所有者 (DBO) のみが新しいデータベースにログオンできます。 コピー操作が完了した後にログインを解決するには、 [ログインの解決](sql-database-copy.md#resolve-logins.md)

## <a name="database-copy-using-the-azure-portal"></a>Azure Portal を使用したデータベースのコピー

Azure Portal を使用してデータベースをコピーするには、データベースのページを開き、ツールバーの [**コピー**] をクリックします。 

   ![データベースのコピー](./media/sql-database-copy/database-copy.png)

## <a name="database-copy-using-powershell"></a>PowerShell を使用したデータベースのコピー

PowerShell を使用してデータベースをコピーするには、[New-AzureRmSqlDatabaseCopy](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabasecopy) コマンドレットを使用します。 

```PowerShell
New-AzureRmSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

完全なサンプル スクリプトについては、「[Copy a database to a new server](scripts/sql-database-copy-database-to-new-server-powershell.md)」 (データベースを新しいサーバーにコピーする) を参照してください。

## <a name="database-copy-using-transact-sql"></a>Transact-SQL を使用したデータベースのコピー

サーバーレベル プリンシパル ログインか、コピーするデータベースを作成したときのログインを使用して、マスター データベースにログオンします。 サーバーレベル プリンシパルではないログインでデータベースをコピーするには、dbmanager ロールのメンバーである必要があります。 ログインおよびサーバーへの接続の詳細については、 [ログインの管理](sql-database-manage-logins.md)に関するページを参照してください。

[CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) ステートメントを使用して、コピー元データベースのコピーを開始します。 このステートメントを実行すると、データベースのコピー処理が始まります。 データベースのコピーは非同期的な処理であるため、CREATE DATABASE ステートメントは、データベースのコピーが完了する前に復帰します。

### <a name="copy-a-sql-database-to-the-same-server"></a>同じサーバーへの SQL データベースのコピー
サーバーレベル プリンシパル ログインか、コピーするデータベースを作成したときのログインを使用して、マスター データベースにログオンします。 サーバーレベル プリンシパルではないログインでデータベースをコピーするには、dbmanager ロールのメンバーである必要があります。

このコマンドは、Database1 を、同じサーバー上の Database2 という名前の新しいデータベースにコピーします。 データベースのサイズに応じて、コピー操作の完了に時間がかかる場合があります。

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>別のサーバーへの SQL データベースのコピー

コピー先サーバー (新しいデータベースを作成する Azure SQL Database サーバー) のマスター データベースにログオンします。 コピー元の Azure SQL Database サーバー上のコピー元データベースのデータベース所有者 (DBO) と同じ名前とパスワードを持つログインを使用します。 コピー先サーバーでのログインも、dbmanager ロールのメンバーであるか、サーバーレベル プリンシパル ログインである必要があります。

このコマンドは、server1 上の Database1 を、server2 上の Database2 という新しいデータベースにコピーします。 データベースのサイズに応じて、コピー操作の完了に時間がかかる場合があります。

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;


### <a name="monitor-the-progress-of-the-copy-operation"></a>コピー操作の進行状況の監視

sys.databases ビューと sys.dm_database_copies ビューを照会して、コピー操作を監視します。 コピー操作の進行中は、新しいデータベースの sys.databases ビューの state_desc 列が COPYING に設定されます。

* コピー操作に失敗すると、新しいデータベースの sys.databases ビューの state_desc 列が SUSPECT に設定されます。 この場合は、新しいデータベースに対して DROP ステートメントを実行した後でもう一度やり直してください。
* コピー操作に成功すると、新しいデータベースの sys.databases ビューの state_desc 列が ONLINE に設定されます。 この場合、コピー操作は完了しています。新しいデータベースは通常のデータベースであり、コピー元データベースから独立して変更することができます。

> [!NOTE]
> * 進行中のコピー操作を取り消すには、新しいデータベースに対して [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) ステートメントを実行します。 また、コピー元のデータベースに対して DROP DATABASE ステートメントを実行することによっても、コピー操作を取り消すことができます。
> 

## <a name="resolve-logins"></a>ログインの解決

新しいデータベースがコピー先サーバーでオンラインになった後、 [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) ステートメントを使用して、ユーザーを新しいデータベースからコピー先サーバーのログインに再マップします。 孤立したユーザーを解決するには、「 [孤立ユーザーのトラブルシューティング](https://msdn.microsoft.com/library/ms175475.aspx)」をご覧ください。 「 [障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md)」もご覧ください。

新しいデータベースのすべてのユーザーは、コピー元データベースで保持していたアクセス許可を保持します。 データベースのコピーを開始したユーザーが新しいデータベースのデータベース所有者になり、新しいセキュリティ識別子 (SID) が割り当てられます。 コピーが成功した後、他のユーザーが再マップされるまでは、コピーを開始したログイン、つまりデータベース所有者 (DBO) のみが新しいデータベースにログオンできます。

別の論理サーバーにデータベースをコピーする場合のユーザーおよびログインの管理の詳細については、「[障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md)」も参照してください。

## <a name="next-steps"></a>次のステップ

* ログインについては、「[ログインの管理](sql-database-manage-logins.md)」と「[障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md)」を参照してください。
* データベースをエクスポートするには、「[BACPAC へのデータベースのエクスポート](sql-database-export.md)」を参照してください。

