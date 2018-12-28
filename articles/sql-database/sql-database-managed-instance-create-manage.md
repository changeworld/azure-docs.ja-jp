---
title: Azure SQL Database Managed Instance の管理 API リファレンス | Microsoft Docs
description: Azure SQL Database Managed Instance の作成と管理について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/12/2018
ms.openlocfilehash: 7fb6917e129c015536143a707fd2a89fc5423a99
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323082"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Azure SQL Database Managed Instance のマネージド API リファレンス

Azure portal、PowerShell、Azure CLI、REST API、および Transact-SQL を使用して、Azure SQL Database Managed Instance を作成および管理することができます。 この記事では、マネージド インスタンスの作成と構成に使用できる関数と API の概要を説明します。

## <a name="azure-portal-create-a-managed-instance"></a>Azure portal:マネージド インスタンスを作成する

Azure SQL Database Managed Instance の作成方法を示したクイックスタートについては、「[クイック スタート:Azure SQL Database マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)」を参照してください。

## <a name="powershell-create-and-manage-a-managed-instance"></a>PowerShell:マネージド インスタンスを作成して管理する

Azure PowerShell を利用して Azure SQL のサーバー、データベース、ファイアウォールを作成し、管理するには、次の PowerShell コマンドレットを使用します。 PowerShell をインストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

> [!TIP]
> PowerShell サンプル スクリプトについては、「[Quick-start script:Create Azure SQL Managed Instance using PowerShell library](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)」 (クイックスタート スクリプト: PowerShell ライブラリを使用した Azure SQL Managed Instance の作成) を参照してください。

| コマンドレット | 説明 |
| --- | --- |
|[New-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlinstance)|Azure SQL Database Managed Instance を作成します |
|[Get-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Get-AzureRmSqlInstance)|Azure SQL Managed Instance に関する情報を返します|
|[Set-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Set-AzureRmSqlInstance)|Azure SQL Database Managed Instance のプロパティを設定します|
|[Remove-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Remove-AzureRmSqlInstance)|Azure SQL Database Managed Instance を削除します|

## <a name="azure-cli-manage-logical-servers-and-databases"></a>Azure CLI:論理サーバーとデータベースを管理する

[Azure CLI](/cli/azure) を利用して Azure SQL のサーバー、データベース、ファイアウォールを作成し、管理するには、次の [Azure CLI SQL Managed Instance](/cli/azure/sql/mi) コマンドを使用します。 [Cloud Shell](/azure/cloud-shell/overview) を使用して CLI をブラウザーで実行することも、macOS、Linux、または Windows に[インストール](/cli/azure/install-azure-cli)することもできます。

> [!TIP]
> Azure CLI のクイックスタートについては、「[Working with SQL Managed Instance using Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)」 (Azure CLI を使用した SQL Managed Instance の操作) を参照してください。
>

| コマンドレット | 説明 |
| --- | --- |
|[az sql mi create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-mi-create) |マネージド インスタンスを作成します|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/db#az-sql-mi-list)|使用可能なマネージド インスタンスを一覧表示します|
|[az sql mi show](/cli/azure/sql/db#az-sql-mi-show)|マネージド インスタンスの詳細を取得します|
|[az sql mi update](/cli/azure/sql/db#az-sql-mi-update)|マネージド インスタンスを更新します|
|[az sql mi delete](/cli/azure/sql/db#az-sql-mi-delete)|マネージド インスタンスを削除します|

## <a name="transact-sql-manage-logical-servers-and-databases"></a>Transact-SQL:論理サーバーとデータベースを管理する

マネージド インスタンスの作成後、Azure SQL Database Managed Instance データベースを作成し、管理するには、次の T-SQL コマンドを使用します。 これらのコマンドを発行するには、Azure portal、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is)、 [Visual Studio Code](https://code.visualstudio.com/docs)、または Azure SQL Database サーバーに接続し、Transact-SQL コマンドを渡すことができるその他の任意のプログラムを使用します。

> [!TIP]
> Microsoft Windows で SQL Server Management Studio を使用して、マネージド インスタンスを構成して接続する必要があることを示すクイック スタートについては、「[クイック スタート:Azure SQL Database Managed Instance に接続するように Azure VM を構成する](sql-database-managed-instance-configure-vm.md)」および「[クイック スタート:オンプレミスから Azure SQL Database Managed Instance へのポイント対サイト接続を構成する](sql-database-managed-instance-configure-p2s.md)」を参照してください。
> [!IMPORTANT]
> Transact-SQL を使用して、マネージド インスタンスを作成または削除することはできません。

| コマンド | 説明 |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|新しいマネージド インスタンス データベースを作成します。 新しいデータベースを作成するには、マスター データベースに接続する必要があります。|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Azure SQL Database Managed Instance データベースを変更します。|

## <a name="rest-api-manage-logical-servers-and-databases"></a>REST API:論理サーバーとデータベースを管理する

Azure SQL Database Managed Instance を作成して管理するには、以下の REST API 要求を使用します。

| コマンド | 説明 |
| --- | --- |
|[Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|マネージド インスタンスを作成または更新します。|
|[Managed Instances - Delete](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|マネージド インスタンスを削除します。|
|[Managed Instances - Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|マネージド インスタンスを取得します。|
|[Managed Instances - List](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|サブスクリプション内のマネージド インスタンスの一覧を返します。|
|[Managed Instances - List By Resource Group](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|リソース グループ内のマネージド インスタンスの一覧を取得します。|
|[Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|マネージド インスタンスを更新します。|

## <a name="next-steps"></a>次の手順

- SQL Server データベースを Azure に移行する方法については、「[Azure SQL Database に移行](sql-database-cloud-migrate.md)」を参照してください。
- サポートされている機能については、[機能](sql-database-features.md)に関する記事をご覧ください。
