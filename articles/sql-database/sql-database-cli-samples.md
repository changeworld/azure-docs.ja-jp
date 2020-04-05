---
title: Azure CLI スクリプトの例
description: Azure SQL Database サーバー、エラスティック プール、データベース、ファイアウォールを作成し、管理するための Azure CLI スクリプト サンプル。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 459a5ea69e11a8614c572f68fce039b6cabbb1ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061716"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Azure SQL Database 用の Azure CLI サンプル

Azure SQL Database は、<a href="/cli/azure">Azure CLI</a> を使用して構成できます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

# <a name="single-database--elastic-pools"></a>[単一データベースとエラスティック プール](#tab/single-database)

| | |
|---|---|
|**単一のデータベースとエラスティック プールを作成する**||
| [単一のデータベースを作成し、ファイアウォール規則を構成する](scripts/sql-database-create-and-configure-database-cli.md) | Azure SQL データベースを作成し、サーバーレベルのファイアウォール規則を構成します。 |
| [エラスティック プールを作成し、プールされたデータベースを移動する](scripts/sql-database-move-database-between-pools-cli.md) | SQL エラスティック プールを作成し、プールされた Azure SQL データベースを移動して、コンピューティング サイズを変更します。 |
|**単一のデータベースとエラスティック プールをスケーリングする**||
| [単一のデータベースをスケーリングする](scripts/sql-database-monitor-and-scale-database-cli.md) | データベースのサイズ情報の照会後、Azure SQL データベースを別のコンピューティング サイズにスケーリングします。 |
| [エラスティック プールをスケーリングする](scripts/sql-database-scale-pool-cli.md) | SQL エラスティック プールを別のコンピューティング サイズにスケーリングします。 |
|**geo レプリケーションを構成しフェールオーバーする**||
| [フェールオーバー グループへの単一データベースの追加](scripts/sql-database-add-single-db-to-failover-group-cli.md)| データベースとフェールオーバー グループを作成し、データベースをフェールオーバー グループに追加して、セカンダリ サーバーへのフェールオーバーをテストします。 |
| [エラスティック プールに対してフェールオーバー グループを構成する](scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | データベースを作成し、それをエラスティック プールに追加し、そのエラスティック プールをフェールオーバー グループに追加して、セカンダリ サーバーへのフェールオーバーをテストします。 |
| [アクティブ geo レプリケーションを使用して、単一のデータベースを構成およびフェールオーバーする](scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Azure SQL データベースのアクティブ geo レプリケーションを構成し、セカンダリ レプリカにフェールオーバーします。 |
| [アクティブ geo レプリケーションを使用して、プールされたデータベースを構成およびフェールオーバーする](scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| SQL エラスティック プール内の Azure SQL データベース用にアクティブ geo レプリケーションを構成し、そのデータベースをセカンダリ レプリカにフェールオーバーします。 |
| **監査と脅威の検出** |
| [監査と脅威検出を構成する](scripts/sql-database-auditing-and-threat-detection-cli.md)| Azure SQL データベースの監査と脅威検出ポリシーを構成します。 |
| **データベースをバックアップ、復元、コピー、インポートする**||
| [データベースをバックアップする](scripts/sql-database-backup-database-cli.md)| Azure Storage バックアップに Azure SQL データベースをバックアップします。 |
| [データベースを復元する](scripts/sql-database-restore-database-cli.md)| geo 冗長バックアップから Azure SQL データベースを復元し、削除された Azure SQL データベースを最新のバックアップに復元します。 |
| [新しいサーバーにデータベースをコピーする](scripts/sql-database-copy-database-to-new-server-cli.md) | 新しい Azure SQL サーバーに既存の Azure SQL データベースのコピーを作成します。 |
| [データベースを bacpac ファイルからインポートする](scripts/sql-database-import-from-bacpac-cli.md)| *.bacpac* ファイルから Azure SQL サーバーにデータベースをインポートします。 |
|||

[単一データベースの Azure CLI API](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) に関するセクションを参照してください。

# <a name="managed-instance"></a>[Managed Instance](#tab/managed-instance)

次の表には、Azure SQL Database Managed Instance の Azure CLI スクリプト例へのリンクが含まれています。

| | |
|---|---|
| **マネージド インスタンスを作成する**||
| [マネージド インスタンスを作成する](scripts/sql-database-create-configure-managed-instance-cli.md)| マネージド インスタンスを作成します。 |
| **透過的なデータ暗号化 (TDE) の構成**||
| [Azure Key Vault を使用して Managed Instance で Transparent Data Encryption を管理する](scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Azure SQL Managed Instance で、Azure Key Vault とさまざまなキーのシナリオを使用して Transparent Data Encryption (TDE) を構成します。 |
|**フェールオーバー グループを構成する**||
| [マネージド インスタンスのフェールオーバー グループを構成する](scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | 2 つのマネージド インスタンスを作成し、それらをフェールオーバー グループに追加して、プライマリ マネージド インスタンスからセカンダリ マネージド インスタンスへのフェールオーバーをテストします。 |
|||

その他の Managed Instance サンプルについては、データベースの[作成](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/)、[更新](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/)、[移動](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)、[操作](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)に関するスクリプトを参照してください。

[マネージド インスタンスの Azure CLI API](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances) について理解を深めましょう。

---
