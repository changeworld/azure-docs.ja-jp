---
title: Azure SQL Database Managed Instance にバックアップを復元する | Microsoft Docs
description: SSMS を使用して Azure SQL Database マネージド インスタンスにデータベース バックアップを復元します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 6cad73c8b8fa6a2fa95a6ea0c1fdb5d4114ffd41
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47180101"
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Azure SQL Database マネージド インスタンスにデータベース バックアップを復元する

このクイック スタートでは、Wide World Importers - Standard バックアップ ファイルを使用して、Azure Blob Storage に格納されたデータベースのバックアップをマネージド インスタンスに復元する方法について説明します。 この方法には、ある程度のダウンタイムが必要です。 

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

Azure Database Migration Service (DMS) を使用して移行する方法のチュートリアルについては、[DMS を使用したマネージド インスタンスの移行](../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。 各種の移行方法の説明については、「[Azure SQL Database Managed Instance への SQL Server インスタンスの移行](sql-database-managed-instance-migrate.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このクイック スタート:
- クイック スタート: [マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)に関するページで作成されたリソースが出発点として使用されます。
- オンプレミスのクライアント コンピューターには、最新版の [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) をインストールしておく必要があります。
- SQL Server Management Studio を使ってマネージド インスタンスに接続できる必要があります。 接続のオプションについて、次のクイック スタートを参照してください。
  - [Connect to an Azure SQL Database Managed Instance from an Azure VM (Azure VM から Azure SQL Database Managed Instance に接続する)](sql-database-managed-instance-configure-vm.md)
  - [Connect to an Azure SQL Database Managed Instance from on-premises using a Point-to-Site connection (ポイント対サイト接続を使用してオンプレミスから Azure SQL Database Managed Instance に接続する)](sql-database-managed-instance-configure-p2s.md)
- Wide World Importers - 標準のバックアップ ファイル (https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak) からダウンロード) を含む事前構成済みの Azure BLOB ストレージ アカウントを使用します。

> [!NOTE]
> Azure BLOB ストレージと Shared Access Signature (SAS) を使用した SQL Server データベースのバックアップと復元の詳細については、「[SQL Server Backup to URL](sql-database-managed-instance-get-started-restore.md)」を参照してください。

## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>バックアップ ファイルから Wide World Importers データベースを復元する

SSMS で、以下の手順を使用して、バックアップ ファイルから Wide World Importers データベースをマネージド インスタンスに復元します。

1. SQL Server Management Studio (SSMS) を開き、マネージド インスタンスに接続します。
2. SSMS で、新しいクエリ ウィンドウを開きます。
3. マネージド インスタンスで事前構成済みのストレージ アカウントと SAS キーを使用して資格情報を作成するには、次のスクリプトを使用します。

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases] 
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D' 
   ```

    ![資格情報を作成する](./media/sql-database-managed-instance-get-started-restore/credential.png)

    > [!NOTE]
    > 生成された SAS キーからは、必ず先頭の **?** を削除するようにします 。
  
3. 次のスクリプトを使用して、SAS 資格情報とバックアップが有効であることを確認します。コンテナーの URL にバックアップ ファイルを指定します。

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![ファイル一覧](./media/sql-database-managed-instance-get-started-restore/file-list.png)

4. 次のスクリプトを使用して、バックアップ ファイルから Wide World Importers データベースを復元します。コンテナーの URL にバックアップ ファイルを指定します。

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![復元](./media/sql-database-managed-instance-get-started-restore/restore.png)

5. 復元の状態を追跡するには、新しいクエリ セッションで次のクエリを実行します。

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

6. 復元が完了したら、オブジェクト エクスプローラーで確認します。 

## <a name="next-steps"></a>次の手順

- Backup to URL に問題がある場合は、「 [SQL Server Backup to URL に関するベスト プラクティスとトラブルシューティング](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)」をご覧ください。
- アプリケーションの接続オプションの概要については、[マネージド インスタンスにアプリケーションを接続する](sql-database-managed-instance-connect-app.md)方法に関するページを参照してください。
- 任意のツールまたは言語を使用してクエリを実行する方法については、「[接続とクエリ](sql-database-connect-query.md)」を参照してください。
