---
title: Azure SQL Database Managed Instance を使用したレプリケーション | Microsoft Docs
description: Azure SQL Database Managed Instance で SQL Server レプリケーションを使用する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 95c27bcc99f08cb1e4998e43a6a2abd508bee0ac
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228170"
---
# <a name="replication-with-sql-database-managed-instance"></a>SQL Database Managed Instance を使用したレプリケーション

[Azure SQL Database Managed Instance](sql-database-managed-instance.md) のパブリック プレビューでレプリケーションを使用できます。 マネージド インスタンスでは、パブリッシャー、ディストリビューター、サブスクライバーの各データベースをホストできます。

## <a name="common-configurations"></a>一般的な構成

一般に、パブリッシャーとディストリビューターは、どちらもクラウドに存在するか、オンプレミスに存在する必要があります。 次の構成がサポートされています。

- **マネージド インスタンス上のパブリッシャーとローカル ディストリビューター**

   ![Replication-with-azure-sql-db-single-managed-instance-publisher-distributor](./media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

   1 つのマネージド インスタンス上にパブリッシャー データベースとディストリビューター データベースが構成されています。

- **マネージド インスタンス上のパブリッシャーとリモート ディストリビューター**

   ![Replication-with-azure-sql-db-separate-managed-instances-publisher-distributor](./media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

   2 つのマネージド インスタンス上にパブリッシャーとディストリビューターが構成されています。 この構成では次のようになります。

  - 2 つのマネージド インスタンスが同じ vNet 内にあります。

  - 2 つのマネージド インスタンスが同じ場所にあります。

- **オンプレミスのパブリッシャーおよびディストリビューターとマネージド インスタンス上のサブスクライバー**

   ![Replication-from-on-premises-to-azure-sql-db-subscriber](./media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)

   この構成では、Azure SQL Database がサブスクライバーです。 この構成では、オンプレミスから Azure への移行がサポートされます。 サブスクライバーの役割では、SQL Database にマネージド インスタンスは不要ですが、オンプレミスから Azure への移行の一段階として SQL Database Managed Instance を使用できます。 Azure SQL Database サブスクライバーの詳細については、[SQL Database へのレプリケーション](replication-to-sql-database.md)に関する記事をご覧ください。

## <a name="requirements"></a>必要条件

Azure SQL Database 上のパブリッシャーとディストリビューターには、以下が必要です。

- Azure SQL Database Managed Instance。

   >[!NOTE]
   >マネージド インスタンスで構成されていない Azure SQL Database は、サブスクライバーとしてのみ使用できます。

- SQL Server のすべてのインスタンスが同じ vNet 上に存在する必要があります。

- 接続では、レプリケーション参加者間で SQL 認証を使用します。

- レプリケーション作業ディレクトリの Azure ストレージ アカウント共有。

## <a name="features"></a>機能

サポート:

- オンプレミスおよび Azure SQL Database Managed Instance インスタンスのトランザクション レプリケーションとスナップショット レプリケーションの組み合わせ。

- サブスクライバーには、オンプレミス、Azure SQL Database 内の単一データベース、または Azure SQL Database エラスティック プール内のプールされたデータベースを使用できます。

- 一方向または双方向のレプリケーション。

## <a name="configure-publishing-and-distribution-example"></a>パブリッシングとディストリビューションの構成例

1. ポータルで [Azure SQL Database Managed Instance を作成](http://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal)します。

1. 作業ディレクトリの [Azure ストレージ アカウントを作成](http://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account)します。 ストレージ キーを必ずコピーしてください。 [ストレージ アクセス キーの表示とコピー](http://docs.microsoft.com/azure/storage/common/storage-create-storage-account#manage-your-storage-access-keys)に関する記事をご覧ください。

1. パブリッシャーのデータベースを作成します。

   下記のサンプル スクリプトでは、`<Publishing_DB>` をこのデータベースの名前に置き換えます。

1. ディストリビューターの SQL 認証を使用するデータベース ユーザーを作成します。 「[データベース ユーザーの作成](http://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial#creating-database-users)」をご覧ください。 セキュリティで保護されたパスワードを使用します。

   下記のサンプル スクリプトでは、`<SQL_USER>` と `<PASSWORD>` にこの SQL Server アカウントのデータベース ユーザーとパスワードを使用します。

1. [SQL Database Managed Instance に接続](http://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms)します。

1. 次のクエリを実行して、ディストリビューターとディストリビューション データベースを追加します。

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

1. 指定したディストリビューション データベースを使用するようにパブリッシャーを構成するには、次のクエリを更新して実行します。

   `<SQL_USER>` と `<PASSWORD>` を、SQL Server アカウントとパスワードに置き換えます。

   `\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>` をストレージ アカウントの値に置き換えます。  

   `<STORAGE_CONNECTION_STRING>` を、Microsoft Azure ストレージ アカウントの **[アクセス キー]** タブの接続文字列に置き換えます。

   次のクエリを更新したら、クエリを実行します。 

   ```sql
   USE [master]
   EXEC sp_adddistpublisher @publisher = @@ServerName,
                @distribution_db = N'distribution',
                @security_mode = 0,
                @login = N'<SQL_USER>',
                @password = N'<PASSWORD>',
                @working_directory = N'\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>',
                @storage_connection_string = N'<STORAGE_CONNECTION_STRING>';
   GO
   ```

1. レプリケーションのパブリッシャーを構成します。 

    次のクエリで、`<Publishing_DB>` をパブリッシャー データベースの名前に置き換えます。

    `<Publication_Name>` をパブリケーションの名前に置き換えます。

    `<SQL_USER>` と `<PASSWORD>` を、SQL Server アカウントとパスワードに置き換えます。

    クエリを更新したら、クエリを実行してパブリケーションを作成します。

   ```sql
   USE [<Publishing_DB>]
   EXEC sp_replicationdboption @dbname = N'<Publishing_DB>',
                @optname = N'publish',
                @value = N'true';

   EXEC sp_addpublication @publication = N'<Publication_Name>',
                @status = N'active';

   EXEC sp_changelogreader_agent @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>';

   EXEC sp_addpublication_snapshot @publication = N'<Publication_Name>',
                @frequency_type = 1,
                @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   ```

1. アーティクル、サブスクリプション、プッシュ サブスクリプション エージェントを追加します。 

   これらのオブジェクトを追加するには、次のスクリプトを更新します。

   `<Object_Name>` をパブリケーション オブジェクトの名前に置き換えます。

   `<Object_Schema>` をソース スキーマの名前に置き換えます。 

   山かっこ (`<>`) 内のその他のパラメーターは、前のスクリプトの値と一致するように置き換えます。 

   ```sql
   EXEC sp_addarticle @publication = N'<Publication_Name>',
                @type = N'logbased',
                @article = N'<Object_Name>',
                @source_object = N'<Object_Name>',
                @source_owner = N'<Object_Schema>'

   EXEC sp_addsubscription @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @destination_db = N'<Subscribing_DB>',
                @subscription_type = N'Push'

   EXEC sp_addpushsubscription_agent @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @subscriber_db = N'<Subscribing_DB>',
                @subscriber_security_mode = 0,
                @subscriber_login = N'<SQL_USER>',
                @subscriber_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>', 
                @job_password = N'<PASSWORD>'
   GO
   ```

## <a name="limitations"></a>制限事項

次の機能はサポートされていません。

- 更新可能なサブスクリプション

- アクティブ geo レプリケーション

## <a name="see-also"></a>関連項目

- [マネージド インスタンスとは](http://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)
